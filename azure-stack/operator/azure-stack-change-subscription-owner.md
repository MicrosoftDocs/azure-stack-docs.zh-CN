---
title: 更改 Azure Stack Hub 用户订阅的计费所有者
description: 了解如何更改 Azure Stack Hub 用户订阅的计费所有者。
author: PatAltimore
ms.topic: conceptual
ms.date: 1/29/2021
ms.author: patricka
ms.reviewer: shnatara
ms.lastreviewed: 1/29/2021
ms.openlocfilehash: 40e4fbff8a04db2a6f2218d60d548df3c75236ba
ms.sourcegitcommit: e88f0a1f2f4ed3bb8442bfb7b754d8b3a51319b4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99533872"
---
# <a name="change-the-billing-owner-for-an-azure-stack-hub-user-subscription"></a>更改 Azure Stack Hub 用户订阅的计费所有者

Azure Stack Hub 操作员可以使用 PowerShell 更改用户订阅的计费所有者。 例如，更改所有者的一个原因是替换已离职的用户。

会将两种类型的所有者分配到订阅：

- **计费所有者**：默认情况下，计费所有者是从套餐获取订阅，然后拥有该订阅的计费关系的用户帐户。 此帐户也是订阅的管理员。 在一个订阅中，只能指定一个此类用户帐户。 计费所有者通常是组织或团队主管。

  可以使用 PowerShell cmdlet [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) 更改计费所有者。  

- **通过 RBAC 角色添加的所有者** - 可以使用 [基于角色的访问控制](azure-stack-manage-permissions.md) (RBAC) 向其他用户授予 **所有者** 角色。 可将任意数量的其他用户帐户添加为所有者，以补充计费所有者。 其他所有者也是订阅的管理员，拥有订阅的所有特权，但无权删除计费所有者。

  可以使用 PowerShell 来管理其他所有者。 有关详细信息，请参阅[此文章](/azure/role-based-access-control/role-assignments-powershell)。

## <a name="change-the-billing-owner"></a>更改计费所有者

运行以下脚本更改用户订阅的计费所有者。 用于运行该脚本的计算机必须连接到 Azure Stack Hub 并运行 Azure Stack Hub PowerShell 模块 1.3.0 或更高版本。 有关详细信息，请参阅[安装 Azure Stack Hub PowerShell](powershell-install-az-module.md)。

>[!NOTE]
>在多租户 Azure Stack Hub 中，新所有者必须与现有所有者位于同一目录中。 在将订阅所有权提供给另一个目录中的某个用户之前，必须首先[邀请该用户作为来宾加入目录](/azure/active-directory/b2b/add-users-administrator)。

运行脚本之前，请替换脚本中的以下值：

- **$ArmEndpoint**：环境的资源管理器终结点。
- **$TenantId**：租户 ID。
- **$SubscriptionId**：订阅 ID。
- **$OwnerUpn**：要添加为新账单所有者的帐户，例如 **user\@example.com**。

### <a name="az-modules"></a>[Az 模块](#tab/az)

```powershell
# Set up Azure Stack Hub admin environment
Add-AzEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Connect-AzAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

[!include[Remove Account](../includes/remove-account-az.md)]

### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm)

```powershell
# Set up Azure Stack Hub admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount  -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRMSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRMContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzureRMsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzureRMsUserSubscription -InputObject $subscription
```
[!include[Remove Account](../includes/remove-account-azurerm.md)]
---




## <a name="next-steps"></a>后续步骤

- [管理基于角色的访问控制](azure-stack-manage-permissions.md)
