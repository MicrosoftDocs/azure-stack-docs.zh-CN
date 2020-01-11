---
title: 使用 Azure Stack 中心策略模块 |Microsoft Docs
description: 了解如何约束 Azure 订阅的行为与 Azure Stack 中心订阅类似
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: aedc2915f3e2be473b74766c4274197112ecf6fe
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75878741"
---
# <a name="manage-azure-policy-using-the-azure-stack-hub-policy-module"></a>使用 Azure Stack 中心策略模块管理 Azure 策略

利用 Azure Stack Hub 策略模块，你可以配置与 Azure Stack 中心具有相同的版本和服务可用性的 Azure 订阅。 该模块使用[**New-azurermpolicydefinition**](/powershell/module/azurerm.resources/new-azurermpolicydefinition) PowerShell Cmdlet 创建 Azure 策略，该策略限制订阅中可用的资源类型和服务。 然后，使用[**new-azurermpolicyassignment**](/powershell/module/azurerm.resources/new-azurermpolicyassignment) cmdlet 在相应的范围内创建策略分配。 配置策略后，可以使用 Azure 订阅来开发面向 Azure Stack 中心的应用。

## <a name="install-the-module"></a>安装模块

1. 安装所需版本的 AzureRM PowerShell 模块，如[安装 Azure Stack 集线器的 PowerShell](../operator/azure-stack-powershell-install.md)的步骤1中所述。
2. [从 GitHub 下载 Azure Stack 中心工具](../operator/azure-stack-powershell-download.md)。
3. [配置 PowerShell 以便与 Azure Stack 中心一起使用](azure-stack-powershell-configure-user.md)。
4. 导入**test-azurestack hbase-runner.psm1**模块：

   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>将策略应用到 Azure 订阅

可以使用以下命令对 Azure 订阅应用默认 Azure Stack 中心策略。 在运行这些命令之前，请将 `Azure subscription name` 替换为你的 Azure 订阅的名称：

```powershell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>将策略应用到资源组

你可能想要应用更精细的策略。 例如，你可能在同一订阅中运行了其他资源。 可以将策略应用程序的范围限定为特定的资源组，这使你可以使用 Azure 资源测试 Azure Stack 中心的应用。 运行以下命令之前，请将 `Azure subscription name` 替换为你的 Azure 订阅的名称：

```powershell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>策略正在进行

部署 Azure 策略后，当你尝试部署策略禁止的资源时，会收到错误：

![由于策略约束导致的资源部署失败结果](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>后续步骤

* [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
* [使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)
* [通过 Visual Studio 部署模板](azure-stack-deploy-template-visual-studio.md)
