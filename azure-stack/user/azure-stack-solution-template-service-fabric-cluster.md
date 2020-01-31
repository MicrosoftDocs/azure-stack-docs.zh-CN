---
title: 在 Azure Stack 中心部署安全的 Service Fabric 群集
description: 了解如何在 Azure Stack 中心部署安全的 Service Fabric 群集
author: mattbriggs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: shnatara
ms.lastreviewed: 09/25/2019
ms.openlocfilehash: fadc0adbd8cad2ff92b3849d6c79c933a1bf3bfb
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884425"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack-hub"></a>在 Azure Stack 中心部署 Service Fabric 群集

使用 Azure Marketplace 中的**Service Fabric 群集**项在 Azure Stack Hub 中部署受保护的 Service Fabric 群集。 

有关使用 Service Fabric 的详细信息，请参阅 azure 文档中[的 azure Service Fabric 概述](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview)和[Service Fabric 群集安全方案](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)。

Azure Stack 中心的 Service Fabric 群集不使用资源提供程序 ServiceFabric。 相反，在 Azure Stack 中心，Service Fabric 群集是使用[所需状态配置（DSC）](https://docs.microsoft.com/powershell/scripting/dsc/overview/overview)预安装软件的虚拟机规模集。

## <a name="prerequisites"></a>必备组件

部署 Service Fabric 群集需要以下各项：
1. **群集证书**  
   这是部署 Service Fabric 时添加到 Key Vault 中的 x.509 服务器证书。 
   - 此证书上的**CN**必须与创建的 Service Fabric 群集的完全限定的域名（FQDN）匹配。 
   - 证书格式必须为 PFX，因为公钥和私钥都是必需的。 
     请参阅创建此服务器端证书的[要求](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)。

     > [!NOTE]  
     > 你可以使用 x.509 服务器证书的自签名证书进行测试。 自签名证书不需要与群集的 FQDN 相匹配。

1. **管理员客户端证书**  
   这是客户端用来向 Service Fabric 群集进行身份验证的证书，该证书可以是自签名的。 请参阅创建此客户端证书的[要求](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security)。

1. **Azure Stack 中心 Marketplace 中必须有以下各项：**
    - **Windows server 2016** -该模板使用 Windows Server 2016 映像创建群集。  
    - **自定义脚本扩展**-Microsoft 的虚拟机扩展。  
    - **PowerShell Desired 阶段配置**-Microsoft 的虚拟机扩展。


## <a name="add-a-secret-to-key-vault"></a>向 Key Vault 添加机密
若要部署 Service Fabric 群集，必须为 Service Fabric 群集指定正确的 Key Vault*机密标识符*或 URL。 Azure 资源管理器模板采用 Key Vault 作为输入。 然后，该模板将在安装 Service Fabric 群集时检索群集证书。

> [!IMPORTANT]  
> 必须使用 PowerShell 将机密添加到 Key Vault 以供 Service Fabric 使用。 不要使用门户。  

使用以下脚本创建 Key Vault，并向其添加*群集证书*。 （请参阅[必备组件](#prerequisites)。）在运行该脚本之前，请查看示例脚本并更新指示的参数以匹配您的环境。 此脚本还将输出需要提供给 Azure 资源管理器模板的值。 

> [!TIP]  
> 在脚本成功之前，必须有一个公共产品/服务，其中包括用于计算、网络、存储和 Key Vault 的服务。 

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


有关详细信息，请参阅[通过 PowerShell 管理 Azure Stack 中心的 Key Vault](azure-stack-key-vault-manage-powershell.md)。

## <a name="deploy-the-marketplace-item"></a>部署 Marketplace 项

1. 在用户门户中，参阅 " **+ 创建资源**" > **计算** > **Service Fabric 群集**"。 

   ![选择 Service Fabric 群集](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. 对于每个页面（如 "*基本*"），填写 "部署" 窗体。 如果你不确定值，请使用默认值。

    若要部署到断开连接的 Azure Stack 中心或部署 Service Fabric 的另一版本，请下载 Service Fabric 部署包及其相应的运行时包，并将其托管在 Azure Stack 中心 blob 上。 向 " **Service Fabric 部署包 url** " 和 " **Service Fabric 运行时包 url** " 字段提供这些值。
    > [!NOTE]  
    > 最新版本的 Service Fabric 及其相应的 SDK 之间存在兼容性问题。 在解决该问题之前，请向部署包 URL 和运行时包 URL 提供以下参数。 否则，部署将失败。
    > - Service Fabric 部署包 URL： <https://download.microsoft.com/download/8/3/6/836E3E99-A300-4714-8278-96BC3E8B5528/6.5.641.9590/Microsoft.Azure.ServiceFabric.WindowsServer.6.5.641.9590.zip>
    > - Service Fabric 运行时包 URL： <https://download.microsoft.com/download/B/0/B/B0BCCAC5-65AA-4BE3-AB13-D5FF5890F4B5/6.5.641.9590/MicrosoftAzureServiceFabric.6.5.641.9590.cab>
    >
    > 对于断开连接的部署，请从指定位置下载这些包，并将其在本地 Azure Stack 中心 Blob 上托管。

   ![基础](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

    
3. 在 "*网络设置*" 页上，可以指定要为应用程序打开的特定端口：

   ![网络设置](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. 在 "*安全*" 页上，添加通过[创建 Azure Key Vault](#add-a-secret-to-key-vault)获取的值并上传密钥。

   对于 "*管理员客户端证书指纹*"，请输入*管理客户端证书*的指纹。 （请参阅[必备组件](#prerequisites)。）
   
   - 源 Key Vault：指定脚本结果中的整个 `keyVault id` 字符串。 
   - 群集证书 URL：从脚本结果的 `Secret Id` 中指定整个 URL。 
   - 群集证书指纹：从脚本结果指定*群集证书指纹*。
   - 服务器证书 URL：如果想要使用群集证书中的单独证书，请将证书上传到 keyvault，并提供机密的完整 URL。 
   - 服务器证书指纹：指定服务器证书的指纹
   - 管理员客户端证书指纹：指定在先决条件中创建的*管理员客户端证书指纹*。 

   ![脚本输出](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![安全性](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. 完成该向导，然后选择 "**创建**" 以部署 Service Fabric 群集。



## <a name="access-the-service-fabric-cluster"></a>访问 Service Fabric 群集
可以通过使用 Service Fabric Explorer 或 Service Fabric PowerShell 来访问 Service Fabric 群集。


### <a name="use-service-fabric-explorer"></a>使用 Service Fabric Explorer
1.  确保浏览器有权访问管理员客户端证书，并且可以对 Service Fabric 群集进行身份验证。  

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 打开 Internet Explorer，并 > **内容**" > **证书**" 中转到 " **internet 选项**"。
  
    b.保留“数据库类型”设置，即设置为“共享”。 在 "证书" 上，选择 "**导入**" 以启动*证书导入向导*，然后单击 "**下一步**"。 在 "*要导入的文件*" 页上，单击 "**浏览**"，然后选择提供给 Azure 资源管理器模板的**管理员客户端证书**。
        
       > [!NOTE]  
       > 此证书不是以前添加到 Key Vault 的群集证书。  

    c. 确保在 "文件资源管理器" 窗口的 "扩展" 下拉列表中选择了 "个人信息交换"。  

       ![个人信息交换](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d.单击“下一步”。 在 "*证书存储*" 页上，选择 "**个人**"，然后完成该向导。  
       ![证书存储](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. 查找 Service Fabric 群集的 FQDN：  

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 中转到与 Service Fabric 群集关联的资源组，并找到*公共 IP 地址*资源。 选择与公共 IP 地址关联的对象打开 "*公共 ip 地址*" 边栏选项卡。  

      ![公共 IP 地址](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b.保留“数据库类型”设置，即设置为“共享”。 在 "公共 IP 地址" 边栏选项卡上，FQDN 显示为 " *DNS 名称*"。  

      ![DNS 名称](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. 若要查找 Service Fabric Explorer 和客户端连接终结点的 URL，请查看模板部署的结果。

1. 在浏览器中转到 <https://*FQDN*:19080>。 将*fqdn*替换为步骤2中的 Service Fabric 群集的 fqdn。   
   如果你使用的是自签名证书，则将收到一条警告消息，指出连接不安全。 若要继续浏览网站，请选择 "**详细信息**"，然后**转到该网页**。 

1. 若要向站点进行身份验证，你必须选择要使用的证书。 选择 "**更多选择**"，选择适当的证书，然后单击 **"确定"** 以连接到 Service Fabric Explorer。 

   ![Authenticate](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



### <a name="use-service-fabric-powershell"></a>使用 Service Fabric PowerShell

1. 从在 Azure Service Fabric 文档中的[Windows 上准备开发环境](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools)中安装*Microsoft Azure Service Fabric SDK* 。  

1. 安装完成后，请配置系统环境变量，以确保 Service Fabric cmdlet 可从 PowerShell 中访问。  
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 请参阅 **"控制面板"**  > **系统和安全** > **系统**"，然后选择"**高级系统设置**"。  
    
      ![控制面板](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b.保留“数据库类型”设置，即设置为“共享”。 在*系统属性*的 "**高级**" 选项卡上，选择 "**环境变量**"。  

    c. 对于 "*系统变量*"、"编辑**路径**" 和 "确保**C：\\程序文件\\Microsoft Service Fabric\\bin\\fabric**\\fabric 位于环境变量列表的顶部。  

      ![环境变量列表](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. 更改环境变量的顺序后，请重新启动 PowerShell，并运行以下 PowerShell 脚本以获取对 Service Fabric 群集的访问权限：

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
   > 脚本中的群集名称前没有*https://* 。 需要端口19000。

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack 中心](azure-stack-solution-template-kubernetes-deploy.md)
