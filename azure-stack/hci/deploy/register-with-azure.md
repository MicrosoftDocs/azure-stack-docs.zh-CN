---
title: 将 Azure Stack HCI 连接到 Azure
description: 如何使用 Azure 注册 Azure Stack HCI。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/17/2020
ms.openlocfilehash: 7a98692fb29b1a539bf9f9fd26e0a3e226cb8669
ms.sourcegitcommit: 2562b86f47db20e2652d4636227afb9cfd0e03ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94785768"
---
# <a name="connect-azure-stack-hci-to-azure"></a>将 Azure Stack HCI 连接到 Azure

> 适用于：Azure Stack HCI v20H2

根据 Azure 在线服务条款，Azure Stack HCI 作为 Azure 服务提供，需要在安装后 30 天内进行注册。 本主题介绍如何向 [Azure Arc](https://azure.microsoft.com/services/azure-arc/) 注册 Azure Stack HCI 群集，以实现监视、支持、计费和混合服务。 注册后，将创建一个 Azure 资源管理器资源来表示每个本地 Azure Stack HCI 群集，从而有效地将 Azure 管理平面扩展到 Azure Stack HCI。 信息会定期在 Azure 资源和本地群集之间进行同步。 

## <a name="prerequisites-for-registration"></a>注册的先决条件

在创建 Azure Stack HCI 群集之前，你将无法注册到 Azure。 节点可以是物理计算机或虚拟机，但它们必须 (UEFI) 统一可扩展固件接口，这意味着不能使用 Hyper-v 第1代虚拟机。 Azure Arc 注册是 Azure Stack HCI 的本机功能，因此不需要代理。

### <a name="internet-access"></a>Internet 访问权限

Azure Stack HCI 节点需要连接到云才能连接到 Azure。 例如，出站 ping 应成功：

```PowerShell
C:\> ping bing.com
```

### <a name="azure-subscription"></a>Azure 订阅

如果还没有 Azure 帐户，请 [创建一个](https://azure.microsoft.com/)。 

可以使用任何类型的现有订阅：
- [适用于学生](https://azure.microsoft.com/free/students/)或[Visual Studio 订户](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)的 Azure 额度免费帐户
- 带信用卡的即用即[付](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)订阅
- 通过企业协议 (EA) 获取的订阅
- 通过云解决方案提供商 (CSP) 计划获取的订阅

### <a name="azure-active-directory-permissions"></a>Azure Active Directory 权限

需要具有 Azure Active Directory 权限才能完成注册过程。 如果还没有这些权限，请让 Azure AD 管理员向你授予或委托这些权限。 有关详细信息，请参阅[管理 Azure 注册](../manage/manage-azure-registration.md#azure-active-directory-permissions)。

## <a name="register-using-powershell"></a>使用 PowerShell 注册

使用以下过程在 Azure 中注册 Azure Stack HCI 群集：

1. 连接到群集节点之一，具体方式为打开 PowerShell 会话并输入以下命令：

   ```PowerShell
   Enter-PSSession <server-name>
   ```

2. 安装适用于 Azure Stack HCI 的 PowerShell 模块：

   ```PowerShell
   Install-WindowsFeature RSAT-Azure-Stack-HCI
   ```

3. 安装所需的 cmdlet：

   ```PowerShell
   Install-Module -Name Az.StackHCI -RequiredVersion 0.3.1
   ```

   > [!NOTE]
   > 1. 你可能会看到一条提示，例如“是否希望 PowerShellGet 立即安装并导入 NuGet 提供程序?”， 你应该回答“是(Y)”。
   > 2. 系统可能还会提示“是否确定要从 'PSGallery' 安装模块?”，你应该回答“是(Y)”。
   > 3. 最后，你可能会假定安装整个 Az 模块将包括 StackHCI 子模块，但情况并非是这样 。 根据标准 Azure PowerShell 约定，不会自动包括预览版中的子模块，因此你需要显式请求 Az.StackHCI，如上所示。

4. 执行实际注册：

   ```PowerShell
   Register-AzStackHCI  -SubscriptionId "<subscription_ID>" [-ResourceName] [-ResourceGroupName]
   ```

   如果使用了此语法，则会以当前用户的身份在默认的 Azure 区域和云环境中使用 Azure 资源和资源组的智能默认名称注册本地群集（本地服务器是该群集的成员），不过你可以根据需要为此命令添加参数来指定这些值。

   请记住，运行 `Register-AzStackHCI` cmdlet 的用户必须具有 [Azure Active Directory 权限](../manage/manage-azure-registration.md#azure-active-directory-permissions)，否则注册过程将不会完成；相反，它将退出并使注册挂起以等待管理员同意。 授权后，只需重新运行 `Register-AzStackHCI` 即可完成注册。

5. 使用 Azure 进行身份验证

   若要完成注册过程，需要使用 Azure 帐户进行身份验证（登录）。 帐户需要有权访问在上述第 4 步中指定的 Azure 订阅才能继续进行注册。 复制所提供的代码，在另一台设备（如电脑或手机）上导航到 microsoft.com/devicelogin，输入该代码，然后在那里登录。 这与 Microsoft 用于输入方式受限的其他设备（例如 Xbox）的体验是相同的。

注册工作流将检测到你的登录并继续完成注册。 然后，你应该能够在 Azure 门户中看到你的群集。

## <a name="next-steps"></a>后续步骤

现在可以执行以下操作：

- [验证群集](validate.md)
- [创建卷](../manage/create-volumes.md)
