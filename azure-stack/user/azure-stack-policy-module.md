---
title: 使用 Azure Stack 策略模块 | Microsoft Docs
description: 了解如何限制 Azure 订阅使其行为像 Azure Stack 订阅
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: 1ae0c24fe5f3a130c7878c8623facada222a0e49
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64299665"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>使用 Azure Stack 策略模块管理 Azure Policy

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 策略模块，可使用相同版本控制和服务可用性与 Azure Stack 配置 Azure 订阅。 该模块使用[New-azurermpolicydefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) PowerShell cmdlet 来创建 Azure 策略限制的资源类型和在订阅中可用的服务。 然后使用 [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) cmdlet 在合适的作用域内创建一个策略分配。 配置策略后，可以使用 Azure 订阅来开发针对 Azure Stack 的应用。

## <a name="install-the-module"></a>安装模块

1. 按照[安装适用于 Azure Stack 的 PowerShell](../operator/azure-stack-powershell-install.md) 步骤 1 中的说明，安装所需的 AzureRM PowerShell 模块版本。
2. [从 GitHub 下载 Azure Stack 工具](../operator/azure-stack-powershell-download.md)。
3. [配置适用于 Azure Stack 的 PowerShell](azure-stack-powershell-configure-user.md)。
4. 导入 AzureStack.Policy.psm1 模块：


   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>将策略应用于 Azure 订阅

可以使用以下命令针对 Azure 订阅应用默认 Azure Stack 策略。 运行此命令之前，将为`Azure subscription name`的 Azure 订阅名称：

```powershell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>将策略应用于资源组

您可能想要应用更精细的策略。 例如，你在相同的订阅中可能有其他正在运行的资源。 可以将策略应用到特定资源组，使您能够使用 Azure 资源的 Azure Stack 的测试您的应用程序。 运行以下命令之前，将为`Azure subscription name`的 Azure 订阅名称：

```powershell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>执行中的策略

部署 Azure 策略后，当你尝试部署策略禁止的资源时收到错误：

![由于策略约束而资源部署失败的结果](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>后续步骤

* [通过 PowerShell 部署模板](azure-stack-deploy-template-powershell.md)
* [使用 Azure CLI 部署模板](azure-stack-deploy-template-command-line.md)
* [使用 Visual Studio 部署模板](azure-stack-deploy-template-visual-studio.md)
