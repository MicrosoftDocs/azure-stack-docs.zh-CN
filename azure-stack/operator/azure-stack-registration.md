---
title: Azure Stack 集成系统的 Azure 注册 | Microsoft Docs
description: 介绍多节点 Azure Stack Azure 连接部署的 Azure 注册过程。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: mabrigg
ms.reviewer: brbartle
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: b70cd30653b8b324ae4d11a4a3e8aafe47d9a179
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618011"
---
# <a name="register-azure-stack-with-azure"></a>将 Azure Stack 注册到 Azure

将 Azure Stack 注册到 Azure 允许从 Azure 下载市场项并设置报告回 Microsoft 的商业数据。 注册 Azure Stack 之后，使用情况会报告给 Azure 商业组件，然后你就可以在用于注册的 Azure 计费订阅 ID 下查看它。

本文中的信息介绍了向 Azure 注册 Azure Stack 集成系统。 有关向 Azure 注册 ASDK 的信息，请参阅 ASDK 文档中的 [Azure Stack 注册](../asdk/asdk-register.md)。

> [!IMPORTANT]  
> 需要注册才能支持完整的 Azure Stack 功能，包括在市场中提供商品。 此外，如果你在使用即用即付计费模式时未注册，则将违反 Azure Stack 许可条款。 若要了解有关 Azure Stack 授权模型的详细信息，请参阅[“如何购买”页](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)。

## <a name="prerequisites"></a>必备组件

在注册之前，需要准备好以下各项：

 - 验证凭据
 - 设置 PowerShell 语言模式
 - 安装适用于 Azure Stack 的 PowerShell
 - 下载 Azure Stack 工具
 - 确定注册方案

### <a name="verify-your-credentials"></a>验证凭据

将 Azure Stack 注册到 Azure 之前，必须准备好：

- Azure 订阅的订阅 ID。 注册仅支持 EA、CSP 或 CSP 共享服务订阅。 CSP 需要确定是[使用 CSP 订阅还是使用 APSS 订阅](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription)。<br><br>若要获取该 ID，请登录到 Azure，单击“所有服务”。 然后，在“常规”类别下，选择“订阅”，单击要使用的订阅，然后可以在“概要”下找到订阅 ID。

  > [!Note]  
  > 当前不支持德国云订阅。

- 订阅所有者的帐户用户名和密码。

- 用户帐户必须有权访问 Azure 订阅并且有权在与该订阅关联的目录中创建标识应用程序和服务主体。 建议使用最低特权管理将 Azure Stack 注册到 Azure。 若要详细了解如何创建自定义角色定义，以便限制进行注册时对订阅的访问，请参阅[为 Azure Stack 创建注册角色](azure-stack-registration-role.md)。

- 已注册 Azure Stack 资源提供程序（请参阅下面的“注册 Azure Stack 资源提供程序”部分了解详细信息）。

注册后，不需要 Azure Active Directory 全局管理员权限。 但是，某些操作可能需要全局管理员凭据。 例如，资源提供程序安装程序脚本或需要授予权限的新功能。 可以临时复原帐户的全局管理员权限，也可以使用单独的全局管理员帐户（该帐户应是*默认提供程序订阅*的所有者）。

注册 Azure Stack 的用户是 Azure Active Directory 中服务主体的所有者。 只有注册了 Azure Stack 的用户可以修改 Azure Stack 注册。 如果某位非管理员用户不是注册服务主体的所有者，则该用户在尝试注册或重新注册 Azure Stack 时，可能会遇到 403 响应。 403 响应表明用户权限不足，无法完成此操作。

如果没有符合这些要求的 Azure 订阅，则可以[创建免费的 Azure 帐户此处](https://azure.microsoft.com/free/?b=17.06)。 注册 Azure Stack 不会对 Azure 订阅收取任何费用。

> [!NOTE]
> 如果有多个 Azure Stack，最佳做法是将每个 Azure Stack 注册到其自己的订阅。 这样可以更容易地跟踪使用情况。

### <a name="powershell-language-mode"></a>PowerShell 语言模式

若要成功注册 Azure Stack，必须将 PowerShell 语言模式设置为 **FullLanguageMode**。  若要验证当前的语言模式是否设置为 Full，请打开权限提升的 PowerShell 窗口，并运行以下 PowerShell cmdlet：

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

确保输出返回的是 **FullLanguageMode**。 如果返回了其他任何语言模式，则需要在另一台计算机上运行注册，或者将语言模式设置为 **FullLanguageMode**，然后才能继续。

### <a name="install-powershell-for-azure-stack"></a>安装适用于 Azure Stack 的 PowerShell

使用适用于 Azure Stack 的最新 PowerShell 来注册到 Azure。

如果最新版本尚未安装，请参阅[安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。

### <a name="download-the-azure-stack-tools"></a>下载 Azure Stack 工具

Azure Stack 工具 GitHub 存储库包含支持 Azure Stack 功能（包括注册功能）的 PowerShell 模块。 在注册过程中，需要导入并使用 Azure Stack 工具存储库中的 **RegisterWithAzure.psm1** PowerShell 模块，将 Azure Stack 实例注册到 Azure。

若要确保使用最新版本，应在注册到 Azure 之前删除 Azure Stack 工具的任何现有版本，然后[从 GitHub 下载最新版本](azure-stack-powershell-download.md)。

### <a name="determine-your-registration-scenario"></a>确定注册方案

Azure Stack 部署可能处于“已连接”或“已断开连接”状态。

 - **已连接**  
 “已连接”意味着 Azure Stack 已部署，因此可以连接到 Internet 和 Azure。 可以将 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 用于标识存储。 对于已连接的部署，可供选择的有两种计费模型：即用即付或基于容量。
    - [使用**即用即付**计费模型将连接的 Azure Stack 注册到 Azure](#register-connected-with-pay-as-you-go-billing)
    - [使用**容量**计费模型将连接的 Azure Stack 注册到 Azure](#register-connected-with-capacity-billing)

 - **已断开连接**  
 使用从 Azure 部署断开连接选项，可以在没有 Internet 连接的情况下部署和使用 Azure Stack。 但是，使用断开连接部署，你将受限于一个 AD FS 标识存储和基于容量的计费模型。
    - [使用**容量**计费模型注册已断开连接的 Azure Stack](#register-disconnected-with-capacity-billing)

### <a name="determine-a-unique-registration-name-to-use"></a>确定要使用的唯一注册名称 
将 Azure Stack 注册到 Azure 时，必须提供唯一的注册名称。 将 Azure Stack 订阅与 Azure 注册关联的简便方法是使用 Azure Stack **云 ID**。 

> [!NOTE]
> 使用基于容量的计费模型的 Azure Stack 注册将需要在这些年度订阅到期后重新注册时更改唯一名称，除非你[删除过期的注册](azure-stack-registration.md#change-the-subscription-you-use)并重新注册到 Azure。

若要确定 Azure Stack 部署的云 ID，请在可以访问特权终结点的计算机上以管理员身份打开 PowerShell，运行以下命令，并记录 **CloudID** 值： 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: Get-AzureStackStampInformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>使用即用即付计费模型注册连接的 Azure Stack

执行以下步骤，使用即用即付计费模式将 Azure Stack 注册到 Azure。

> [!Note]  
> 所有这些步骤必须在可以访问特权终结点 (PEP) 的计算机上运行。 有关 PEP 的详细信息，请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。

连接的环境可以访问 Internet 和 Azure。 对于这些环境，需将 Azure Stack 资源提供程序注册到 Azure，然后配置计费模式。

1. 若要向 Azure 注册 Azure Stack 资源提供程序，请以管理员身份启动 PowerShell ISE，然后使用以下 PowerShell cmdlet，并将 **EnvironmentName** 参数设置为相应的 Azure 订阅类型（请参阅下面的“参数”）。

2. 添加用于注册 Azure Stack 的 Azure 帐户。 若要添加该帐户，请运行 **Add-AzureRmAccount** cmdlet。 系统会提示你输入 Azure 帐户凭据，您可能必须使用双重身份验证，具体情况视你的帐户的配置。

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | 参数 | 描述 |  
   |-----|-----|
   | EnvironmentName | Azure 云订阅环境名称。 受支持的环境名称**AzureCloud**， **AzureUSGovernment**，或使用中国 Azure 订阅，如果**AzureChinaCloud**。  |

3. 如果有多个订阅，请运行以下命令，选择要使用的那个订阅：  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. 运行以下命令，在 Azure 订阅中注册 Azure Stack 资源提供程序：

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 以管理员身份启动 PowerShell ISE，并导航到下载 Azure Stack 工具时所创建的 **AzureStack-Tools-master** 目录中的 **Registration** 文件夹。 使用 PowerShell 导入 **RegisterWithAzure.psm1** 模块：

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. 接下来，在同一个 PowerShell 会话中，确保已登录到正确的 Azure PowerShell 上下文。 这是前面在注册 Azure Stack 资源提供程序时所用的 Azure 帐户。 要运行的 Powershell：

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | 参数 | 描述 |  
   |-----|-----|
   | EnvironmentName | Azure 云订阅环境名称。 受支持的环境名称**AzureCloud**， **AzureUSGovernment**，或使用中国 Azure 订阅，如果**AzureChinaCloud**。  |

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

   该过程需要花费 10 到 15 分钟。 命令完成后，会显示以下消息：“现已使用提供的参数注册并激活环境”。

## <a name="register-connected-with-capacity-billing"></a>使用容量计费模型注册连接的 Azure Stack

执行以下步骤，使用即用即付计费模式将 Azure Stack 注册到 Azure。

> [!Note]  
> 所有这些步骤必须在可以访问特权终结点 (PEP) 的计算机上运行。 有关 PEP 的详细信息，请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。

连接的环境可以访问 Internet 和 Azure。 对于这些环境，需将 Azure Stack 资源提供程序注册到 Azure，然后配置计费模式。

1. 若要向 Azure 注册 Azure Stack 资源提供程序，请以管理员身份启动 PowerShell ISE，然后使用以下 PowerShell cmdlet，并将 **EnvironmentName** 参数设置为相应的 Azure 订阅类型（请参阅下面的“参数”）。

2. 添加用于注册 Azure Stack 的 Azure 帐户。 若要添加该帐户，请运行 **Add-AzureRmAccount** cmdlet。 系统会提示你输入 Azure 帐户凭据，您可能必须使用双重身份验证，具体情况视你的帐户的配置。

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | 参数 | 描述 |  
   |-----|-----|
   | EnvironmentName | Azure 云订阅环境名称。 受支持的环境名称**AzureCloud**， **AzureUSGovernment**，或使用中国 Azure 订阅，如果**AzureChinaCloud**。  |

3. 如果有多个订阅，请运行以下命令，选择要使用的那个订阅：  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. 运行以下命令，在 Azure 订阅中注册 Azure Stack 资源提供程序：

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. 以管理员身份启动 PowerShell ISE，并导航到下载 Azure Stack 工具时所创建的 **AzureStack-Tools-master** 目录中的 **Registration** 文件夹。 使用 PowerShell 导入 **RegisterWithAzure.psm1** 模块：

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

## <a name="register-disconnected-with-capacity-billing"></a>使用容量计费模型注册断开连接的 Azure Stack

若要在离线环境（未建立 Internet 连接）中注册 Azure Stack，需要从 Azure Stack 环境获取注册令牌，然后在可连接到 Azure 并已安装适用于 Azure Stack 的 PowerShell 的计算机上使用该令牌。  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>从 Azure Stack 环境获取注册令牌

1. 以管理员身份启动 PowerShell ISE，并导航到下载 Azure Stack 工具时所创建的 **AzureStack-Tools-master** 目录中的 **Registration** 文件夹。 导入 **RegisterWithAzure.psm1** 模块：  

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

3. 保存此注册令牌，以便在连接 Azure 的计算机上使用。 可以从 $FilePathForRegistrationToken 复制文件或文本。

### <a name="connect-to-azure-and-register"></a>连接到 Azure 并注册

在连接到 Internet 的计算机上，执行相同的步骤以导入 RegisterWithAzure.psm1 模块，并登录到正确的 Azure Powershell 上下文。 然后调用 Register-AzsEnvironment。 指定可注册到 Azure 的注册令牌。 如果使用同一 Azure 订阅 ID 注册多个 Azure Stack 实例，请指定唯一的注册名称。 运行以下 cmdlet：

  ```powershell  
  $RegistrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
  ```

（可选）可以使用 Get-Content cmdlet，使之指向包含注册令牌的文件：

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

### <a name="create-an-activation-resource-in-azure-stack"></a>在 Azure Stack 中创建激活资源

使用 Get-AzsActivationKey 从创建的激活密钥中获取文件或文本后，返回到 Azure Stack 环境。 接下来，使用该激活密钥在 Azure Stack 中创建激活资源。 若要创建激活资源，请运行以下 PowerShell cmdlet： 

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

（可选）可以使用 Get-Content cmdlet，使之指向包含注册令牌的文件：

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>验证 Azure Stack 注册

可以使用“区域管理”磁贴，验证 Azure Stack 注册是否成功。 可在管理门户的默认仪表板上使用此磁贴。 状态可能是已注册，也可能是未注册。 如果是已注册，则还会显示用于注册 Azure Stack 的 Azure 订阅 ID，以及注册资源组和名称。

1. 登录到 [Azure Stack 管理门户](https://adminportal.local.azurestack.external)。

2. 在“仪表板”中，选择“区域管理”。

3. 选择“属性”。 此边栏选项卡显示环境的状态和详细信息。 状态可以是**Registered**，**未注册**，或**已过期**。

    [![“区域管理”磁贴](media/azure-stack-registration/admin1sm.png "“区域管理”磁贴")](media/azure-stack-registration/admin1.png#lightbox)

    如果已注册，则属性包括：
    
    - **注册订阅 ID**：已注册并与 Azure Stack 关联的 Azure 订阅 ID
    - **注册资源组**：包含 Azure Stack 资源的关联订阅中的 Azure 资源组。

4. 使用 Azure 门户查看 Azure Stack 应用注册。 使用已关联到用于注册 Azure Stack 的订阅的帐户登录到 Azure 门户。 切换到与 Azure Stack 关联的租户。
5. 导航到“Azure Active Directory”>“应用注册”>“查看所有应用程序”。

    ![应用注册](media/azure-stack-registration/app-registrations.png)

    Azure Stack 应用注册带有 **Azure Stack** 前缀。

或者，可以使用“市场管理”功能来验证注册是否成功。 如果在“市场管理”边栏选项卡中看到市场项列表，即表示注册成功。 但是，在离线环境中，无法在“市场管理”中看到市场项。

> [!NOTE]
> 完成注册后，将不再显示提示未注册的活动警告。 在离线场景中，即使注册成功，“市场管理”中也仍会显示一条消息，要求你注册并激活 Azure Stack。

## <a name="renew-or-change-registration"></a>续订或更改注册

### <a name="renew-or-change-registration-in-connected-environments"></a>在联网环境中续订或更改注册

你将需要更新或续订在以下情况下的注册：

- 续订基于容量的年度订阅之后。
- 更改计费模式时。
- 调整基于容量的计费（添加/删除节点）时。

#### <a name="change-the-subscription-you-use"></a>更改使用的订阅

如果你想要更改的订阅使用，必须先运行**Remove-azsregistration** cmdlet，然后，确保已登录到正确的 Azure PowerShell 上下文，并最终运行**Set-azsregistration**与任何已更改的参数包括`<billing model>`:

  ```powershell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName $RegistrationName
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>更改计费模型或功能提供方式

若要更改安装的计费模型或功能提供方式，可以调用注册函数来设置新值。 不需要先删除当前注册：

  ```powershell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>在离线环境中续订或更改注册

你将需要更新或续订在以下情况下的注册：

- 续订基于容量的年度订阅之后。
- 更改计费模式时。
- 调整基于容量的计费（添加/删除节点）时。

#### <a name="remove-the-activation-resource-from-azure-stack"></a>从 Azure Stack 中删除激活资源

首先需要从 Azure Stack 中删除激活资源，然后再删除 Azure 中的注册资源。  

若要删除 Azure Stack 中的激活资源，请在 Azure Stack 环境中运行以下 PowerShell cmdlet：  

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

### <a name="re-register-using-disconnected-steps"></a>使用适用于联网场景的步骤重新注册

现已在离线场景中完全取消注册，接下来必须重复上述步骤，在离线场景中注册 Azure Stack 环境。

### <a name="disable-or-enable-usage-reporting"></a>禁用或启用使用情况报告

对于使用容量计费模型的 Azure Stack 环境，请将 **UsageReportingEnabled** 参数与 **Set-AzsRegistration** 或 **Get-AzsRegistrationToken** cmdlet 配合使用，以便关闭使用情况报告功能。 默认情况下，Azure Stack 报告使用情况指标。 使用容量或支持断开连接的环境的操作员需关闭使用情况报告功能。

#### <a name="with-a-connected-azure-stack"></a>使用连接的 Azure Stack

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>使用断开连接的 Azure Stack

1. 若要更改注册令牌，请运行以下 PowerShell cmdlet：  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > 注册令牌保存在为 *$FilePathForRegistrationToken* 指定的文件中。 可以自行更改文件路径或文件名。

2. 保存此注册令牌，以便在连接 Azure 的计算机上使用。 可以从 $FilePathForRegistrationToken 复制文件或文本。

## <a name="move-a-registration-resource"></a>移动注册资源
对于所有环境，都支持在同一订阅下的资源组之间移动注册资源。 但是，只有当两个订阅都解析到同一个合作伙伴 ID 时，才支持在订阅之间移动注册资源。 有关将资源移到新的资源组的详细信息，请参阅[将资源移到新的资源组或订阅](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)。

## <a name="registration-reference"></a>注册参考

### <a name="set-azsregistration"></a>Set-AzsRegistration

可以使用 Set-AzsRegistration 将 Azure Stack 注册到 Azure，并启用或禁用在市场中提供商品的功能和使用情况报告功能。

若要运行此 cmdlet，需具备：
- 任何类型的全球 Azure 订阅。
- 此外还必须在登录到 Azure PowerShell 时，使用其身份为该订阅的所有者或参与者的帐户。

```powershell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| 参数 | Type | 描述 |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | 用于[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的凭据。 用户名采用 **AzureStackDomain\CloudAdmin** 格式。 |
| PrivilegedEndpoint | String | 预先配置的远程 PowerShell 控制台，提供的功能包括日志收集和其他部署后任务。 有关详细信息，请参阅[使用特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)一文。 |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | 订阅使用的计费模型。 此参数允许的值为：Capacity、PayAsYouUse 和 Development。 |
| MarketplaceSyndicationEnabled | True/False | 确定市场管理功能在门户中是否可用。 如果通过 Internet 连接进行注册，请设置为 true。 如果在断开连接的环境中进行注册，请设置为 false。 对于断开连接的注册，可以使用[脱机联合工具](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario)下载市场项。 |
| UsageReportingEnabled | True/False | 默认情况下，Azure Stack 报告使用情况指标。 使用容量或支持断开连接的环境的操作员需关闭使用情况报告功能。 此参数允许的值为：True、False。 |
| AgreementNumber | String |  |
| RegistrationName | String | 如果在多个使用同一 Azure 订阅 ID 的 Azure Stack 实例上运行注册脚本，请为注册设置唯一名称。 参数的默认值为 **AzureStackRegistration**。 但是，如果在多个 Azure Stack 实例上使用同一名称，该脚本将会失败。 |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken 根据输入参数生成注册令牌。

```powershell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| 参数 | Type | 描述 |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | 用于[访问特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)的凭据。 用户名采用 **AzureStackDomain\CloudAdmin** 格式。 |
| PrivilegedEndpoint | String |  预先配置的远程 PowerShell 控制台，提供的功能包括日志收集和其他部署后任务。 有关详细信息，请参阅[使用特权终结点](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint)一文。 |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | 订阅使用的计费模型。 此参数允许的值为：Capacity、PayAsYouUse 和 Development。 |
| MarketplaceSyndicationEnabled | True/False |  |
| UsageReportingEnabled | True/False | 默认情况下，Azure Stack 报告使用情况指标。 使用容量或支持断开连接的环境的操作员需关闭使用情况报告功能。 此参数允许的值为：True、False。 |
| AgreementNumber | String |  |

## <a name="registration-failures"></a>注册失败

在尝试注册 Azure Stack 时，可能会看到以下错误之一：
1. 无法检索 $hostName 的必需硬件信息。 请检查物理主机和连接性，然后尝试重新运行注册。

2. 无法连接到 $hostName 以获取硬件信息 - 请检查物理主机和连接性，然后尝试重新运行注册。

> 原因：这通常是因为我们尝试从主机获取硬件详细信息（例如 UUID、Bios 和 CPU）以尝试激活，但却无法完成它，因为无法连接到物理主机。

尝试访问市场管理时，会在尝试同步发布产品时出错。 
> 原因：当 Azure Stack 无法访问注册资源时，通常会发生这种情况。 一个常见的原因是 Azure 订阅的目录租户发生更改时它重置注册。 如果你已更改订阅的目录租户，则无法访问 Azure Stack marketplace 或报表使用情况。 需要重新注册才能解决此问题。

在你已经通过离线过程注册戳记的情况下，市场管理仍会要求你注册并激活 Azure Stack。 
> 原因：这是离线环境的已知问题。 可以按照[这些步骤](azure-stack-registration.md#verify-azure-stack-registration)验证注册状态。 若要使用市场管理，需使用[脱机工具](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario)。 

## <a name="next-steps"></a>后续步骤

[从 Azure 下载市场项](azure-stack-download-azure-marketplace-item.md)
