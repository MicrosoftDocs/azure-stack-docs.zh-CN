---
title: 管理 Azure Stack 的物理内存容量 | Microsoft Docs
description: 监视和管理 Azure Stack 的可用存储空间。
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
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 3aa69f1ea6e16b582a17da867c4895413d0043d2
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782391"
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>管理 Azure Stack 的物理内存容量

*适用于：Azure Stack 集成系统*

若要增加 Azure Stack 的总可用内存容量，可以添加更多内存。 在 Azure Stack 中，物理服务器也称为*缩放单位节点*。 所有属于单一缩放单元的缩放单元节点都必须具有相同的内存量。

> [!note]  
> 继续之前，请参阅硬件制造商文档，了解制造商是否支持物理内存升级。 OEM 硬件供应商支持合同可能会要求供应商进行物理服务器机架放置和设备固件更新。

以下流程图显示将内存添加到每个缩放单元节点的一般过程。

![将内存添加到每个缩放单元节点](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>将内存添加到现有节点
以下步骤提供添加内存过程的高级概述。 

> [!Warning]
> 请勿在未参考 OEM 提供的文档的情况下按照这些步骤操作。
> 
> [!Warning]
> 由于不支持滚动内存升级，因此必须关闭整个缩放单元。

1. 按照[启动和停止 Azure Stack](azure-stack-start-and-stop.md) 一文中所述的步骤，停止 Azure Stack。
2. 升级使用硬件制造商的文档的每台物理计算机上的内存。
3. 按照[启动和停止 Azure Stack](azure-stack-start-and-stop.md) 一文中的步骤，启动 Azure Stack。

## <a name="next-steps"></a>后续步骤

 - 若要了解如何管理 Azure 中的存储帐户，以根据业务需求查找、恢复和回收存储容量，请参阅[管理 Azure Stack 中的存储帐户](azure-stack-manage-storage-accounts.md)。
 - 若要了解 Azure Stack 云操作员如何监视和管理其 Azure Stack 部署的存储容量，请参阅[管理 Azure Stack 的存储容量](azure-stack-manage-storage-shares.md)。 
