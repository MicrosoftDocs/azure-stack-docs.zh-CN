---
title: 将 Azure Stack Hub 注册到 Azure
titleSuffix: Azure Stack Hub
description: 了解如何将 Azure Stack Hub 集成系统注册到 Azure，以便可以下载 Azure 市场项并设置数据报告。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 04/06/2020
ms.author: inhenkel
ms.reviewer: avishwan
ms.lastreviewed: 03/04/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 3f9741019a28548e9f20308312d62ea68e757795
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81308240"
---
# <a name="register-azure-stack-hub-with-azure"></a>将 Azure Stack Hub 注册到 Azure

将 Azure Stack 集线器注册到 Azure，以便可以从 Azure 下载 Azure Marketplace 项，并将商务数据报表设置回 Microsoft。 注册 Azure Stack Hub 之后，使用情况会报告给 Azure 商业组件，然后你就可以在用于注册的 Azure 计费订阅 ID 下查看它。

本文中的信息介绍如何向 Azure 注册 Azure Stack Hub 集成系统。 有关向 Azure 注册 ASDK 的信息，请参阅 ASDK 文档中的 [Azure Stack Hub 注册](../asdk/asdk-register.md)。

> [!IMPORTANT]  
> 需要注册才能支持完整的 Azure Stack Hub 功能，包括在市场中提供商品。 如果你在使用即用即付计费模型时未注册，则将违反 Azure Stack 集线器许可条款。 若要详细了解 Azure Stack 中心许可模式，请参阅[如何购买页](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)。

## <a name="prerequisites"></a>必备条件

在注册之前，请先完成以下先决条件：

- 验证凭据。
- 设置 PowerShell 语言模式。
- 为 Azure Stack 集线器安装 PowerShell。
- 下载 Azure Stack 中心工具。
- 确定计费模式。
- 确定唯一的注册名称。

### <a name="verify-your-credentials"></a>验证凭据

在向 Azure 注册 Azure Stack 集线器之前，必须具备：

- Azure 订阅的订阅 ID。 注册仅支持 EA、CSP 或 CSP 共享服务订阅。 CSP 需要确定是[使用 CSP 订阅还是使用 APSS 订阅](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)。<br><br>若要获取该 ID，请登录到 Azure，单击“所有服务”****。 然后，在“常规”**** 类别下，选择“订阅”，单击要使用的订阅，然后可以在“概要”下找到订阅 ID。******** 最佳做法是，对生产环境和开发环境或测试环境使用单独的订阅。 

  > [!Note]  
  > 目前尚不支持德国云订阅。

- 订阅所有者的帐户用户名和密码。

- 用户帐户必须有权访问 Azure 订阅并且有权在与该订阅关联的目录中创建标识应用和服务主体。 建议使用最小特权管理将 Azure Stack 集线器注册到 Azure。 有关如何创建将访问权限限制为注册订阅的自定义角色定义的详细信息，请参阅为[Azure Stack 中心创建注册角色](azure-stack-registration-role.md)。

- 已注册 Azure Stack 集线器资源提供程序（有关详细信息，请参阅以下注册 Azure Stack 中心资源提供程序部分）。

注册后，不需要 Azure Active Directory (Azure AD) 全局管理员权限。 但是，某些操作可能需要全局管理员凭据（例如，资源提供程序安装程序脚本或需要授予权限的新功能）。 可以暂时恢复帐户的全局管理员权限，也可以使用*默认提供程序订阅*所有者的单独全局管理员帐户。

注册 Azure Stack 中心的用户是 Azure AD 中服务主体的所有者。 只有注册 Azure Stack 中心的用户可以修改 Azure Stack 中心注册。 如果非管理员用户不是注册服务主体的所有者尝试注册或重新注册 Azure Stack 集线器，则这些用户可能会遇到403响应。 403 响应表明用户权限不足，无法完成此操作。

如果没有满足这些要求的 Azure 订阅，可以在[此处创建一个免费的 azure 帐户](https://azure.microsoft.com/free/?b=17.06)。 注册 Azure Stack 中心将不会对 Azure 订阅产生任何费用。

> [!NOTE]
> 如果有多个 Azure Stack 集线器，最佳做法是将每个 Azure Stack 中心注册到其自己的订阅。 这样可以更容易地跟踪使用情况。

### <a name="set-the-powershell-language-mode"></a>设置 PowerShell 语言模式

若要成功注册 Azure Stack 集线器，PowerShell 语言模式必须设置为**FullLanguageMode**。  若要验证当前的语言模式是否设置为 Full，请打开权限提升的 PowerShell 窗口，并运行以下 PowerShell cmdlet：

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

确保输出返回的是 **FullLanguageMode**。 如果返回了其他任何语言模式，则需要在另一台计算机上运行注册，或者将语言模式设置为 **FullLanguageMode**，然后才能继续。

### <a name="install-powershell-for-azure-stack-hub"></a>为 Azure Stack 集线器安装 PowerShell

使用最新的 PowerShell 进行 Azure Stack 中心向 Azure 注册。

如果尚未安装最新版本，请参阅[为 Azure Stack 集线器安装 PowerShell](azure-stack-powershell-install.md)。

### <a name="download-the-azure-stack-hub-tools"></a>下载 Azure Stack 中心工具

Azure Stack 集线器工具 GitHub 存储库包含支持 Azure Stack 集线器功能的 PowerShell 模块，包括注册功能。 注册过程中，需要导入并使用**registerwithazure.psm1 Hbase-runner.psm1** PowerShell 模块（在 Azure Stack 集线器工具存储库中找到），以便向 Azure 注册你的 Azure Stack 中心实例。

若要确保使用最新版本，请删除现有版本的 Azure Stack 中心工具，并[从 GitHub 下载最新版本](azure-stack-powershell-download.md)，然后再向 Azure 注册。

### <a name="determine-your-billing-model"></a>确定计费模式
::: zone pivot="state-connected"
 连接的部署允许 Azure Stack 集线器连接到 internet 和 Azure。 你还可以使用 Azure AD 或 Active Directory 联合身份验证服务（AD FS）作为标识存储，并从两种计费模型中进行选择： "即用即付" 或 "基于容量"。 稍后在运行注册脚本时指定计费模型。
::: zone-end

::: zone pivot="state-disconnected"
 通过断开连接的部署，无需连接到 internet 即可使用 Azure Stack 中心。 对于断开连接的部署，只能使用 AD FS 的标识存储和基于容量的计费模型。 稍后在运行注册脚本时指定计费模型。
::: zone-end

### <a name="determine-your-unique-registration-name"></a>确定唯一的注册名称

运行注册脚本时，必须提供唯一的注册名称。 将 Azure Stack 中心订阅与 Azure 注册关联的一种简单方法是使用你的 Azure Stack 中心**云 ID**。

> [!NOTE]
> 使用基于容量的计费模式 Azure Stack 集线器注册将需要在这些年度订阅过期后重新注册，除非[删除已过期的注册](#renew-or-change-registration)并重新注册到 Azure。

若要确定 Azure Stack 中心部署的云 ID，请在可以访问特权终结点的计算机上以管理员身份打开 PowerShell，运行以下命令，然后记录**CloudID**值：

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: Get-AzureStackStampInformation
```

::: zone pivot="state-connected"
## <a name="register-with-pay-as-you-use-billing"></a>使用即用即付计费注册

使用以下步骤，通过即用即付计费模型将 Azure Stack 集线器注册到 Azure。

> [!Note]  
> 所有这些步骤必须在可以访问特权终结点 (PEP) 的计算机上运行。 有关 PEP 的详细信息，请参阅[使用 Azure Stack 集线器中的特权终结点](azure-stack-privileged-endpoint.md)。

连接的环境可以访问 Internet 和 Azure。 对于这些环境，需要将 Azure Stack 集线器资源提供程序注册到 Azure，然后配置计费模型。

1. 若要向 Azure 注册 Azure Stack 集线器资源提供程序，请以管理员身份启动 PowerShell ISE，并使用以下 PowerShell cmdlet，并将**EnvironmentName**参数设置为适当的 Azure 订阅类型（请参阅下面的参数）。

2. 添加用于注册 Azure Stack 集线器的 Azure 帐户。 若要添加该帐户，请运行 **Add-AzureRmAccount** cmdlet。 系统会提示输入 Azure 帐户凭据。根据帐户的配置，可能需要使用双因素身份验证。

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | 参数 | 说明 |  
   |-----|-----|
   | EnvironmentName | Azure 云订阅环境名称。 支持的环境名称为**AzureCloud**、 **AzureUSGovernment**或使用中国 Azure 订阅**AzureChinaCloud**。  |

   >[!Note]
   > 如果会话过期，密码已更改，或者只是希望切换帐户，请在使用 Add-AzureRmAccount 登录之前运行以下 cmdlet：`Remove-AzureRmAccount-Scope Process`

3. 如果有多个订阅，请运行以下命令，选择要使用的那个订阅：  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. 运行以下命令，在 Azure 订阅中注册 Azure Stack 集线器资源提供程序：

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 以管理员身份启动 PowerShell ISE，并导航到下载 Azure Stack 集线器工具时创建的**test-azurestack**目录中的**注册**文件夹。 使用 PowerShell 导入 **RegisterWithAzure.psm1** 模块：

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. 接下来，在同一个 PowerShell 会话中，确保已登录到正确的 Azure PowerShell 上下文。 此上下文是以前用于注册 Azure Stack 中心资源提供程序的 Azure 帐户。 要运行的 Powershell：

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | 参数 | 说明 |  
   |-----|-----|
   | EnvironmentName | Azure 云订阅环境名称。 支持的环境名称为**AzureCloud**、 **AzureUSGovernment**或使用中国 Azure 订阅**AzureChinaCloud**。  |

7. 在同一个 PowerShell 会话中运行 **Set-AzsRegistration** cmdlet。 要运行的 PowerShell：  

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   有关 Set-AzsRegistration cmdlet 的详细信息，请参阅[注册参考](#registration-reference)。

   该过程需要花费 10 到 15 分钟。 命令完成后，会显示以下消息：“现已使用提供的参数注册并激活环境”。****

## <a name="register-with-capacity-billing"></a>向容量计费注册

使用以下步骤将 Azure Stack 集线器注册到使用容量计费模型的 Azure。

> [!Note]  
> 所有这些步骤必须在可以访问特权终结点 (PEP) 的计算机上运行。 有关 PEP 的详细信息，请参阅[使用 Azure Stack 集线器中的特权终结点](azure-stack-privileged-endpoint.md)。

连接的环境可以访问 Internet 和 Azure。 对于这些环境，需要将 Azure Stack 集线器资源提供程序注册到 Azure，然后配置计费模型。

1. 若要向 Azure 注册 Azure Stack 集线器资源提供程序，请以管理员身份启动 PowerShell ISE，并使用以下 PowerShell cmdlet，并将**EnvironmentName**参数设置为适当的 Azure 订阅类型（请参阅下面的参数）。

2. 添加用于注册 Azure Stack 集线器的 Azure 帐户。 若要添加该帐户，请运行 **Add-AzureRmAccount** cmdlet。 系统会提示输入 Azure 帐户凭据。根据帐户的配置，可能需要使用双因素身份验证。

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | 参数 | 说明 |  
   |-----|-----|
   | EnvironmentName | Azure 云订阅环境名称。 支持的环境名称为**AzureCloud**、 **AzureUSGovernment**或使用中国 Azure 订阅**AzureChinaCloud**。  |

3. 如果有多个订阅，请运行以下命令，选择要使用的那个订阅：  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. 运行以下命令，在 Azure 订阅中注册 Azure Stack 集线器资源提供程序：

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 以管理员身份启动 PowerShell ISE，并导航到下载 Azure Stack 集线器工具时创建的**test-azurestack**目录中的**注册**文件夹。 使用 PowerShell 导入 **RegisterWithAzure.psm1** 模块：

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
   ```
   > [!Note]  
   > 可以通过将参数设置为 false 来使用 **Set-AzsRegistration** cmdlet 的 UsageReportingEnabled 参数禁用使用情况报告。 
   
   有关 Set-AzsRegistration cmdlet 的详细信息，请参阅[注册参考](#registration-reference)。
::: zone-end

::: zone pivot="state-disconnected"
## <a name="register-with-capacity-billing"></a>向容量计费注册

如果要在断开连接的环境（无 internet 连接）中注册 Azure Stack 集线器，则需要从 Azure Stack 中心环境中获取注册令牌。 然后在可以连接到 Azure 并已安装 Azure Stack 集线器的 PowerShell 的计算机上使用该令牌。  

### <a name="get-a-registration-token-from-the-azure-stack-hub-environment"></a>从 Azure Stack 中心环境获取注册令牌

1. 以管理员身份启动 PowerShell ISE，并导航到下载 Azure Stack 集线器工具时创建的**test-azurestack**目录中的**注册**文件夹。 导入 **RegisterWithAzure.psm1** 模块：  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. 若要获取注册令牌，请运行以下 PowerShell cmdlet：  

   ```Powershell
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   有关 Get-AzsRegistrationToken cmdlet 的详细信息，请参阅[注册参考](#registration-reference)。

   > [!Tip]  
   > 注册令牌保存在为 *$FilePathForRegistrationToken* 指定的文件中。 可以自行更改文件路径或文件名。

3. 保存此注册令牌，以便在连接 Azure 的计算机上使用。 可以从 *$FilePathForRegistrationToken* 复制文件或文本。

### <a name="connect-to-azure-and-register"></a>连接到 Azure 并注册

在连接到 Internet 的计算机上，执行相同的步骤以导入 RegisterWithAzure.psm1 模块，并登录到正确的 Azure Powershell 上下文。 然后调用 Register-AzsEnvironment。 指定可注册到 Azure 的注册令牌。 如果要使用相同的 Azure 订阅 ID 注册 Azure Stack 中心的多个实例，请指定唯一的注册名称。

需要注册令牌和唯一令牌名称。

1. 以管理员身份启动 PowerShell ISE，并导航到下载 Azure Stack 集线器工具时创建的**test-azurestack**目录中的**注册**文件夹。 导入 **RegisterWithAzure.psm1** 模块：  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. 然后运行以下 PowerShell cmdlet：  

    ```powershell  
    $RegistrationToken = "<Your Registration Token>"
    $RegistrationName = "<unique-registration-name>"
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

（可选）可以使用 Get-Content cmdlet 指向包含注册令牌的文件。

需要注册令牌和唯一令牌名称。

1. 以管理员身份启动 PowerShell ISE，并导航到下载 Azure Stack 集线器工具时创建的**test-azurestack**目录中的**注册**文件夹。 导入 **RegisterWithAzure.psm1** 模块：  

    ```powershell  
    Import-Module .\RegisterWithAzure.psm1
    ```

2. 然后运行以下 PowerShell cmdlet：  

    ```powershell  
    $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

  > [!Note]  
  > 保存注册资源名称和注册令牌，供以后参考。

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>从 Azure 注册资源检索激活密钥

接下来，需要在执行 Register-AzsEnvironment 期间，从 Azure 中创建的注册资源检索激活密钥。

若要获取激活密钥，请运行以下 PowerShell cmdlet：  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > 激活密钥保存在为 *$KeyOutputFilePath* 指定的文件中。 可以自行更改文件路径或文件名。

### <a name="create-an-activation-resource-in-azure-stack-hub"></a>在 Azure Stack 集线器中创建激活资源

返回到 Azure Stack 中心环境，其中包含从 AzsActivationKey 创建的激活密钥中的文件或文本。 接下来，使用该激活密钥在 Azure Stack 集线器中创建激活资源。 若要创建激活资源，请运行以下 PowerShell cmdlet：

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

（可选）可以使用 Get-Content cmdlet 指向包含注册令牌的文件：

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```
::: zone-end

## <a name="verify-azure-stack-hub-registration"></a>验证 Azure Stack 集线器注册

你可以使用 "**区域管理**" 磁贴来验证 Azure Stack 集线器注册是否成功。 可在管理员门户的默认仪表板上使用此磁贴。 状态可能是已注册，也可能是未注册。 如果已注册，还会显示用于向注册资源组和名称注册 Azure Stack 中心的 Azure 订阅 ID。

1. 登录到[Azure Stack 中心管理员门户](https://adminportal.local.azurestack.external)。

2. 在“仪表板”中，选择“区域管理”。****

3. 选择“属性”  。 此边栏选项卡显示环境的状态和详细信息。 状态可能是“已注册”、“未注册”或“已过期”。************

    [![Azure Stack 中心管理员门户中的 "区域管理" 磁贴](media/azure-stack-registration/admin1sm.png "“区域管理”磁贴")](media/azure-stack-registration/admin1.png#lightbox)

    如果已注册，则属性包括：
    
    - **注册订阅 id**：注册并关联到 Azure Stack 中心的 AZURE 订阅 id。
    - **注册资源组**：关联订阅中包含 Azure Stack 中心资源的 Azure 资源组。

4. 你可以使用 Azure 门户查看 Azure Stack 中心注册资源，然后验证注册是否成功。 使用与用于注册 Azure Stack 中心的订阅关联的帐户登录到[Azure 门户](https://portal.azure.com)。 选择“所有资源”****，启用“显示隐藏类型”**** 复选框，然后选择注册名称。

5. 如果注册未成功，则必须按照[此处的步骤](#change-the-subscription-you-use)重新注册以解决问题。  

或者，可以使用“市场管理”功能来验证注册是否成功。 如果在“市场管理”边栏选项卡中看到市场项列表，即表示注册成功。 但是，在离线环境中，无法在“市场管理”中看到市场项。

> [!NOTE]
> 完成注册后，将不再显示有关未注册的活动警告。 在1904之前的 Azure Stack 集线器版本中，在断开连接的情况下，会在 Marketplace 管理中看到一条消息，要求注册并激活你的 Azure Stack 中心，即使你已成功注册也是如此。 此消息不会出现在 1904 版和更高版本中。

## <a name="renew-or-change-registration"></a>续订或更改注册

::: zone pivot="state-connected"
在以下情况下，需要更新或续订注册：

- 续订基于容量的年度订阅之后。
- 更改计费模式时。
- 调整基于容量的计费（添加/删除节点）时。

### <a name="change-the-subscription-you-use"></a>更改使用的订阅

若要更改使用的订阅，必须先运行 **Remove-AzsRegistration** cmdlet，并确保登录到正确的 Azure PowerShell 上下文。 然后结合任何已更改的参数（包括 `<billing model>`）运行 **Set-AzsRegistration**：

  ```powershell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName $RegistrationName
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="change-the-billing-model-or-how-to-offer-features"></a>更改计费模型或功能提供方式

若要更改安装的计费模型或功能提供方式，可以调用注册函数来设置新值。 不需要先删除当前注册。

  ```powershell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```
::: zone-end

::: zone pivot="state-disconnected"
在以下情况下，需要更新或续订注册：

- 续订基于容量的年度订阅之后。
- 更改计费模式时。
- 调整基于容量的计费（添加/删除节点）时。

### <a name="remove-the-activation-resource-from-azure-stack-hub"></a>从 Azure Stack 中心删除激活资源

首先需要从 Azure Stack 中心删除激活资源，然后从 Azure 中删除注册资源。  

若要删除 Azure Stack 集线器中的激活资源，请在 Azure Stack 中心环境中运行以下 PowerShell cmdlet：  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

接下来，若要删除 Azure 中的注册资源，请确保计算机已连接到 Azure，登录到正确的 Azure PowerShell 上下文，并运行相应的 PowerShell cmdlet，如下所示。

可以使用用于创建资源的注册令牌：  

  ```Powershell
  $RegistrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $RegistrationToken
  ```

或者，可以使用注册名称：

  ```Powershell
  $RegistrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $RegistrationName
  ```

### <a name="re-register-using-connected-steps"></a>使用连接的步骤重新注册

如果将计费模型从 "已断开连接" 状态的 "容量计费" 更改为 "已连接" 状态下的消耗计费，则你将在[连接的模型步骤](azure-stack-registration.md?pivots=state-connected#change-the-billing-model-or-how-to-offer-features)之后重新注册。 

>[!Note] 
>这不会更改你的标识模型，只更改计费机制，你仍将使用 ADFS 作为标识源。

### <a name="re-register-using-disconnected-steps"></a>使用适用于联网场景的步骤重新注册

你现在已在断开连接的情况下完全取消注册，并且必须重复执行在断开连接的情况下注册 Azure Stack 集线器环境的步骤。
::: zone-end

### <a name="disable-or-enable-usage-reporting"></a>禁用或启用使用情况报告

对于使用容量计费模型的 Azure Stack 集线器环境，请使用**set-azsregistration**或**AzsRegistrationToken** cmdlet 关闭使用**UsageReportingEnabled**参数的使用情况报告。 默认情况下，Azure Stack 中心报告使用情况指标。 使用容量或支持断开连接的环境的操作员需关闭使用情况报告功能。

::: zone pivot="state-connected"
运行以下 PowerShell cmdlet：

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```
::: zone-end
::: zone pivot="state-disconnected"
1. 若要更改注册令牌，请运行以下 PowerShell cmdlet：  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > 注册令牌保存在为 *$FilePathForRegistrationToken* 指定的文件中。 可以自行更改文件路径或文件名。

2. 保存此注册令牌，以便在连接 Azure 的计算机上使用。 可以从 *$FilePathForRegistrationToken* 复制文件或文本。
::: zone-end

## <a name="move-a-registration-resource"></a>移动注册资源

对于所有环境，都支持在同一订阅下的资源组之间移动注册资源****。 但是，只有当两个订阅都解析到同一个合作伙伴 ID 时，才支持在订阅之间移动注册资源。 有关将资源移到新的资源组的详细信息，请参阅[将资源移到新的资源组或订阅](/azure/azure-resource-manager/resource-group-move-resources)。

> [!IMPORTANT]
> 为了防止意外删除门户上的注册资源，注册脚本会自动向资源添加一个锁。 在移动或删除该资源之前，必须先删除此锁。 建议你向注册资源添加锁，以防止意外删除。

## <a name="registration-reference"></a>注册参考

### <a name="set-azsregistration"></a>Set-AzsRegistration

可以使用**set-azsregistration**将 Azure Stack 集线器注册到 Azure，并启用或禁用 marketplace 中的产品和使用情况报告。

若要运行此 cmdlet，需具备：

- 任何类型的全球 Azure 订阅。
- 使用其身份为该订阅的所有者或参与者的帐户登录到 Azure PowerShell。

```powershell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| 参数 | 类型 | 说明 |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | 用于[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的凭据。 用户名采用 **AzureStackDomain\CloudAdmin** 格式。 |
| PrivilegedEndpoint | 字符串 | 预先配置的远程 PowerShell 控制台，提供日志收集和其他部署后任务等功能。 有关详细信息，请参阅[使用特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)一文。 |
| AzureContext | PSObject |  |
| ResourceGroupName | 字符串 |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | 订阅使用的计费模式。 此参数允许的值：Capacity、PayAsYouUse 和 Development。 |
| MarketplaceSyndicationEnabled | True/False | 确定市场管理功能在门户中是否可用。 如果通过 Internet 连接进行注册，请设置为 true。 如果在断开连接的环境中进行注册，请设置为 false。 对于断开连接的注册，可以使用[脱机联合工具](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario)下载市场项。 |
| UsageReportingEnabled | True/False | 默认情况下，Azure Stack 中心报告使用情况指标。 使用容量或支持断开连接的环境的操作员需关闭使用情况报告功能。 此参数的允许值：True、False。 |
| AgreementNumber | 字符串 | 为此 Azure Stack 的容量 SKU 排序所依据的 EA 协议号码。 |
| RegistrationName | 字符串 | 如果要使用相同的 Azure 订阅 ID 在 Azure Stack 中心的多个实例上运行注册脚本，请设置注册的唯一名称。 参数的默认值为 **AzureStackRegistration**。 但是，如果在 Azure Stack 中心的多个实例上使用相同的名称，则脚本将失败。 |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken 根据输入参数生成注册令牌。

```powershell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| 参数 | 类型 | 说明 |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | 用于[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的凭据。 用户名采用 **AzureStackDomain\CloudAdmin** 格式。 |
| PrivilegedEndpoint | 字符串 |  预先配置的远程 PowerShell 控制台，提供日志收集和其他部署后任务等功能。 有关详细信息，请参阅[使用特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)一文。 |
| AzureContext | PSObject |  |
| ResourceGroupName | 字符串 |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | 订阅使用的计费模式。 此参数允许的值：Capacity、PayAsYouUse 和 Development。 |
| MarketplaceSyndicationEnabled | True/False |  |
| UsageReportingEnabled | True/False | 默认情况下，Azure Stack 中心报告使用情况指标。 使用容量或支持断开连接的环境的操作员需关闭使用情况报告功能。 此参数的允许值：True、False。 |
| AgreementNumber | 字符串 |  |

## <a name="registration-failures"></a>注册失败

尝试注册 Azure Stack 中心时，可能会出现以下错误之一：

- 无法为检索必需的`$hostName`硬件信息。 请检查物理主机和连接，然后尝试重新运行注册。

- 无法连接到`$hostName`以获取硬件信息。 请检查物理主机和连接性，然后尝试重新运行注册。

   原因：这通常是因为我们尝试从主机获取硬件详细信息（例如 UUID、Bios 和 CPU）以尝试激活，但却无法完成它，因为无法连接到物理主机。

- Cloud identifier [`GUID`] 已经注册。 不允许重复使用云标识符。

   原因：如果你的 Azure Stack 环境已经注册，则会发生这种情况。 如果要使用不同的订阅或计费模式重新注册您的环境，请按照[续订或更改注册步骤](#renew-or-change-registration)操作。

- 尝试访问市场管理时，会在尝试同步发布产品时出错。

   原因：当 Azure Stack 集线器无法访问注册资源时，通常会发生这种情况。 这种情况的一种常见原因是，当 Azure 订阅的目录租户更改时，它会重置注册。 如果更改了订阅的目录租户，则无法访问 Azure Stack 中心市场或报告使用情况。 需要重新注册才能解决此问题。
::: zone pivot="state-disconnected"
- 即使已使用断开连接的进程注册了 stamp，Marketplace 管理仍会要求注册并激活 Azure Stack 中心。

   原因：这是已断开连接的环境的已知问题，需要[验证注册状态](#verify-azure-stack-hub-registration)。 若要使用 Marketplace 管理，请使用[脱机工具](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario)。
::: zone-end

## <a name="next-steps"></a>后续步骤

[从 Azure 下载市场项](azure-stack-download-azure-marketplace-item.md)