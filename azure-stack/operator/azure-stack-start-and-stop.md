---
title: 启动和停止
titleSuffix: Azure Stack Hub
description: 了解如何启动和停止 Azure Stack Hub。
author: PatAltimore
ms.topic: how-to
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: 529cbfe4aff3c84cf732e58ff92b1bd920cd64b3
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869128"
---
# <a name="start-and-stop-azure-stack-hub"></a>启动和停止 Azure Stack Hub

请遵循本文中的过程正确关闭并重启 Azure Stack Hub 服务。  停止会以物理方式关闭整个 Azure Stack Hub 环境并将其断电。 启动会打开所有基础结构角色的电源，并使租户资源回到关闭前的电源状态。 

## <a name="stop-azure-stack-hub"></a>停止 Azure Stack Hub

使用以下步骤停止或关闭 Azure Stack Hub：

1. 针对即将进行的关闭准备在 Azure Stack Hub 环境的租户资源上运行的所有工作负荷。

2. 从可以通过网络访问 Azure Stack Hub ERCS VM 的计算机打开特权终结点会话 (PEP)。 有关说明，请参阅[使用 Azure Stack Hub 中的特权终结点](azure-stack-privileged-endpoint.md)。

3. 从 PEP 运行：

    ```powershell
      Stop-AzureStack
    ```

4. 等待所有物理 Azure Stack Hub 节点关闭电源。

> [!Note]
> 可以按照提供 Azure Stack Hub 硬件的原始设备制造商 (OEM) 的指示，确认物理节点的电源状态。

## <a name="start-azure-stack-hub"></a>启动 Azure Stack Hub

使用以下步骤启动 Azure Stack Hub。 请按照下列步骤操作，而不论 Azure Stack Hub 是如何停止的。

1. 将 Azure Stack Hub 环境中每个物理节点的电源打开。 按照提供 Azure Stack Hub 硬件的 OEM 的指示，确认物理节点的电源开启指示。

2. 等待直到 Azure Stack Hub 基础结构服务启动。 Azure Stack Hub 基础结构服务完成启动过程可能需要两个小时。 可以使用 [**Get-ActionStatus** cmdlet](#get-the-startup-status-for-azure-stack-hub) 确认 Azure Stack Hub 的启动状态。

3. 确保所有租户资源都已恢复到关闭之前的状态。 在工作负荷管理器启动后，可能需要重新配置在租户资源上运行的工作负荷。

## <a name="get-the-startup-status-for-azure-stack-hub"></a>获取 Azure Stack Hub 的启动状态

使用以下步骤获取 Azure Stack Hub 启动例程的启动状态：

1. 从可以通过网络访问 Azure Stack Hub ERCS VM 的计算机打开特权终结点会话。

2. 从 PEP 运行：

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack-hub"></a>针对 Azure Stack Hub 的启动和关闭进行故障排除

如果打开 Azure Stack Hub 环境的电源两小时后基础结构和租户服务未成功启动，请执行以下步骤。

1. 从可以通过网络访问 Azure Stack Hub ERCS VM 的计算机打开特权终结点会话。

2. 运行：

    ```powershell
      Test-AzureStack
      ```

3. 查看输出并解决任何运行状况错误。 有关详细信息，请参阅[运行 Azure Stack Hub 的验证测试](azure-stack-diagnostic-test.md)。

4. 运行：

    ```powershell
      Start-AzureStack
    ```

5. 如果运行 **test-azurestack** 导致失败，请联系 Microsoft 支持部门。

## <a name="next-steps"></a>后续步骤

详细了解 [Azure Stack Hub 诊断工具](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002)
