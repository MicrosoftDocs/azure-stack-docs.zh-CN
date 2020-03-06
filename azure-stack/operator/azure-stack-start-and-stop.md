---
title: 启动和停止
titleSuffix: Azure Stack Hub
description: 了解如何启动和停止 Azure Stack 集线器。
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: misainat
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: d6742ad75625eaed8f7e8c1ebec725fa1b62d53c
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78367516"
---
# <a name="start-and-stop-azure-stack-hub"></a>启动和停止 Azure Stack 集线器

按照本文中的过程操作，正确关闭并重新启动 Azure Stack 中心服务。 *停止*将物理关闭并关闭整个 Azure Stack 中心环境。 *开始*在所有基础结构角色上通电，并将租户资源返回到关机之前的电源状态。

## <a name="stop-azure-stack-hub"></a>停止 Azure Stack 集线器

通过以下步骤停止或关闭 Azure Stack 集线器：

1. 准备 Azure Stack 中心环境的租户资源上运行的所有工作负荷，以供即将关闭。

2. 从具有网络访问权限的计算机上打开特权终结点会话（PEP） ERCS Azure Stack 中心 Vm。 有关说明，请参阅[使用 Azure Stack 集线器中的特权终结点](azure-stack-privileged-endpoint.md)。

3. 从 PEP 运行：

    ```powershell
      Stop-AzureStack
    ```

4. 等待所有物理 Azure Stack 中心节点关闭。

> [!Note]
> 可以按照提供 Azure Stack 集线器硬件的原始设备制造商（OEM）中的说明来验证物理节点的电源状态。

## <a name="start-azure-stack-hub"></a>开始 Azure Stack 集线器

启动 Azure Stack 集线器，并执行以下步骤。 不管 Azure Stack 中心停止的方式如何，请执行这些步骤。

1. 开启 Azure Stack 中心环境中的每个物理节点。 按照为 Azure Stack 中心提供硬件的 OEM 中的说明，验证物理节点的开机说明。

2. 等待 Azure Stack 集线器基础结构服务启动。 Azure Stack 集线器基础结构服务可能需要两个小时才能完成启动过程。 可以通过[ **ActionStatus** Cmdlet](#get-the-startup-status-for-azure-stack-hub)验证 Azure Stack 集线器的开始状态。

3. 确保你的所有租户资源都已返回到关闭前的状态。 工作负荷管理器启动后，在租户资源上运行的工作负荷可能需要重新配置。

## <a name="get-the-startup-status-for-azure-stack-hub"></a>获取 Azure Stack 中心的启动状态

通过以下步骤获取 Azure Stack 集线器启动例程的启动：

1. 从具有网络访问权限的计算机上打开特权终结点会话，Azure Stack 集线器 ERCS Vm。

2. 从 PEP 运行：

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack-hub"></a>Azure Stack 集线器的启动和关闭疑难解答

如果在 Azure Stack 集线器环境上通电后，基础结构和租户服务不能成功启动两小时，请执行以下步骤。

1. 从具有网络访问权限的计算机上打开特权终结点会话，Azure Stack 集线器 ERCS Vm。

2. 运行：

    ```powershell
      Test-AzureStack
      ```

3. 查看输出并解决所有运行状况错误。 有关详细信息，请参阅[运行 Azure Stack 集线器的验证测试](azure-stack-diagnostic-test.md)。

4. 运行：

    ```powershell
      Start-AzureStack
    ```

5. 如果运行**test-azurestack**导致失败，请联系 Microsoft 支持部门。

## <a name="next-steps"></a>后续步骤

详细了解[Azure Stack 集线器诊断工具](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)
