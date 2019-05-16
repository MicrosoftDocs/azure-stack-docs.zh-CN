---
title: 在 Azure Stack 中部署受保护的 Service Fabric 群集 | Microsoft Docs
description: 了解如何在 Azure Stack 中部署受保护的 Service Fabric 群集
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: shnatara
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: b35368804423a4647b84000d95adf41ee5fe09b2
ms.sourcegitcommit: 87d93cdcdb6efb06e894f56c2f09cad594e1a8b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65712467"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>在 Azure Stack 中部署 Service Fabric 群集

使用 Azure 市场中的“Service Fabric 群集”项在 Azure Stack 中部署受保护的 Service Fabric 群集。 

有关使用 Service Fabric 的详细信息，请参阅[的 Azure Service Fabric 概述](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)并[Service Fabric 群集安全方案](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)Azure 文档中。

Azure Stack 中的 Service Fabric 群集不使用资源提供程序 Microsoft.ServiceFabric。 相反，在 Azure Stack 中，Service Fabric 群集是虚拟机规模集与预安装的软件，在使用[Desired State Configuration (DSC)](https://docs.microsoft.com/powershell/dsc/overview/overview)。

## <a name="prerequisites"></a>必备组件

若要部署 Service Fabric 群集，必须做好以下准备：
1. **群集证书**  
   这是部署 Service Fabric 时添加到密钥保管库的 X.509 服务器证书。 
   - 此证书中的 **CN** 必须与创建的 Service Fabric 群集的完全限定域名 (FQDN) 匹配。 
   - 证书格式必须是 PFX，因为需要公钥和私钥。 
     请参阅创建此服务器端证书所要满足的[要求](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)。

     > [!NOTE]  
     > 出于测试目的，可以使用 X.509 服务器证书的自签名的证书就地。 自签名的证书不需要与群集的 FQDN 匹配。

1. **管理客户端证书**  
   这是客户端使用对 Service Fabric 群集，可以自签名进行身份验证的证书。 请参阅创建此客户端证书所要满足的[要求](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)。

1. **必须在 Azure Stack 市场中提供以下各项：**
    - **Windows Server 2016** - 模板使用 Windows Server 2016 映像来创建群集。  
    - **自定义脚本扩展**-从 Microsoft 的虚拟机扩展。  
    - **PowerShell Desired Stage Configuration** - Microsoft 提供的虚拟机扩展。


## <a name="add-a-secret-to-key-vault"></a>向 Key Vault 添加机密
若要部署的 Service Fabric 群集，必须指定正确的 Key Vault*机密标识符*或 Service Fabric 群集的 URL。 Azure 资源管理器模板使用密钥保管库作为输入。 然后，该模板在安装 Service Fabric 群集时检索群集证书。

> [!IMPORTANT]  
> 您必须使用 PowerShell 将机密添加到密钥保管库，以用于 Service Fabric。 不要使用门户。  

使用以下脚本来创建密钥保管库，并添加*群集证书*到它。 （请参阅[先决条件](#prerequisites)。）在运行该脚本之前，请查看示例脚本并更新指示的参数，使之与环境匹配。 此脚本还会输出需要向 Azure 资源管理器模板提供的值。 

> [!TIP]  
> 该脚本才能成功之前，必须有公共产品/服务，包括计算、 网络、 存储和密钥保管库的服务。 

  ```powershell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


有关详细信息，请参阅[使用 PowerShell 的 Azure Stack 上管理密钥保管库](azure-stack-key-vault-manage-powershell.md)。

## <a name="deploy-the-marketplace-item"></a>部署市场项

1. 在用户门户中，转到“+ 创建资源” > “计算” > “Service Fabric 群集”。 

   ![选择“Service Fabric 群集”](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

1. 填写每个页（例如“基本信息”）中的部署窗体。 如果不确定要指定哪个值，请使用默认值。 选择“确定”转到下一页：

   ![基本](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

1. 在“网络设置”页上，可以指定要对应用程序打开的特定端口：

   ![网络设置](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

1. 上*安全*页上，添加所获取的值[创建 Azure 密钥保管库](#add-a-secret-to-key-vault)并上传机密。

   对于“管理客户端证书指纹”，请输入管理客户端证书的指纹。 （请参阅[先决条件](#prerequisites)。）
   
   - 源 Key Vault：指定整个`keyVault id`从脚本结果的字符串。 
   - 群集证书 URL：指定从整个 URL`Secret Id`从脚本结果。 
   - 群集证书指纹：指定脚本结果中的 *Cluster Certificate Thumbprint*（群集证书指纹）。
   - 管理客户端证书指纹：指定在先决条件中创建的*管理客户端证书指纹*。 

   ![脚本输出](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![安全](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

1. 完成向导，然后选择“创建”以部署 Service Fabric 群集。



## <a name="access-the-service-fabric-cluster"></a>访问 Service Fabric 群集
可以使用 Service Fabric Explorer 或 Service Fabric PowerShell 访问 Service Fabric 群集。


### <a name="use-service-fabric-explorer"></a>使用 Service Fabric Explorer
1.  请确保浏览器有权访问你的管理员客户端证书，并可以向 Service Fabric 群集进行身份验证。  

    a. 打开 Internet Explorer 并转到“Internet 选项” > “内容” > “证书”。
  
    b. 在“证书”中，选择“导入”启动“证书导入向导”，然后单击“下一步”。 在“要导入的文件”页上单击“浏览”，然后选择提供给 Azure 资源管理器模板的**管理客户端证书**。
        
       > [!NOTE]  
       > 此证书不是先前添加到密钥保管库的群集证书。  

    c. 确保您有"个人信息交换"— — 文件资源管理器窗口的扩展下拉列表中选择。  

       ![个人信息交换](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. 在“证书存储”页上选择“个人”，然后完成向导。  
       ![证书存储](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. 若要查找 Service Fabric 群集的 FQDN：  

    a. 转到与 Service Fabric 群集关联的资源组，并找到“公共 IP 地址”资源。 选择与“公共 IP 地址”关联的对象，打开“公共 IP 地址”边栏选项卡。  

      ![公用 IP 地址](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. 在“公共 IP 地址”边栏选项卡上，FQDN 显示为“DNS 名称”。  

      ![DNS 名称](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. 若要查找 Service Fabric Explorer 的 URL 和客户端连接终结点，请查看模板部署的结果。

1. 在浏览器中转到 <https://*FQDN*:19080>。 将 *FQDN* 替换为在步骤 2 中获取的 Service Fabric 群集 FQDN。   
   如果已使用了自签名的证书，则会连接并不安全的警告。 若要继续的 web 站点，请选择**详细信息**，然后**转到网页**。 

1. 若要在站点中进行身份验证，必须选择要使用的证书。 选择“更多选项”，选择适当的证书，然后单击“确定”连接到 Service Fabric Explorer。 

   ![验证](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



### <a name="use-service-fabric-powershell"></a>使用 Service Fabric PowerShell

1. 安装*Microsoft Azure Service Fabric SDK*从[准备开发环境上 Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) Azure Service Fabric 文档中。  

1. 安装完成后，配置系统环境变量，确保可从 PowerShell 访问 Service Fabric cmdlet。  
    
    a. 转到“控制面板” > “系统和安全” > “系统”并选择“高级系统设置”。  
    
      ![控制面板](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. 在“系统属性”的“高级”选项卡上，选择“环境变量”。  

    c. 对于“系统变量”，请编辑“路径”，并确保 **C:\\Program Files\\Microsoft Service Fabric\\bin\\Fabric\\Fabric.Code** 位于环境变量列表的最前面。  

      ![环境变量列表](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. 更改环境变量的顺序后，重启 PowerShell，然后运行以下 PowerShell 脚本获取 Service Fabric 群集的访问权限：

   ```powershell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ```
   
   > [!NOTE]  
   > 脚本中群集名称的前面没有 *https://*。 必须指定端口 19000。

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)
