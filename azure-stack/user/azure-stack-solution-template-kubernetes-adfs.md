---
title: 将 Kubernetes 部署到 Azure Stack 使用 Active Directory 联合身份验证服务 (AD FS) |Microsoft Docs
description: 了解如何将 Kubernetes 部署到 Azure Stack 使用 Active Directory 联合身份验证服务 (AD FS)。
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
ms.date: 02/11/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: 367053882e9e44ba983d5df552929c65222fcafc
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66197353"
---
# <a name="deploy-kubernetes-to-azure-stack-using-active-directory-federated-services"></a>将 Kubernetes 部署到 Azure Stack 使用 Active Directory 联合身份验证服务

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!Note]  
> Azure Stack 上的 Kubernetes 现为预览版。 在预览目前不支持 azure Stack 离线的场景中。

可以按照这篇文章来部署和设置 Kubernetes 资源中的步骤。 Active Directory 联合身份验证服务 (AD FS) 是标识管理服务，请执行以下步骤。

## <a name="prerequisites"></a>必备组件 

开始之前，请确保你有适当的权限且 Azure Stack 已就绪。

1. 生成一个 SSH 公钥和私钥对，用于登录到 Azure Stack 上的 Linux VM。 创建群集时需要的公钥。

    有关如何生成密钥的说明，请参阅 [SSH 密钥生成](https://github.com/msazurestackworkloads/acs-engine/blob/master/docs/ssh.md#ssh-key-generation)。

1. 检查你在 Azure Stack 租户门户中是否有有效的订阅，以及是否有足够的公共 IP 地址来添加新的应用程序。

    此群集不能部署到 Azure Stack **管理员**订阅。 必须使用**用户**订阅。 

1. 在 Azure Stack 订阅中需要密钥保管库服务。

1. 在 marketplace 中会用到 Kubernetes 群集。 

如果缺少该密钥保管库服务和 Kubernetes 群集 marketplace 项，请与 Azure Stack 管理员联系。

## <a name="create-a-service-principal"></a>创建服务主体

您需要使用 Azure Stack 管理员可以使用 AD FS 作为标识解决方案时设置服务主体。 服务主体允许应用程序访问的 Azure Stack 资源。

1. Azure Stack 管理员向你提供一个证书和服务主体的信息。

   - 服务主体信息应如下所示：

     ```Text  
       ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
       ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
       Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
       ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
       PSComputerName        : azs-ercs01
       RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
     ```

   - 你的证书将具有扩展名的文件`.pfx`。 你将以机密形式存储在密钥保管库中的证书。

2. 将新的服务主体作为参与者角色分配给你的订阅。 有关说明，请参阅[将角色分配](../operator/azure-stack-create-service-principals.md)。

3. 创建密钥保管库用于存储部署的证书。 使用以下 PowerShell 脚本而不是在门户。

   - 需要具有以下几部分信息：

       | 值 | 描述 |
       | ---   | ---         |
       | Azure 资源管理器终结点 | Microsoft Azure 资源管理器是一种管理框架，允许管理员部署、 管理和监视 Azure 资源。 Azure 资源管理器可以通过单个操作以组任务而不是单个任务的形式处理这些任务。<br>在 Azure Stack 开发工具包 (ASDK) 的终结点是： `https://management.local.azurestack.external/`<br>集成系统中的终结点是： `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
       | 你的订阅 ID | [订阅 ID](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) 用于访问 Azure Stack 中的套餐。 |
       | 您的用户名称 | 使用只是您的用户名称而不是你的域名和用户名，如`username`而不是`azurestack\username`。 |
       | 资源组名称  | 新的资源组或选择现有资源组的名称。 资源名称必须为字母数字，且必须小写。 |
       | 密钥保管库名称 | 在保管库的名称。<br> 正则表达式模式： `^[a-zA-Z0-9-]{3,24}$` |
       | 资源组位置 | 资源组的位置。 这是为 Azure Stack 安装选择的区域。 |

   - 使用提升的提示符下打开 PowerShell 并[连接到 Azure Stack](azure-stack-powershell-configure-user.md#connect-to-azure-stack-with-ad-fs)。 使用更新为你的值的参数运行以下脚本：

   ```powershell  
       $armEndpoint="<Azure Resource Manager Endpoint>"
       $subscriptionId="<Your Subscription ID>"
       $username="<your user name >"
       $resource_group_name = "<the resource group name >"
       $key_vault_name = "<keyvault name>"
       $resource_group_location="<resource group location>"
        
       # Login Azure Stack Environment
       Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
       $mycreds = Get-Credential
       Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
        
       # Create new Resource group and key vault
       New-AzureRmResourceGroup -Name $resource_group_name -Location $resource_group_location -Force
        
       # Note, Do not omit -EnabledForTemplateDeployment flag
       New-AzureRmKeyVault -VaultName $key_vault_name -ResourceGroupName $resource_group_name -Location $resource_group_location -EnabledForTemplateDeployment
        
       # Obtain the security identifier(SID) of the active directory user
       $adUser = Get-ADUser -Filter "Name -eq '$username'" -Credential $mycreds
       $objectSID = $adUser.SID.Value
       # Set the key vault access policy
       Set-AzureRmKeyVaultAccessPolicy -VaultName $key_vault_name -ResourceGroupName $resource_group_name -ObjectId $objectSID -BypassObjectIdValidation -PermissionsToKeys all -PermissionsToSecrets all
     ```

4. 将证书上传到密钥保管库。

   - 需要具有以下几部分信息：

       | 值 | 描述 |
       | ---   | ---         |
       | 证书路径 | 证书的 FQDN 或文件路径。 |
       | 证书密码 | 证书密码。 |
       | 机密名称 | 用来引用保管库中存储的证书的机密名称。 |
       | 密钥保管库名称 | 上一步中创建的密钥保管库的名称。 |
       | Azure 资源管理器终结点 | 在 Azure Stack 开发工具包 (ASDK) 的终结点是： `https://management.local.azurestack.external/`<br>集成系统中的终结点是： `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/` |
       | 你的订阅 ID | [订阅 ID](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) 用于访问 Azure Stack 中的套餐。 |

   - 使用提升的提示符下打开 PowerShell 并[连接到 Azure Stack](azure-stack-powershell-configure-user.md#connect-to-azure-stack-with-ad-fs)。 使用更新为你的值的参数运行以下脚本：

    ```powershell
        
     # upload the pfx to key vault
     $tempPFXFilePath = "<certificate path>"
     $password = "<certificate password>"
     $keyVaultSecretName = "<secret name>"
     $keyVaultName = "<key vault name>"
     $armEndpoint="<Azure Resource Manager Endpoint>"
     $subscriptionId="<Your Subscription ID>"
     # Login Azure Stack Environment
     Add-AzureRmEnvironment -ARMEndpoint $armEndpoint -Name t
     $mycreds = Get-Credential
     Login-AzureRmAccount -Credential $mycreds -Environment t -Subscription $subscriptionId
    
     $certContentInBytes = [io.file]::ReadAllBytes($tempPFXFilePath)
     $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
     $jsonObject = @"
     {
     "data": "$pfxAsBase64EncodedString",
     "dataType" :"pfx",
     "password": "$password"
     }
     "@
     $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
     $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
     $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
     $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name $keyVaultSecretName -SecretValue $secret 
     ```

## <a name="deploy-kubernetes"></a>部署 Kubernetes

1. 打开 [Azure Stack 门户](https://portal.local.azurestack.external)。

1. 选择“+ 创建资源” > “计算” > “Kubernetes 群集”。 单击**创建**。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/01_kub_market_item.png)

### <a name="1-basics"></a>1.基本

1. 在“创建 Kubernetes 群集”中选择“基本信息”。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/02_kub_config_basic.png)

1. 选择**订阅** ID。

1. 输入新资源组的名称，或者选择现有资源组。 资源名称必须为字母数字，且必须小写。

1. 选择资源组的“位置”。 这是为 Azure Stack 安装选择的区域。

### <a name="2-kubernetes-cluster-settings"></a>2.Kubernetes 群集设置

1. 在“创建 Kubernetes 群集”中选择“Kubernetes 群集设置”。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/03_kub_config_settings-adfs.png)

1. 输入 **Linux VM 管理员用户名**。 构成 Kubernetes 群集和 DVM 的 Linux 虚拟机的用户名。

1. 输入 **SSH 公钥**，用于向所有作为 Kubernetes 群集和 DVM 的一部分创建的 Linux 计算机授权。

1. 输入特定于区域的**主配置文件 DNS 前缀**。 这必须是特定于区域的名称，例如 `k8s-12345`。 最佳做法是尝试选择与资源组名称相同的名称。

    > [!Note]  
    > 为每个群集使用新且唯一的主配置文件 DNS 前缀。

1. 选择“Kubernetes 主池配置文件计数”。 此计数包含主池中的节点数。 其范围为 1 到 7。 此值应当为奇数。

1. 选择“Kubernetes 主 VM 的 VMSize”。

1. 选择“Kubernetes 节点池配置文件计数”。 此计数包含群集中的代理数。 

1. 选择“存储配置文件”。 可以选择“Blob 磁盘”或“托管磁盘”。 这指定 Kubernetes 节点 VM 的 VM 大小。 

1. 选择**ADFS**有关**Azure Stack 的标识系统**为 Azure Stack 安装。

1. 输入**服务主体 ClientId**，供 Kubernetes Azure 云提供程序使用。 Azure Stack 管理员创建服务主体时标识应用程序 ID 为客户端 ID。

1. 输入**密钥保管库资源组**源包含证书的密钥保管库。

1. 输入**密钥保管库名称**包含你的证书作为机密的密钥保管库的名称。 

1. 输入**密钥保管库机密**。 机密名称引用你的证书。

1. 输入 **Kubernetes Azure 云提供程序版本**。 这是 Kubernetes Azure 提供程序的版本。 Azure Stack 为每个 Azure Stack 版本发布了自定义的 Kubernetes 内部版本。

### <a name="3-summary"></a>3.摘要

1. 选择“摘要”。 此边栏选项卡显示针对 Kubernetes 群集配置设置的验证消息。

    ![部署解决方案模板](media/azure-stack-solution-template-kubernetes-deploy/04_preview.png)

2. 复查你的设置。

3. 选择“确定”以部署群集。

> [!TIP]  
>  如果对你的部署有疑问，可以在 [Azure Stack 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中发布问题或查看是否已经有人已回答了该问题。 

## <a name="next-steps"></a>后续步骤

[连接到群集](azure-stack-solution-template-kubernetes-deploy.md#connect-to-your-cluster)

[启用 Kubernetes 仪表板](azure-stack-solution-template-kubernetes-dashboard.md)