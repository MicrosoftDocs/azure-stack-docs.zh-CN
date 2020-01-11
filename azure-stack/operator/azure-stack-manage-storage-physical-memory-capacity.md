---
title: 管理 Azure Stack 集线器中的物理内存容量 |Microsoft Docs
description: 了解如何监视和管理 Azure Stack 集线器中的物理内存和容量。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: cfacebb9c4589332b57d3140b6ef43c1156ea55c
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882328"
---
# <a name="manage-physical-memory-capacity-in-azure-stack-hub"></a>管理 Azure Stack 集线器中的物理内存容量

若要增加 Azure Stack 集线器中的总可用内存容量，可以添加更多内存。 在 Azure Stack 中心，物理服务器也称为*缩放单位节点*。 属于单个缩放单位的所有缩放单元节点必须具有相同的内存量。

> [!note]  
> 继续之前，请查阅硬件制造商的文档，以查看制造商是否支持物理内存升级。 OEM 硬件供应商支持合同可能要求供应商执行物理服务器机架放置和设备固件更新。

以下流程图显示了将内存添加到每个缩放单位节点的一般过程。

![将内存添加到每个缩放单位节点的过程](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>向现有节点添加内存
以下步骤提供添加内存的过程的高级概述。

> [!Warning]
> 请勿在未参考 OEM 提供的文档的情况下执行这些步骤。
> 
> [!Warning]
> 必须关闭整个缩放单位，因为不支持滚动内存升级。

1. 使用[开始和停止 Azure Stack 中心](azure-stack-start-and-stop.md)一文中所述的步骤停止 Azure Stack 集线器。
2. 使用硬件制造商的文档升级每台物理计算机上的内存。
3. 按照[启动和停止 Azure Stack 中心](azure-stack-start-and-stop.md)一文中的步骤开始 Azure Stack 中心。

## <a name="next-steps"></a>后续步骤

 - 若要了解如何管理 Azure Stack 中心中的存储帐户，请参阅[管理 Azure Stack 中心的存储帐户](azure-stack-manage-storage-accounts.md)。
 - 若要了解如何监视和管理 Azure Stack 中心部署的存储容量，请参阅[管理 Azure Stack 中心的存储容量](azure-stack-manage-storage-shares.md)。
