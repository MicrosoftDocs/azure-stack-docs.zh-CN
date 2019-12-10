---
title: 启动和停止
titleSuffix: Azure Stack
description: 了解如何启动和停止 Azure Stack。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: dbd85b4d7b63edb89cc327fd5b1f5592c24db508
ms.sourcegitcommit: 08d2938006b743b76fba42778db79202d7c3e1c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74954377"
---
# <a name="start-and-stop-azure-stack"></a>启动和停止 Azure Stack

按照本文中的过程操作，正确关闭并重新启动 Azure Stack 服务。 *停止*将物理关闭并关闭整个 Azure Stack 环境。 *开始*在所有基础结构角色上通电，并将租户资源返回到关机之前的电源状态。

## <a name="stop-azure-stack"></a>停止 Azure Stack

按照以下步骤停止或关闭 Azure Stack：

1. 准备 Azure Stack 环境的租户资源上运行的所有工作负荷，以供即将关闭。

2. 从具有对 Azure Stack ERCS Vm 的网络访问权限的计算机打开特权终结点会话（PEP）。 有关说明，请参阅[在 Azure Stack 中使用特权终结点](azure-stack-privileged-endpoint.md)。

3. 从 PEP 运行：

    ```powershell
      Stop-AzureStack
    ```

4. 等待所有物理 Azure Stack 节点关闭。

> [!Note]
> 可以按照提供 Azure Stack 硬件的原始设备制造商（OEM）中的说明来验证物理节点的电源状态。

## <a name="start-azure-stack"></a>开始 Azure Stack

按照以下步骤开始 Azure Stack。 不管 Azure Stack 如何停止，请执行以下步骤。

1. 开启 Azure Stack 环境中每个物理节点的电源。 按照为你的 Azure Stack 提供硬件的 OEM 中的说明，验证物理节点的开机说明。

2. 等待 Azure Stack 基础结构服务启动。 Azure Stack 基础结构服务可能需要两个小时才能完成启动过程。 可以通过[ **ActionStatus** cmdlet](#get-the-startup-status-for-azure-stack)验证 Azure Stack 的开始状态。

3. 确保你的所有租户资源都已返回到关闭前的状态。 工作负荷管理器启动后，在租户资源上运行的工作负荷可能需要重新配置。

## <a name="get-the-startup-status-for-azure-stack"></a>获取 Azure Stack 的启动状态

通过以下步骤获取 Azure Stack 启动例程的启动：

1. 从具有对 Azure Stack ERCS Vm 的网络访问权限的计算机打开特权终结点会话。

2. 从 PEP 运行：

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Azure Stack 的启动和关闭疑难解答

如果在你开启 Azure Stack 环境后，基础结构和租户服务不能成功启动两小时，请执行以下步骤。

1. 从具有对 Azure Stack ERCS Vm 的网络访问权限的计算机打开特权终结点会话。

2. 运行：

    ```powershell
      Test-AzureStack
      ```

3. 查看输出并解决所有运行状况错误。 有关详细信息，请参阅[运行 Azure Stack 的验证测试](azure-stack-diagnostic-test.md)。

4. 运行：

    ```powershell
      Start-AzureStack
    ```

5. 如果运行**test-azurestack**导致失败，请联系 Microsoft 支持部门。

## <a name="next-steps"></a>后续步骤

详细了解[Azure Stack 诊断工具](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)
