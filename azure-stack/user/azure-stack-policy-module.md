---
title: 使用 Azure Stack Hub 策略模块
description: 了解如何限制 Azure 订阅使其行为像 Azure Stack Hub 订阅
author: sethmanheim
ms.topic: article
ms.date: 11/22/2020
ms.author: sethm
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: 13d3e006d676e7e24f94741c59cb8837d5200d1d
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518120"
---
# <a name="manage-azure-policy-using-the-azure-stack-hub-policy-module"></a>使用 Azure Stack Hub 策略模块管理 Azure Policy

使用 Azure Stack Hub 策略模块，可为 Azure 订阅配置与 Azure Stack Hub 相同的版本控制和服务可用性。 该模块使用 [**AzPolicyDefinition**](/powershell/module/Az.resources/new-Azpolicydefinition) PowerShell Cmdlet 创建 Azure 策略，该策略限制订阅中可用的资源类型和服务。 然后，使用 [**AzPolicyAssignment**](/powershell/module/Az.resources/new-Azpolicyassignment) cmdlet 在相应的范围内创建策略分配。 配置策略后，可以使用 Azure 订阅来开发针对 Azure Stack Hub 的应用。

## <a name="install-the-module"></a>安装模块

1. 如 [安装 Azure Stack 集线器的 PowerShell](../operator/powershell-install-az-module.md)的步骤1中所述，安装 Az PowerShell 模块所需的版本。
2. [从 GitHub 下载 Azure Stack Hub 工具](../operator/azure-stack-powershell-download.md)。
3. [配置适用于 Azure Stack Hub 的 PowerShell](azure-stack-powershell-configure-user.md)。
4. 导入 AzureStack.Policy.psm1 模块：

   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>将策略应用于 Azure 订阅

可以使用以下命令将默认 Azure Stack Hub 策略应用于 Azure 订阅。 在运行这些命令之前，请 `Azure subscription name` 将替换为你的 Azure 订阅的名称。

### <a name="az-modules"></a>[Az 模块](#tab/az1)

```powershell
Add-AzAccount
$s = Select-AzSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm1)

```powershell
Add-AzureRMAccount
$s = Select-AzureRMSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRMPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRMPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

---

## <a name="apply-policy-to-a-resource-group"></a>将策略应用于资源组

你可能想要应用更细化的策略。 例如，你在相同的订阅中可能有其他正在运行的资源。 可以将策略应用范围限定为特定资源组，这样就可以使用 Azure 资源测试 Azure Stack Hub 的应用。 在运行以下命令之前，请将 `Azure subscription name` 替换为 Azure 订阅的名称：

### <a name="az-modules"></a>[Az 模块](#tab/az2)

```powershell
Add-AzAccount
$rgName = 'myRG01'
$s = Select-AzSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm2)
 
```powershell
Add-AzureRMAccount
$rgName = 'myRG01'
$s = Select-AzureRMSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRMPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRMPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

---

## <a name="policy-in-action"></a>执行中的策略

部署 Azure Policy 后，当尝试部署被策略禁止的资源时会收到错误：

![由于策略约束而资源部署失败的结果](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>后续步骤

* [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
* [使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)
* [使用 Visual Studio 部署模板](azure-stack-deploy-template-visual-studio.md)
