---
title: 将 Kubernetes 添加到 Azure Stack 市场 | Microsoft Docs
description: 了解如何将 Kubernetes 添加到 Azure Stack 市场。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 09fa7b503f0c594d2af0c6f16a6d4618cec0fac3
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64294429"
---
# <a name="add-kubernetes-to-the-azure-stack-marketplace"></a>将 Kubernetes 添加到 Azure Stack 市场

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!note]  
> Azure Stack 上的 Kubernetes 现为预览版。 在预览目前不支持 azure Stack 离线的场景中。

可以将 Kubernetes 作为市场项提供给用户。 然后，用户可以将 Kubernetes 部署在单个协调的操作。

下面的文章着眼于使用 Azure 资源管理器模板为独立的 Kubernetes 群集部署和预配资源。 在开始之前，请检查 Azure Stack 和全球 Azure 租户设置。 收集关于 Azure Stack 的必需信息。 将所需资源添加到租户和 Azure Stack 市场。 群集取决于 Ubuntu 服务器、 自定义脚本，以及要在 marketplace 中的 Kubernetes 群集 Marketplace 项。

## <a name="create-a-plan-an-offer-and-a-subscription"></a>创建计划、套餐和订阅

为 Kubernetes 市场项创建计划、套餐和订阅。 也可以使用现有计划和套餐。

1. 登录到[管理门户](https://adminportal.local.azurestack.external)。

1. 创建一个计划作为基本计划。 有关说明，请参阅[在 Azure Stack 中创建计划](azure-stack-create-plan.md)。

1. 创建套餐。 有关说明，请参阅[在 Azure Stack 中创建套餐](azure-stack-create-offer.md)。

1. 选择“套餐”，并找到你创建的套餐。

1. 选择“套餐”边栏选项卡中的“概述”。

1. 选择“更改状态”。 选择“公共”。

1. 选择 **+ 创建资源** > **提供了和计划** > **订阅**创建订阅。

    a. 输入**显示名称**。

    b. 输入**用户**。 请使用与你的租户关联的 Azure AD 帐户。

    c. **提供商说明**

    d. 将“目录租户”设置为你的 Azure Stack 的 Azure AD 租户。 

    e. 选择“套餐”。 选择你创建的套餐的名称。 记下订阅 ID。

## <a name="create-a-service-principal-and-credentials-in-ad-fs"></a>在 AD FS 中创建服务主体和凭据

如果您使用 Active Directory 联合身份验证服务 (AD FS) 标识管理服务，需要创建服务主体的用户部署 Kubernetes 群集。

1. 创建和导出用来创建服务主体的自签名的证书。 

    - 需要具有以下几部分信息：

       | 值 | 描述 |
       | ---   | ---         |
       | 密码 | 为证书输入新密码。 |
       | 本地证书路径 | 输入证书的路径和文件名的名称。 例如： `c:\certfilename.pfx` |
       | 证书名称 | 输入证书的名称。 |
       | 证书存储位置 |  例如： `Cert:\LocalMachine\My` |

    - 使用提升的提示符打开 PowerShell。 使用更新为你的值的参数运行以下脚本：

        ```powershell  
        # Creates a new self signed certificate 
        $passwordString = "<password>"
        $certlocation = "<local certificate path>.pfx"
        $certificateName = "CN=<certificate name>"
        $certStoreLocation="<certificate store location>"
        
        $params = @{
        CertStoreLocation = $certStoreLocation
        DnsName = $certificateName
        FriendlyName = $certificateName
        KeyLength = 2048
        KeyUsageProperty = 'All'
        KeyExportPolicy = 'Exportable'
        Provider = 'Microsoft Enhanced Cryptographic Provider v1.0'
        HashAlgorithm = 'SHA256'
        }
        
        $cert = New-SelfSignedCertificate @params -ErrorAction Stop
        Write-Verbose "Generated new certificate '$($cert.Subject)' ($($cert.Thumbprint))." -Verbose
        
        #Exports certificate with password in a .pfx format
        $pwd = ConvertTo-SecureString -String $passwordString -Force -AsPlainText
        Export-PfxCertificate -cert $cert -FilePath $certlocation -Password $pwd
        ```

2.  请记下显示在 PowerShell 会话中，新的证书 ID `1C2ED76081405F14747DC3B5F76BB1D83227D824`。 创建服务主体时，将使用 ID。

    ```powershell  
    VERBOSE: Generated new certificate 'CN=<certificate name>' (1C2ED76081405F14747DC3B5F76BB1D83227D824).
    ```

3. 创建服务主体使用的证书。

    - 需要具有以下几部分信息：

       | 值 | 描述                     |
       | ---   | ---                             |
       | ERCS IP | 在 ASDK 中，特权终结点通常是`AzS-ERCS01`。 |
       | 应用程序名称 | 输入应用程序服务主体的简单名称。 |
       | 证书存储位置 | 证书存储在计算机上的路径。 这将由存储位置并在第一步中生成的证书 ID。 例如： `Cert:\LocalMachine\My\1C2ED76081405F14747DC3B5F76BB1D83227D824` |

       出现提示时，使用以下凭据连接到特权终结点。 
        - 用户名：格式指定 CloudAdmin 帐户， `<Azure Stack domain>\cloudadmin`。 （对于 ASDK，用户名是 azurestack\cloudadmin。）
        - 密码：输入安装 AzureStackAdmin 域管理员帐户期间提供的相同密码。

    - 使用更新为你的值的参数运行以下脚本：

        ```powershell  
        #Create service principal using the certificate
        $privilegedendpoint="<ERCS IP>"
        $applicationName="<application name>"
        $certStoreLocation="<certificate location>"
        
        # Get certificate information
        $cert = Get-Item $certStoreLocation
        
        # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
        $creds = Get-Credential

        # Creating a PSSession to the ERCS PrivilegedEndpoint
        $session = New-PSSession -ComputerName $privilegedendpoint -ConfigurationName PrivilegedEndpoint -Credential $creds

        # Get Service principal Information
        $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name "$using:applicationName" -ClientCertificates $using:cert}

        # Get Stamp information
        $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }

        # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
        $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

        # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
        $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

        # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
        $TenantID = $AzureStackInfo.AADTenantID

        # Register an AzureRM environment that targets your Azure Stack instance
        Add-AzureRMEnvironment `
        -Name "AzureStackUser" `
        -ArmEndpoint $ArmEndpoint

        # Set the GraphEndpointResourceId value
        Set-AzureRmEnvironment `
        -Name "AzureStackUser" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true
        Add-AzureRmAccount -EnvironmentName "azurestackuser" `
        -ServicePrincipal `
        -CertificateThumbprint $ServicePrincipal.Thumbprint `
        -ApplicationId $ServicePrincipal.ClientId `
        -TenantId $TenantID

        # Output the SPN details
        $ServicePrincipal
        ```

    - 如下面的代码段所示的服务主体详细信息

        ```Text  
        ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
        ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
        Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
        ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
        PSComputerName        : azs-ercs01
        RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
        ```

## <a name="add-an-ubuntu-server-image"></a>添加 Ubuntu 服务器映像

将以下 Ubuntu 服务器映像添加到市场：

1. 登录到[管理门户](https://adminportal.local.azurestack.external)。

1. 选择“所有服务”，然后在“管理”类别下，选择“市场管理”。

1. 选择“+ 从 Azure 添加”。

1. 输入 `Ubuntu Server` 。

1. 选择最新版本的服务器。 检查完整版本并确保已安装最新版本：
    - **发布者**：Canonical
    - **产品/服务**：UbuntuServer
    - **版本**：16.04.201806120 （或最新版本）
    - **SKU**：16.04-LTS

1. 选择“下载”。

## <a name="add-a-custom-script-for-linux"></a>添加适用于 Linux 的自定义脚本

从市场中添加 Kubernetes：

1. 打开[管理门户](https://adminportal.local.azurestack.external)。

1. 选择“所有服务”，然后在“管理”类别下，选择“市场管理”。

1. 选择“+ 从 Azure 添加”。

1. 输入 `Custom Script for Linux` 。

1. 选择具有以下配置文件的服务器：
   - **产品/服务**：适用于 Linux 的自定义脚本 2.0
   - **版本**：2.0.6 （或最新版本）
   - **发布者**：Microsoft Corp

     > [!Note]  
     > 可能会列出适用于 Linux 的自定义脚本的多个版本。 你将需要添加的项的最后一个版本。

1. 选择“下载”。


## <a name="add-kubernetes-to-the-marketplace"></a>将 Kubernetes 添加到市场

1. 打开[管理门户](https://adminportal.local.azurestack.external)。

1. 选择“所有服务”，然后在“管理”类别下，选择“市场管理”。

1. 选择“+ 从 Azure 添加”。

1. 输入 `Kubernetes` 。

1. 选择 `Kubernetes Cluster`。

1. 选择“下载”。

    > [!note]  
    > 市场项可能需要花费五分钟才会出现在市场中。

    ![Kubernetes](../user/media/azure-stack-solution-template-kubernetes-deploy/marketplaceitem.png)

## <a name="update-or-remove-the-kubernetes"></a>更新或删除 Kubernetes 

当更新 Kubernetes 项，则会在 Marketplace 中删除上一项。 按照这篇文章中的说明将添加到 marketplace 更新 Kubernetes。

若要删除 Kubernetes 项，请执行以下操作：

1. 以操作员身份使用 PowerShell 连接到 Azure Stack。 有关说明，请参阅[以操作员身份使用 PowerShell 连接到 Azure Stack](azure-stack-powershell-configure-admin.md)。

2. 在库中查找当前的 Kubernetes 群集项。

    ```powershell  
    Get-AzsGalleryItem | Select Name
    ```
    
3. 记下当前项的名称，例如 `Microsoft.AzureStackKubernetesCluster.0.3.0`

4. 使用以下 PowerShell cmdlet 删除项：

    ```powershell  
    $Itemname="Microsoft.AzureStackKubernetesCluster.0.3.0"

    Remove-AzsGalleryItem -Name $Itemname
    ```

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack](../user/azure-stack-solution-template-kubernetes-deploy.md)

[在 Azure Stack 中提供服务概述](azure-stack-offer-services-overview.md)
