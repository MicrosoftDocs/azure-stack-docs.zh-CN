---
title: 将 ASDK 注册到 Azure
description: 了解如何在 Azure 中注册 Azure Stack 开发工具包（ASDK）以启用 marketplace 联合和使用情况报告。
author: justinha
ms.topic: article
ms.date: 06/14/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/14/2019
ms.openlocfilehash: 2f0af3bd550b31e70f7c1e722ab79149c57c1a2f
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700640"
---
# <a name="register-the-asdk-with-azure"></a>将 ASDK 注册到 Azure

可以注册 Azure 的 Azure Stack 开发工具包（ASDK）安装，以便从 Azure 下载 marketplace 项，并将商务数据报表设置回 Microsoft。 需要注册才能支持完整 Azure Stack 功能，包括 marketplace 供稿。 需要注册才能测试重要的 Azure Stack 功能，例如 marketplace 联合和使用情况报告。 注册 Azure Stack 后，使用情况将报告给 Azure commerce。 你可以在用于注册的订阅下查看它。 但是，ASDK 用户不会向其报告的任何使用付费。

如果未注册 ASDK，可能会看到 "**需要激活**" 警告警报，提示注册 ASDK。 此行为是预期的行为。

## <a name="prerequisites"></a>必备条件

在使用这些说明将 ASDK 注册到 Azure 之前，请确保已安装 Azure Stack PowerShell 并按[部署后配置](asdk-post-deploy.md)一文中所述下载 Azure Stack 工具。

在用于向 Azure 注册 ASDK 的计算机上，PowerShell 语言模式还必须设置为**FullLanguage** 。 若要验证当前语言模式是否设置为 full，请打开提升的 PowerShell 窗口并运行以下 PowerShell 命令：

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

确保输出返回**FullLanguage**。 如果返回任何其他语言模式，则需要在另一台计算机上运行注册，或将语言模式设置为**FullLanguage** ，然后再继续。

用于注册的 Azure AD 帐户需要具有访问 Azure 订阅的权限，并且具有在与该订阅关联的目录中创建标识应用和服务主体的权限。 建议通过[创建用于注册的服务帐户](../operator/azure-stack-registration-role.md)（而不是使用全局管理员凭据），将 Azure Stack 注册到 Azure。

## <a name="register-the-asdk"></a>注册 ASDK

请按照以下步骤将 ASDK 注册到 Azure。

> [!NOTE]
> 所有这些步骤都必须从有权访问特权终结点的计算机上运行。 对于 ASDK，这是 ASDK 主机计算机。

1. 以管理员身份打开 PowerShell 控制台。  

2. 运行以下 PowerShell 命令，将 ASDK 安装注册到 Azure。 登录到你的 Azure 计费订阅 ID 和本地 ASDK 安装。 如果还没有 Azure 计费订阅 ID，可以在[此处创建一个免费的 azure 帐户](https://azure.microsoft.com/free/?b=17.06)。 注册 Azure Stack 不会对 Azure 订阅产生任何费用。<br><br>运行**set-azsregistration** cmdlet 时设置注册的唯一名称。 **RegistrationName**参数的默认值为**AzureStackRegistration**。 但是，如果在多个 Azure Stack 实例上使用相同的名称，则脚本将失败。

    ```powershell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    # If you have multiple subscriptions, run the following command to select the one you want to use:
    # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription
    
    # Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```

3. 脚本完成后，应会看到以下消息：**你的环境现在已注册并使用提供的参数激活。**

    ![你的环境现在已注册](media/asdk-register/1.PNG)

## <a name="register-in-disconnected-environments"></a>在断开连接的环境中注册

如果要在断开连接的环境中注册 Azure Stack （不带 internet 连接），则需要从 Azure Stack 环境中获取注册令牌，然后在可连接到 Azure 的计算机上使用该令牌来注册和创建激活ASDK 环境的资源。

 > [!IMPORTANT]
 > 在使用这些说明注册 Azure Stack 之前，请确保已安装适用于 Azure Stack 的 PowerShell 并下载 Azure Stack 工具，如 ASDK 主计算机上的[部署后配置](asdk-post-deploy.md)文章和用于连接到 Azure 和注册的 internet 访问的计算机上所述。

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>从 Azure Stack 环境中获取注册令牌

在 ASDK 主计算机上，以管理员身份启动 PowerShell，并导航到下载 Azure Stack 工具时创建的**test-azurestack-master**目录中的**注册**文件夹。 使用以下 PowerShell 命令导入**registerwithazure.psm1**模块，然后使用**AzsRegistrationToken** cmdlet 获取注册令牌：  

   ```powershell  
   # Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Create registration token
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```

保存此注册令牌，以在连接 internet 的计算机上使用。 可以从 `$FilePathForRegistrationToken` 参数创建的文件中复制文件或文本。

### <a name="connect-to-azure-and-register"></a>连接到 Azure 并注册

在连接 internet 的计算机上，使用以下 PowerShell 命令导入**registerwithazure.psm1**模块，并使用刚刚创建的注册令牌和唯一的注册名称，使用**unregister-azsenvironment** cmdlet 注册到 Azure：  

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

或者，可以使用 "**获取内容**" cmdlet 指向包含注册令牌的文件：

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

注册完成后，应会看到类似于以下内容的消息：**你的 Azure Stack 环境现在已注册到 Azure。**

> [!IMPORTANT]
> 不要**关闭 PowerShell**窗口。

保存注册令牌和注册资源名称以供将来参考。

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>从 Azure 注册资源检索激活密钥

仍在使用连接 internet 的计算机**和同一 PowerShell 控制台窗口**，从在 Azure 中注册时创建的注册资源检索激活密钥。

若要获取激活密钥，请运行以下 PowerShell 命令。 使用在上一步中注册 Azure 时提供的相同的唯一注册名称值：  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```

### <a name="create-an-activation-resource-in-azure-stack"></a>在 Azure Stack 中创建激活资源

返回到 Azure Stack 环境，其中包含从**AzsActivationKey**创建的激活密钥中的文件或文本。 运行以下 PowerShell 命令，以使用该激活密钥在 Azure Stack 中创建激活资源：   

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

或者，可以使用 "**获取内容**" cmdlet 指向包含注册令牌的文件：

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

激活完成后，应会看到类似于以下内容的消息：**你的环境已完成注册和激活过程。**

## <a name="verify-the-registration-was-successful"></a>验证注册是否成功

您可以使用 "**区域管理**" 磁贴来验证 Azure Stack 注册是否成功。 此磁贴可在管理员门户中的默认仪表板上使用。

1. 登录到[Azure Stack 管理员门户](https://adminportal.local.azurestack.external)。

2. 在仪表板中，选择 "**区域管理**"。

    [![Azure Stack 管理员门户中的区域管理磁贴](media/asdk-register/admin1sm.png "区域管理磁贴")](media/asdk-register/admin1.png#lightbox)

3. 选择“属性”。 此边栏选项卡显示了你的环境的状态和详细信息。 状态可以为 "**已注册**" 或 "**未注册**"。 如果已注册，还会显示用于注册 Azure Stack 的 Azure 订阅 ID，以及注册资源组和名称。

## <a name="move-a-registration-resource"></a>移动注册资源
支持在同一订阅**下的资源**组之间移动注册资源。 有关将资源移到新资源组的详细信息，请参阅[将资源移到新的资源组或订阅](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。


## <a name="next-steps"></a>后续步骤

- [添加 Azure Stack 中心市场项](../operator/azure-stack-marketplace.md)
