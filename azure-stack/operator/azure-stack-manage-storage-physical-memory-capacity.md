---
title: 管理 Azure Stack Hub 中的物理内存容量
description: 了解如何监视和管理 Azure Stack Hub 中的物理内存和容量。
author: PatAltimore
ms.topic: how-to
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 82aa7c6bd9295d5727ca7a37fa6798d2bf352887
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870182"
---
# <a name="manage-physical-memory-capacity-in-azure-stack-hub"></a>管理 Azure Stack Hub 中的物理内存容量

若要增加 Azure Stack Hub 中的总可用内存容量，可以添加更多内存。 在 Azure Stack Hub 中，物理服务器也称为“缩放单元节点”  。 所有属于单一缩放单元的缩放单元节点都必须具有相同的内存量。

> [!note]  
> 继续之前，请参阅硬件制造商文档，了解制造商是否支持物理内存升级。 OEM 硬件供应商支持合同可能会要求供应商进行物理服务器机架放置和设备固件更新。

以下流程图显示将内存添加到每个缩放单元节点的一般过程。

![将内存添加到每个缩放单元节点的过程](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>将内存添加到现有节点
以下步骤提供添加内存过程的高级概述。

> [!Warning]
> 请勿在未参考 OEM 提供的文档的情况下按照这些步骤操作。
> 
> [!Warning]
> 由于不支持滚动内存升级，因此必须关闭整个缩放单元。

1. 按照[启动和停止 Azure Stack Hub](azure-stack-start-and-stop.md) 一文中所述的步骤，停止 Azure Stack Hub。
2. 使用硬件制造商的文档升级每台物理计算机上的内存。
3. 按照[启动和停止 Azure Stack Hub](azure-stack-start-and-stop.md) 一文中的步骤，启动 Azure Stack Hub。

## <a name="next-steps"></a>后续步骤

 - 若要了解如何管理 Azure Stack Hub 中的存储帐户，请参阅[管理 Azure Stack Hub 中的存储帐户](azure-stack-manage-storage-accounts.md)。
 - 若要了解如何监视和管理 Azure Stack Hub 部署的存储容量，请参阅[管理 Azure Stack Hub 的存储容量](azure-stack-manage-storage-shares.md)。
