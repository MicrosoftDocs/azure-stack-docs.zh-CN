---
title: 将 Azure Stack HCI 连接到 Azure
description: 如何将 Azure Stack HCI 群集注册到 Azure。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/16/2020
ms.openlocfilehash: 95e0ed6b87fb501b31c024c5d2d886b4e1bce8ac
ms.sourcegitcommit: f30e5178e0b4be4e3886f4e9f699a2b51286e2a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97620630"
---
# <a name="connect-azure-stack-hci-to-azure"></a>将 Azure Stack HCI 连接到 Azure

> 适用于：Azure Stack HCI v20H2

根据 Azure 在线服务条款，Azure Stack HCI 作为 Azure 服务提供，需要在安装后 30 天内进行注册。 本主题介绍如何向 [Azure Arc](https://azure.microsoft.com/services/azure-arc/) 注册 Azure Stack HCI 群集，以实现监视、支持、计费和混合服务。 注册后，将创建一个 Azure 资源管理器资源来表示每个本地 Azure Stack HCI 群集，从而有效地将 Azure 管理平面扩展到 Azure Stack HCI。 信息会定期在 Azure 资源与本地群集之间同步 () 。

   > [!IMPORTANT]
   > 需要向 Azure 注册。 在将群集注册到 Azure 之前，Azure Stack HCI 操作系统未有效获得许可，不受支持，并且功能有所缩减 (例如，你无法) 创建虚拟机。

## <a name="prerequisites-for-registration"></a>注册的先决条件

在创建 Azure Stack HCI 群集之前，将无法向 Azure 注册。 为了支持群集，群集节点必须是物理服务器。 虚拟机可用于测试，但必须支持 (UEFI) 统一可扩展固件接口，这意味着不能使用 Hyper-v 第1代虚拟机。 Azure Arc 注册是 Azure Stack HCI 操作系统的本机功能，因此无需注册代理。

### <a name="internet-access"></a>Internet 访问权限

Azure Stack HCI 需要定期连接到 Azure 公有云。 如果出站连接受到外部公司防火墙或代理服务器的限制，则必须将其配置为允许对端口443的出站访问， (HTTPS) 在有限数量的众所周知的 Azure Ip 上。 有关如何准备防火墙的信息，请参阅为 [AZURE STACK HCI 配置防火墙](../concepts/configure-firewalls.md)。

   > [!NOTE]
   > 注册过程将尝试与 PowerShell 库联系以验证你是否具有所需的最新版本的 PowerShell 模块，如 Az 和 AzureAD。 尽管 PowerShell 库在 Azure 上托管，但它当前没有服务标记。 如果无法从具有出站 internet 访问权限的管理计算机上运行上述 cmdlet，则我们建议下载这些模块，并手动将它们传输到要运行该命令的群集节点 `Register-AzStackHCI` 。 或者，你可以 [在断开连接的情况下安装模块](/powershell/scripting/gallery/how-to/working-with-local-psrepositories?view=powershell-7.1#installing-powershellget-on-a-disconnected-system)。

### <a name="azure-subscription-and-permissions"></a>Azure 订阅和权限

如果还没有 Azure 帐户，请 [创建一个](https://azure.microsoft.com/)。

可以使用任何类型的现有订阅：
- [适用于学生](https://azure.microsoft.com/free/students/)或[Visual Studio 订户](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)的 Azure 额度免费帐户
- 带信用卡的即用即[付](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)订阅
- 通过企业协议 (EA) 获取的订阅
- 通过云解决方案提供商 (CSP) 计划获取的订阅

注册群集的用户必须具有 Azure 订阅权限才能执行以下操作：

- 注册资源提供程序
- 创建/获取/删除 Azure 资源和资源组

如果你的 Azure 订阅通过 EA 或 CSP，最简单的方法是请求 Azure 订阅管理员向你的订阅分配内置的 "所有者" 或 "参与者" Azure 角色。 但是，某些管理员可能更倾向于使用更严格的选项。 在这种情况下，可以通过执行以下步骤创建一个特定于 Azure Stack HCI 注册的自定义 Azure 角色：

1. 使用以下内容创建名为 **customHCIRole.js** 的 json 文件。 请确保更改 <subscriptionID> 为你的 Azure 订阅 ID。 若要获取订阅 ID，请访问 [portal.azure.com](https://portal.azure.com)，导航到 "订阅"，然后从列表中复制/粘贴你的 ID。

   ```json
   {
     "Name": "Azure Stack HCI registration role”,
     "Id": null,
     "IsCustom": true,
     "Description": "Custom Azure role to allow subscription-level access to register Azure Stack HCI",
     "Actions": [
       "Microsoft.Resources/subscriptions/resourceGroups/write",
       "Microsoft.Resources/subscriptions/resourceGroups/read",
       "Microsoft.Resources/subscriptions/resourceGroups/delete",
       "Microsoft.AzureStackHCI/register/action",
       "Microsoft.AzureStackHCI/Unregister/Action",
       "Microsoft.AzureStackHCI/clusters/*"
     ],
     "NotActions": [
     ],
   "AssignableScopes": [
       "/subscriptions/<subscriptionId>"
     ]
   }
   ```

2. 创建自定义角色：

   ```powershell
   New-AzRoleDefinition -InputFile <path to customHCIRole.json>
   ```

3. 向用户分配自定义角色：

   ```powershell
   $user = get-AzAdUser -DisplayName <userdisplayname>
   $role = Get-AzRoleDefinition -Name "Azure Stack HCI registration role"
   New-AzRoleAssignment -ObjectId $user.Id -RoleDefinitionId $role.Id -Scope /subscriptions/<subscriptionid>
   ```

### <a name="azure-active-directory-permissions"></a>Azure Active Directory 权限

还需要适当的 Azure Active Directory 权限才能完成注册过程。 如果尚未安装，请要求 Azure AD 管理员授予许可或委托给你的权限。 有关详细信息，请参阅[管理 Azure 注册](../manage/manage-azure-registration.md#azure-active-directory-app-permissions)。

## <a name="register-using-powershell"></a>使用 PowerShell 注册

使用以下过程通过管理 PC 将 Azure Stack HCI 群集注册到 Azure。

1. 在管理 PC 上安装所需的 cmdlet。 如果要注册从当前正式发行版部署的群集 (GA) Azure Stack HCI 的映像，只需运行以下命令即可。 如果你的群集是从公共预览映像部署的，请确保在尝试注册到 Azure 之前，已将2020年11月23日预览版 (KB4586852) 应用到了群集中的每个服务器。

   ```PowerShell
   Install-Module -Name Az.StackHCI
   ```

   > [!NOTE]
   > - 你可能会看到一条提示，例如“是否希望 PowerShellGet 立即安装并导入 NuGet 提供程序?”， 你应该回答“是(Y)”。
   > - 系统可能还会提示“是否确定要从 'PSGallery' 安装模块?”，你应该回答“是(Y)”。

2. 使用群集中任何服务器的名称执行注册。 若要获取 Azure 订阅 ID，请访问 [portal.azure.com](https://portal.azure.com)，导航到 "订阅"，然后从列表中复制/粘贴你的 ID。

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" -ComputerName Server1 [–Credential] [-ResourceName] [-ResourceGroupName] [-Region]
   ```

   此语法使用 Azure 资源和资源组的默认 Azure 区域和云环境，并为 Azure 资源和资源组使用智能默认名称来注册群集的 (，其中 Server1 为成员) ，当前用户为当前用户，但是，可以将参数添加到此命令以指定这些值。

   请记住，运行 `Register-AzStackHCI` cmdlet 的用户必须具有 [Azure Active Directory 权限](../manage/manage-azure-registration.md#azure-active-directory-app-permissions)，否则注册过程将不会完成；相反，它将退出并使注册挂起以等待管理员同意。 授权后，只需重新运行 `Register-AzStackHCI` 即可完成注册。

3. 使用 Azure 进行身份验证

   若要完成注册过程，需要使用 Azure 帐户进行身份验证（登录）。 帐户需要有权访问在上述第 4 步中指定的 Azure 订阅才能继续进行注册。 复制所提供的代码，在另一台设备（如电脑或手机）上导航到 microsoft.com/devicelogin，输入该代码，然后在那里登录。 这与 Microsoft 用于输入方式受限的其他设备（例如 Xbox）的体验是相同的。

注册工作流将检测到你的登录并继续完成注册。 然后，你应该能够在 Azure 门户中看到你的群集。

## <a name="next-steps"></a>后续步骤

现在可以执行以下操作：

- [验证群集](validate.md)

