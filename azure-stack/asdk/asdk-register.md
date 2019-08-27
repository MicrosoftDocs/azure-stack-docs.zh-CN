---
title: 将 ASDK 注册到 Azure | Microsoft Docs
description: 了解如何在 Azure 中注册 Azure Stack 开发工具包 (ASDK) 以启用 marketplace 联合和使用情况报告。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/14/2019
ms.openlocfilehash: 886271e99b10d3fec0801f977a693a01e59fc0a5
ms.sourcegitcommit: 7968f9f0946138867323793be9966ee2ef99dcf4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70025866"
---
# <a name="register-the-asdk-with-azure"></a>将 ASDK 注册到 Azure

可将 Azure Stack 开发工具包 (ASDK) 安装注册到 Azure，以便从 Azure 下载市场项，并设置向 Microsoft 报告商务数据的功能。 需要注册才能支持完整的 Azure Stack 功能，包括市场联合。 需要注册才能测试重要的 Azure Stack 功能，如市场联合和使用情况报告。 注册 Azure Stack 之后，使用情况将报告给 Azure 商业组件。 用于注册的订阅下会显示此信息。 但是，ASDK 用户无需付费，不管他们报告的用量是多少。

如果未注册 ASDK, 可能会看到 "**需要激活**" 警告警报, 提示注册 ASDK。 此行为是预期的行为。

## <a name="prerequisites"></a>先决条件

在使用这些说明将 ASDK 注册到 Azure 之前, 请确保已安装 Azure Stack PowerShell 并按[部署后配置](asdk-post-deploy.md)一文中所述下载 Azure Stack 工具。

在用于向 Azure 注册 ASDK 的计算机上, PowerShell 语言模式还必须设置为**FullLanguage** 。 若要验证当前的语言模式是否设置为 Full，请打开权限提升的 PowerShell 窗口，并运行以下 PowerShell 命令：

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

确保输出返回的是 **FullLanguage**。 如果返回任何其他语言模式, 则需要在另一台计算机上运行注册, 或将语言模式设置为**FullLanguage** , 然后再继续。

用于注册的 Azure AD 帐户需要具有访问 Azure 订阅的权限, 并且具有在与该订阅关联的目录中创建标识应用和服务主体的权限。 建议通过[创建用于注册的服务帐户](../operator/azure-stack-registration-role.md)(而不是使用全局管理员凭据), 将 Azure Stack 注册到 Azure。

## <a name="register-the-asdk"></a>注册 ASDK

遵循以下步骤将 ASDK 注册到 Azure。

> [!NOTE]
> 所有这些步骤必须在可以访问特权终结点的计算机上运行。 对于 ASDK, 这是 ASDK 主机计算机。

1. 以管理员身份打开 PowerShell 控制台。  

2. 运行以下 PowerShell 命令，将 ASDK 安装注册到 Azure。 登录到你的 Azure 计费订阅 ID 和本地 ASDK 安装。 如果还没有 Azure 计费订阅 ID, 可以在[此处创建一个免费的 azure 帐户](https://azure.microsoft.com/free/?b=17.06)。 注册 Azure Stack 不会对 Azure 订阅收取任何费用。<br><br>在运行 **Set-AzsRegistration** cmdlet 时设置适用于注册的唯一名称。 **RegistrationName** 参数的默认值为 **AzureStackRegistration**。 但是，如果在多个 Azure Stack 实例上使用同一名称，该脚本会失败。

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

3. 该脚本完成后，会显示以下消息：“现已使用提供的参数注册并激活环境”。

    ![环境现已注册](media/asdk-register/1.PNG)

## <a name="register-in-disconnected-environments"></a>在离线环境中注册

如果要在断开连接的环境中注册 Azure Stack (不带 internet 连接), 则需要从 Azure Stack 环境中获取注册令牌, 然后在可连接到 Azure 的计算机上使用该令牌来注册和创建激活ASDK 环境的资源。

 > [!IMPORTANT]
 > 在使用这些说明注册 Azure Stack 之前, 请确保已安装适用于 Azure Stack 的 PowerShell 并下载 Azure Stack 工具, 如 ASDK 主计算机上的[部署后配置](asdk-post-deploy.md)文章和用于连接到 Azure 并注册的具有 internet 访问权限的计算机。

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>从 Azure Stack 环境获取注册令牌

在 ASDK 主计算机上, 以管理员身份启动 PowerShell, 并导航到下载 Azure Stack 工具时创建的**test-azurestack-master**目录中的**注册**文件夹。 使用以下 PowerShell 命令导入 **RegisterWithAzure.psm1** 模块，然后使用 **Get-AzsRegistrationToken** cmdlet 获取注册令牌：  

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

保存此注册令牌，以便在连接到 Internet 的计算机上使用。 可以从`$FilePathForRegistrationToken`参数创建的文件中复制文件或文本。

### <a name="connect-to-azure-and-register"></a>连接到 Azure 并注册

在连接 internet 的计算机上, 使用以下 PowerShell 命令导入**registerwithazure.psm1**模块, 然后使用**unregister-azsenvironment** cmdlet 通过刚刚创建的注册令牌注册到 Azure, 然后唯一的注册名称:  

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

或者，可以使用 **Get-Content** cmdlet 指向包含注册令牌的文件：

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

注册完成后, 应会看到类似于以下内容的消息:**你的 Azure Stack 环境现在已注册到 Azure。**

> [!IMPORTANT]
> 不要关闭 PowerShell 窗口。

保存注册令牌和注册资源名称，供以后参考。

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>从 Azure 注册资源检索激活密钥

仍然使用已连接到 Internet 的计算机**和同一 PowerShell 控制台窗口**，从注册到 Azure 时创建的注册资源中检索激活密钥。

若要获取激活密钥, 请运行以下 PowerShell 命令。 使用在上一步中注册 Azure 时提供的相同的唯一注册名称值:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```

### <a name="create-an-activation-resource-in-azure-stack"></a>在 Azure Stack 中创建激活资源

使用 **Get-AzsActivationKey** 从创建的激活密钥中获取文件或文本后，返回到 Azure Stack 环境。 运行以下 PowerShell 命令，使用该激活密钥在 Azure Stack 中创建激活资源：   

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

或者，可以使用 **Get-Content** cmdlet 指向包含注册令牌的文件：

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

激活完成后, 应会看到类似于以下内容的消息:**你的环境已完成注册和激活过程。**

## <a name="verify-the-registration-was-successful"></a>验证注册是否成功

可以使用“区域管理”磁贴，验证 Azure Stack 注册是否成功。 此磁贴在管理门户中的默认仪表板上可用。

1. 登录到[Azure Stack 管理门户](https://adminportal.local.azurestack.external)。

2. 在“仪表板”中，选择“区域管理”。

    [![Azure Stack 管理门户中的 "区域管理" 磁贴](media/asdk-register/admin1sm.png "区域管理磁贴")](media/asdk-register/admin1.png#lightbox)

3. 选择“属性”。 此边栏选项卡显示环境的状态和详细信息。 状态可能是“已注册”，也可能是“未注册”。 如果是已注册，则还会显示用于注册 Azure Stack 的 Azure 订阅 ID，以及注册资源组和名称。

## <a name="move-a-registration-resource"></a>移动注册资源
支持在同一订阅下的资源组之间移动注册资源。 有关将资源移到新资源组的详细信息, 请参阅[将资源移到新的资源组或订阅](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。


## <a name="next-steps"></a>后续步骤

- [添加 Azure Stack 市场项](../operator/azure-stack-marketplace.md)
