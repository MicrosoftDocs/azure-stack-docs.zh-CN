---
title: 使用 PowerShell 管理 Azure Stack 中心的订阅、计划和产品/服务
description: 如何在 Azure Stack Hub 中通过 PowerShell 管理订阅、计划和产品/服务。
author: PatAltimore
ms.topic: how-to
ms.date: 12/18/2020
ms.author: patricka
ms.lastreviewed: 12/18/2020
ms.reviewer: bganapa
ms.openlocfilehash: 3dcc3eae565f2e2a41078c2696a09f60ffa618b9
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697840"
---
# <a name="use-powershell-to-manage-subscriptions-plans-and-offers-in-azure-stack-hub"></a>使用 PowerShell 管理 Azure Stack 中心的订阅、计划和产品/服务

可以通过使用产品/服务、计划和订阅来使用 PowerShell 配置和传递服务。 使用 PowerShell。 有关在 Azure Stack 集线器上设置 PowerShell 的说明，请参阅 [安装适用于 Azure Stack 中心的 Powershell Az 模块](powershell-install-az-module.md)。 有关使用 PowerShell 连接到 Azure Stack 集线器的信息，请参阅 [使用 Powershell 连接到 Azure Stack 中心](azure-stack-powershell-configure-admin.md)。

在开始之前，请验证是否已加载 Azure Stack 集线器 PowerShell 模块。 在 PowerShell 控制台中，键入 `Import-Module AzureStack` 。

## <a name="create-a-plan"></a>创建计划

创建计划时需要配额。 可以使用现有配额，也可以创建新配额。 例如，若要创建存储、计算和网络配额，可使用 [AzsStorageQuota](/powershell/module/azs.storage.admin/new-azsstoragequota)、 [AzsComputeQuota](/powershell/module/azs.compute.admin/new-azscomputequota)和 [AzsNetworkQuota](/powershell/module/azs.network.admin/new-azsnetworkquota) cmdlet：

```powershell
$serviceQuotas  = @()
$serviceQuotas += (New-AzsStorageQuota -Name "Example storage quota with defaults").Id
$serviceQuotas += (New-AzsComputeQuota -Name "Example compute quota with defaults").Id
$serviceQuotas += (New-AzsNetworkQuota -Name "Example network quota with defaults").Id
```

若要创建或更新基础计划或外接程序计划，请使用 [AzsPlan](/powershell/module/azs.subscriptions.admin/new-azsplan)。

```powershell
$testPlan = New-AzsPlan -Name "testplan" -ResourceGroupName "testrg" -QuotaIds $serviceQuotas -Description "Test plan"
```

## <a name="create-an-offer"></a>创建产品

若要创建产品/服务，请使用 [AzsOffer](/powershell/module/azs.subscriptions.admin/new-azsoffer)。

```powershell
New-AzsOffer -Name "testoffer" -ResourceGroupName "testrg" -BasePlanIds @($testPlan.Id)
```

提供产品/服务后，可以将计划添加到产品/服务。 请使用 [AzsPlanToOffer](/powershell/module/azs.subscriptions.admin/add-azsplantooffer)。 **-PlanLinkType** 参数用于区分计划类型。

```powershell
Add-AzsPlanToOffer -PlanName "addonplan" -PlanLinkType Addon -OfferName "testoffer" -ResourceGroupName "testrg" -MaxAcquisitionCount 18
```

若要更改产品/服务的状态，请使用 [AzsOffer](/powershell/module/azs.subscriptions.admin/set-azsoffer) cmdlet。

```powershell
$offer = Get-AzsAdminManagedOffer -Name "testoffer" -ResourceGroupName "testrg"
$offer.state = "Public"
$offer | Set-AzsOffer -Confirm:$false
```

## <a name="create-subscription-to-an-offer"></a>创建产品/服务的订阅

创建某个套餐后，用户需要订阅此套餐才能使用此套餐。 用户可通过两种方式订阅套餐：

* 作为云操作员，可以为用户创建订阅。 创建的订阅可用于公共和专用套餐。
* 用户可以订阅公共产品/服务。

若要为用户创建作为云操作员的订阅，请使用 [AzsUserSubscription](/powershell/module/azs.subscriptions.admin/new-azsusersubscription)。

```powershell
New-AzsUserSubscription -Owner "user@contoso.com" -DisplayName "User subscription" -OfferId "/subscriptions/<Subscription ID>/resourceGroups/testrg/providers/Microsoft.Subscriptions.Admin/offers/testoffer"
```

若要以用户身份订阅公共产品/服务，请使用 [AzsSubscription](/powershell/module/azs.subscriptions/new-azssubscription)。 *AzsSubscription* 要求连接到用户 Azure 资源管理器环境。 使用 [通过 PowerShell 连接到 Azure Stack 集线器](azure-stack-powershell-configure-admin.md) 中的步骤，但使用用户 Azure 资源管理器终结点。 例如，`Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"`。

```powershell
$testOffer = Get-AzsOffer | Where-Object Name -eq "testoffer"
New-AzsSubscription -OfferId "User subscription" -OfferId $testOffer.Id -DisplayName "My subscription"
```

## <a name="delete-quotas-plans-offers-and-subscriptions"></a>删除配额、计划、套餐和订阅

随附的 PowerShell cmdlet 可用于删除 Azure Stack 集线器配额、计划、产品和订阅。 下面显示了每个的示例。

使用 [AzsUserSubscription](/powershell/module/azs.subscriptions.admin/remove-azsusersubscription) 从产品/服务中删除订阅。

```powershell
Remove-AzsUserSubscription -TargetSubscriptionId "c90173b1-de7a-4b1d-8600-b8325ca1eab1e"
```

若要从产品/服务中删除计划，请使用 [AzsPlanFromOffer](/powershell/module/azs.subscriptions.admin/remove-azsplanfromoffer)。

```powershell
Remove-AzsPlanFromOffer -PlanName "addonplan" -PlanLinkType Addon -OfferName "testoffer" -ResourceGroupName "testrg"
Remove-AzsPlanFromOffer -PlanName "testplan" -PlanLinkType Base -OfferName "testoffer" -ResourceGroupName "testrg"
```

使用 [AzsPlan](/powershell/module/azs.subscriptions.admin/remove-azsplan) 删除计划。

```powershell
Remove-AzsPlan -Name "testplan" -ResourceGroupName "testrg"
```

使用 [AzsOffer](/powershell/module/azs.subscriptions.admin/remove-azsoffer) 删除产品/服务。

```powershell
Remove-AzsOffer -Name "testoffer" -ResourceGroupName "testrg"
```

若要删除配额，请使用 [AzsStorageQuota](/powershell/module/azs.storage.admin/remove-azsstoragequota)、 [AzsComputeQuota](/powershell/module/azs.compute.admin/remove-azscomputequota)、 [AzsNetworkQuota](/powershell/module/azs.network.admin/remove-azsnetworkquota) 。

```powershell
Remove-AzsStorageQuota -Name "Example storage quota with defaults"
Remove-AzsComputeQuota -Name "Example compute quota with defaults"
Remove-AzsNetworkQuota -Name "Example network quota with defaults"
```

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack Hub 中管理更新](./azure-stack-updates.md)
