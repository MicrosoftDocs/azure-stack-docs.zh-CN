---
title: 管理 Azure Stack HCI 的 Azure 注册
description: 如何使用 PowerShell 管理 Azure Stack HCI 的 Azure 注册并了解注册状态。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: 696ef552dcf49f31fb613a22393617e653f7e10d
ms.sourcegitcommit: eb91a28a19a74f799b093ae2a705f7f6e4c5cd49
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87436456"
---
# <a name="manage-azure-registration"></a>管理 Azure 注册

> 适用于 Azure Stack HCI v20H2

创建 Azure Stack HCI 群集后，必须[向 Azure Arc 注册该群集](../deploy/register-with-azure.md)。群集注册后，会定期在本地群集和云之间同步信息。 本主题介绍如何了解注册状态并在可以解除群集授权时注销群集。

## <a name="understanding-registration-status"></a>了解注册状态

若要了解注册状态，请使用 `Get-AzureStackHCI` PowerShell cmdlet 和 `ClusterStatus`、`RegistrationStatus` 和 `ConnectionStatus` 属性。 例如，在安装 Azure Stack HCI 操作系统之后、创建或加入群集之前，`ClusterStatus` 属性显示为“尚未”状态：

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="创建群集前的 Azure 注册状态":::

创建群集后，只有 `RegistrationStatus` 显示“尚未”状态：

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="创建群集后的 Azure 注册状态":::

根据 Azure 在线服务条款，Azure Stack HCI 需要在安装后 30 天内进行注册。 如果在 30 天后未群集化，则 `ClusterStatus` 将显示 `OutOfPolicy`，如果 30 天后未注册，则 `RegistrationStatus` 将显示 `OutOfPolicy`。

注册群集后，可以查看 `ConnectionStatus` 和 `LastConnected` 时间（通常在最后一天内），除非群集暂时与 Internet 断开连接。 Azure Stack HCI 群集最多可以连续 30 天完全脱机运行。

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="注册后的 Azure 注册状态":::

如果超出允许的最长时间，则 `ConnectionStatus` 将显示 `OutOfPolicy`。

## <a name="azure-active-directory-permissions"></a>Azure Active Directory 权限

除了在订阅中创建 Azure 资源外，注册 Azure Stack HCI 还可以在 Azure Active Directory 租户中创建一个概念类似于用户的应用标识。 应用标识会继承群集名称。 此标识代表订阅中的 Azure Stack HCI 云服务（如果适用）执行操作。

如果运行 `Register-AzureStackHCI` 的用户是 Azure Active Directory 管理员或已被委派了足够的权限，则这一切都会自动发生，无需执行其他操作。 否则，可能需要 Azure Active Director 管理员的批准才能完成注册。 你的管理员可以向应用显式授予同意，也可以委派权限，使你可以向应用授予同意：

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="Azure Active Directory 权限和标识图" border="false":::

若要授予许可，请打开 portal.azure.com，并使用对 Azure Active Directory 具有足够权限的 Azure 帐户登录。 依次导航到“Azure Active Directory”、“应用注册”。  选择以你的群集命名的应用标识，然后导航到“API 权限”。

应用需要两种权限：

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

向 Azure Active Directory 管理员寻求批准可能需要一些时间，因此 `Register-AzureStackHCI` cmdlet 会退出，并将注册状态保持为“待管理员同意”，即完成部分注册。 授予同意后，只需重新运行 `Register-AzureStackHCI` 即可完成注册。

## <a name="unregister-azure-stack-hci-with-azure"></a>使用 Azure 注销 Azure Stack HCI

准备好解除 Azure Stack HCI 群集的授权后，请使用 `Unregister-AzStackHCI` cmdlet 进行注销。 这将停止通过 Azure Arc 进行的所有监视、支持和计费功能。将删除代表群集的 Azure 资源和 Azure Active Directory 应用标识，但不会删除该资源组，因为它可能包含其他不相关的资源。

如果在群集节点上运行 `Unregister-AzStackHCI` cmdlet，请使用以下语法并指定你的 Azure 订阅 ID 以及要注销的 Azure Stack HCI 群集的资源名称：

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

系统将提示你在另一台设备 (例如你的电脑或手机) 上访问 microsoft.com/devicelogin，输入代码，并登录到此处，以通过 Azure 进行身份验证。

如果从管理电脑运行 cmdlet，则还需要指定群集中服务器的名称：

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

将弹出一个交互式 Azure 登录窗口。 显示的确切提示将因安全设置（例如双重身份验证）而异。 按照提示进行登录。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [将 Azure Stack HCI 连接到 Azure](../deploy/register-with-azure.md)
- [使用 Azure Monitor 监视 Azure Stack HCI](azure-monitor.md)
