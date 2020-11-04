---
title: 将 Azure Stack Hub 注册到 Azure
description: 了解如何向 Azure 注册 Azure Stack 集线器，以便下载 Azure Marketplace 项并设置数据报告。
author: sethmanheim
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 88f6c0e36fb8a101b963835fd7f0dcd69595e119
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93329030"
---
# <a name="register-azure-stack-hub-with-azure"></a>将 Azure Stack Hub 注册到 Azure

若要设置 Marketplace 联合，必须 Azure Stack 在部署完成后，在 (MDC) 或 Azure Stack 集线器上注册并激活模块化数据中心。 利用 Marketplace 供稿，管理员使用从 Azure Marketplace 下载的映像填充本地 Azure Stack 中心市场。

对于将连接到 Azure 云的系统以及要断开连接的系统，需要注册。

## <a name="overview"></a>概述

注册过程包括以下步骤：

1. 从 Azure Stack 中心导出注册令牌。
2. 将 Azure Stack 集线器注册到 Azure。
3. 从 Azure 导出激活密钥。
4. 激活 Azure Stack Hub。

如果系统已连接到 Azure 云，则可以使用单个脚本执行所有四个步骤。 如果系统已断开连接，则管理员必须单独执行这些步骤。

> [!NOTE]
> 对于 MDC，必须分别注册每个 Azure Stack 集线器系统。 因此，以下说明必须执行三次。

## <a name="prerequisites"></a>先决条件

在注册之前，需要满足以下先决条件：

- 验证凭据。
- 设置 PowerShell 语言模式。
- 安装适用于 Azure Stack Hub 的 PowerShell。
- 安装 Azure Stack 集线器工具模块
- 确定注册方案。

### <a name="verify-your-credentials"></a>验证凭据

将 Azure Stack Hub 注册到 Azure 之前，必须准备好：

- Azure 订阅的订阅 ID。  

    > [!Note]  
    > Azure 订阅与 azure 云、Azure 政府等 (的 azure 云环境相关联 ) 这将确定要连接到哪个云来访问 Marketplace 内容。
    > 
    > 必须为绝地大反击 services 批准用于注册的订阅。 这可确保你注册的设备有权无限制地使用资源，而不会报告 Azure 使用情况。 为了批准你的订阅，请向发送电子邮件，其中 azshregistration@microsoft.com 包含需要批准的订阅 ID，以及要注册) Azure Stack 中心耐用或模块化数据中心 (MDC。

- 订阅所有者的帐户用户名和密码。 
- 用户帐户必须有权访问 Azure 订阅并且有权在与该订阅关联的目录中创建标识应用和服务主体。 建议使用最低特权管理将 Azure Stack Hub 注册到 Azure。 有关如何创建将访问权限限制为注册订阅的自定义角色定义的详细信息，请参阅为 [Azure Stack 中心创建注册角色](../../operator/azure-stack-registration-role.md)。
- 注册 Azure Stack 集线器资源提供程序 (参阅以下部分了解详细信息) 。

注册后，Azure Active Directory (Azure AD) 全局管理员权限不是必需的。 但是，某些操作可能需要全局管理员凭据 (例如，资源提供程序安装程序脚本或需要向其授予权限的新功能) 。 您可以临时恢复帐户的全局管理员权限，也可以使用作为 *默认提供程序订阅* 的所有者的单独全局管理员帐户。

注册 Azure Stack Hub 的用户是 Azure AD 中服务主体的所有者。 只有注册了 Azure Stack Hub 的用户可以修改 Azure Stack Hub 注册。 如果非管理员用户不是注册服务主体的所有者尝试注册或重新注册 Azure Stack 集线器，则他们可能会看到403响应。 403响应指示用户没有足够的权限来完成该操作。

注册 Azure Stack Hub 不会对 Azure 订阅收取任何费用。

### <a name="powershell-language-mode"></a>PowerShell 语言模式

若要成功注册 Azure Stack Hub，必须将 PowerShell 语言模式设置为 **FullLanguageMode** 。 若要验证当前的语言模式是否设置为 Full，请打开权限提升的 PowerShell 窗口，并运行以下 PowerShell cmdlet：

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

确保输出返回 **FullLanguageMode** 。 如果返回任何其他语言模式，则需要在另一台计算机上运行注册，或在继续操作之前需要将语言模式设置为 **FullLanguageMode** 。

### <a name="install-powershell-for-azure-stack-hub"></a>安装适用于 Azure Stack Hub 的 PowerShell

使用适用于 Azure Stack Hub 的最新 PowerShell 来注册到 Azure。

如果尚未安装最新版本，请参阅 [为 Azure Stack 集线器安装 PowerShell](../../operator/azure-stack-powershell-install.md)。

### <a name="install-the-azure-stack-hub-tools-module"></a>安装 Azure Stack 集线器工具模块

[Azure Stack 中心工具模块](https://www.powershellgallery.com/packages/azs.tools.admin/0.1.0)包含支持 Azure Stack 集线器功能的 PowerShell 模块，包括注册功能。 注册过程中，必须导入并使用 **registerwithazure.psm1. hbase-runner.psm1** 子模块将 Azure Stack 集线器实例注册到 Azure。

若要安装最新的 Azure Stack 中心工具：

1. 打开提升的 PowerShell 命令提示符。
2. 运行以下 cmdlet：

    ```powershell  
        Install-Module -Name Azs.Tools.Admin
    ```

### <a name="determine-your-registration-scenario"></a>确定注册方案

Azure Stack 中心部署可能 *已连接* 或已 *断开* 连接。

- **已连接** ：已连接意味着你已部署 Azure Stack 集线器，以便它能够连接到 Internet 和 Azure。 你可以为标识存储 Azure AD 或 Active Directory 联合身份验证服务 (AD FS) 。

- **断开连接** ：通过 Azure 部署选项断开连接，你可以在没有连接到 internet 的情况下部署和使用 Azure Stack 集线器。

### <a name="determine-a-unique-registration-name-to-use"></a>确定要使用的唯一注册名称

向 Azure 注册 Azure Stack 集线器时，必须提供唯一的注册名称。 将 Azure Stack Hub 订阅与 Azure 注册关联的简便方法是使用 Azure Stack Hub **云 ID** 。

若要确定 Azure Stack Hub 部署的云 ID，请在可以访问特权终结点的计算机上以管理员身份打开 PowerShell，运行以下命令，并记录 **CloudID** 值：

```PowerShell
Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Get-AzureStackStampInformation
```

## <a name="registration-and-activation-for-systems-connected-to-the-azure-cloud"></a>已连接到 Azure 云的系统的注册和激活

使用以下步骤注册已连接到 Azure 的 Azure Stack 集线器系统。

> [!NOTE]  
> 所有这些步骤必须在可以访问特权终结点 (PEP) 的计算机上运行。 有关 PEP 的详细信息，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。

连接的环境可以访问 Internet 和 Azure。 对于这些环境，请将 Azure Stack 集线器资源提供程序注册到 Azure，然后配置你的计费模型。

1. 若要向 Azure 注册 Azure Stack 集线器资源提供程序，请以管理员身份启动 PowerShell ISE，并使用以下 PowerShell cmdlet，并将 **EnvironmentName** 参数设置为适当的 Azure 订阅类型 (参阅按如下所述) 参数。

2. 添加用于注册 Azure Stack Hub 的 Azure 帐户。 若要添加该帐户，请运行 **Add-AzureRmAccount** cmdlet。 系统将提示你输入 Azure 帐户凭据，并且可能需要根据帐户配置使用双因素身份验证。

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | 参数 | 说明 |  
   |-----|-----|
   | EnvironmentName | Azure 云订阅环境名称。 支持的环境名称为 **AzureCloud** 或 **AzureUSGovernment** 。  |

   >[!NOTE]
   > 如果会话过期，你的密码已更改，或你想要切换帐户，请在使用 **add-azurermaccount** ： **add-azurermaccount 进程** 登录之前运行以下 cmdlet。

3. 在同一 PowerShell 会话中，确保登录到正确的 Azure PowerShell 上下文。 此上下文是以前用于注册 Azure Stack 中心资源提供程序的 Azure 帐户：

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | 参数 | 说明 |  
   |-----|-----|
   | EnvironmentName | Azure 云订阅环境名称。 支持的环境名称为 **AzureCloud** 或 **AzureUSGovernment** 。  |

4. 如果有多个订阅，请运行以下命令，选择要使用的那个订阅：

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

5. 运行以下命令，将 Azure Stack 集线器资源提供程序注册到你的 Azure 订阅：

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

   或者，你可以使用 Azure 门户在 Azure 订阅中注册 Azure Stack 集线器资源提供程序。 使用与 Azure 订阅关联的帐户登录 Azure 门户。 **Categories**  >  **General**  >  单击 "常规 **订阅** "，然后选择要向其注册 Azure Stack 中心资源提供程序所需的订阅 ID。 在左侧窗格中，导航到 " **设置** " "  >  **资源提供程序** "。 选择 **test-azurestack** 资源提供程序，然后选择 " **注册** "。

   ![注册 Azure Stack 集线器资源提供程序](./media/registration-tzl/register-azure-resource-provider-portal.png)


6. 在同一 PowerShell 会话中，运行 **set-azsregistration** cmdlet：

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $msAssetTag = "Enter the value printed on the product"
   $RegistrationName = "<unique-registration-name>"

   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Custom `
      -RegistrationName $RegistrationName `
      -msAssetTag $msAssetTagName `
      -UsageReportingEnabled: $false
   ```
   `msAssetTag`对于自定义计费模式注册，MS 资产标记 () 是必需的，并在产品上打印。
    
   该过程需要花费 10 到 15 分钟。 命令完成后，会看到消息。 “现已使用提供的参数注册并激活环境”。

## <a name="registration-and-activation-for-systems-not-connected-to-the-azure-cloud"></a>未连接到 Azure 云的系统的注册和激活 

如果要在断开连接的环境中注册 Azure Stack 集线器 (没有 internet 连接) ，请执行以下步骤：

1. 从 Azure Stack 中心环境获取注册令牌。

2. 使用步骤1中的注册令牌连接到 Azure 并进行注册。 使用可以连接到 Azure 的计算机，并且已安装 Azure Stack 集线器的 PowerShell。

3. 从 Azure 注册检索激活密钥。

4. 使用 Azure 中的激活密钥激活 Azure Stack 集线器。

### <a name="get-a-registration-token-from-the-azure-stack-hub-environment"></a>从 Azure Stack Hub 环境获取注册令牌

从 Azure Stack 中心环境获取注册令牌。 然后，在可连接到 Azure 的计算机上使用该令牌，并为安装 Azure Stack 集线器和 Azure Stack 中心工具使用 PowerShell。

1. 若要获取注册令牌，请在权限提升的提示符下运行以下 PowerShell cmdlet：

   ```PowerShell
    $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt" 
    
    $RegistrationToken = Get-AzsRegistrationToken `
      -PrivilegedEndpointCredential $YourCloudAdminCredential `
      -UsageReportingEnabled:$False `
      -PrivilegedEndpoint $YourPrivilegedEndpoint `
      -BillingModel Custom -msAssetTag '<MS Asset tag>' `
      -TokenOutputFilePath $FilePathForRegistrationToken 
   ```

   > [!TIP]  
   > 注册令牌保存在指定的文件中 `$FilePathForRegistrationToken` 。 您可以根据需要更改路径或文件名。

2. 保存此注册令牌，以便在连接 Azure 的计算机上使用。 可以从复制文件或文本 `$FilePathForRegistrationToken` 。

### <a name="connect-to-azure-and-register"></a>连接到 Azure 并注册

在连接到 internet 的计算机上，登录到正确的 Azure PowerShell 上下文。 然后调用 **unregister-azsenvironment** 。 指定可注册到 Azure 的注册令牌。 如果要使用相同的 Azure 订阅 ID 注册 Azure Stack 集线器的多个实例，请指定唯一的注册名称。

需要注册令牌和唯一令牌名称。

1. 从提升权限的 PowerShell 提示符运行以下 cmdlet，以确保使用正确的订阅。

    ```powershell  
    Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
    ```

2. 然后，运行以下 PowerShell cmdlet：

    ```powershell  
    $RegistrationToken = "<Your Registration Token>"
    $RegistrationName = "<unique-registration-name>"
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

3. （可选）可以使用 " **获取内容** " cmdlet 指向包含注册令牌的文件。

   需要注册令牌和唯一令牌名称。

   1. 以管理员身份启动 PowerShell ISE，并导航到下载 Azure Stack Hub 工具时所创建的 **AzureStack-Tools-master** 目录中的 **Registration** 文件夹。 导入 **RegisterWithAzure.psm1** 模块：

      ```powershell  
      Import-Module .\RegisterWithAzure.psm1
      ```

   2. 然后，运行以下 PowerShell cmdlet：

      ```powershell  
      $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
      Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
      ```

> [!NOTE]  
> 保存注册资源名称和注册令牌，供以后参考。

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>从 Azure 注册资源检索激活密钥

接下来，在 **unregister-azsenvironment** 期间从 Azure 中创建的注册资源检索激活密钥。

若要获取激活密钥，请运行以下 PowerShell cmdlet：

```PowerShell
$RegistrationResourceName = "<unique-registration-name>"
$KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
$ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
```

> [!TIP]  
> 激活密钥保存在指定的文件中 `$KeyOutputFilePath` 。 您可以根据需要更改路径或文件名。

### <a name="create-an-activation-resource-in-azure-stack-hub"></a>在 Azure Stack 集线器中创建激活资源

返回到 Azure Stack 中心环境，其中包含通过 **AzsActivationKey** 创建的激活密钥中的文件或文本。 接下来，使用该激活密钥在 Azure Stack 集线器中创建激活资源。 若要创建激活资源，请运行以下 PowerShell cmdlet：

```PowerShell
$ActivationKey = "<activation key>"
New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
```

（可选）可以使用 " **获取内容** " cmdlet 指向包含注册令牌的文件：

```PowerShell
$ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
```

## <a name="verify-azure-stack-hub-registration"></a>验证 Azure Stack Hub 注册

可以使用“区域管理”磁贴验证 Azure Stack Hub 注册是否成功。 可在管理员门户的默认仪表板上使用此磁贴。 状态可能是已注册，也可能是未注册。 如果是已注册，则还会显示用于注册 Azure Stack Hub 的 Azure 订阅 ID，以及注册资源组和名称。

1. 登录到 Azure Stack Hub 管理员门户 (`https://adminportal.local.azurestack.external`)。

2. 在“仪表板”中，选择“区域管理”。

3. 选择“属性”。 此边栏选项卡显示环境的状态和详细信息。 状态可能是“已注册”、“未注册”或“已过期”。  

   [![Azure Stack Hub 管理员门户中的“区域管理”磁贴](media/registration-tzl/admin-1-sm.png "“区域管理”磁贴")](media/registration-tzl/admin-1.png#lightbox)

   如果已注册，则属性包括：

    - **注册订阅 ID** ：已注册并与 Azure Stack Hub 关联的 Azure 订阅 ID。
    - **注册资源组** ：包含 Azure Stack Hub 资源的关联订阅中的 Azure 资源组。

4. 可以使用 Azure 门户查看 Azure Stack Hub 注册资源，然后验证注册是否成功。 使用与用于注册 Azure Stack 中心的订阅关联的帐户登录到 [Azure 门户](https://portal.azure.com/) 。 选择 " **所有资源** "，启用 " **显示隐藏的类型** " 复选框，然后选择注册名称。

5. 如果注册未成功，则必须通过 [更改用于](../../operator/azure-stack-registration.md#change-the-subscription-you-use) 解决问题的订阅重新注册。

或者，可以使用“市场管理”功能来验证注册是否成功。 如果在 **marketplace 管理** 边栏选项卡中看到 marketplace 项列表，则注册成功。 但在断开连接的环境中，无法在 Marketplace 管理中看到 Marketplace 项。

> [!NOTE]
> 完成注册后，将不再显示 "未注册的活动警告"。

> [!NOTE]
> **订阅不支持错误消息 [InvalidRegistrationToken]： BillingModel ' Custom '** ，这表明用于注册的订阅尚未获得 Azure Stack 集线器耐用或 MDC 使用的批准。 请联系 azshregistration@microsoft.com ，与要注册的产品 (Azure Stack 中心耐用或 MDC) 的类型一起批准订阅。

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 管理基础知识](azure-stack-manage-basics-tzl.md)  
