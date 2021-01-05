---
title: Azure Stack 上的 GPU VM | Microsoft Docs
description: Azure Stack 中的 GPU 计算参考。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2020
ms.author: patricka
ms.reviewer: kivenkat
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 2b941c47b9c9662998b8d55bc6878e935969d1a4
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872527"
---
# <a name="gpu-vms-on-azure-stack"></a>Azure Stack 上的 GPU VM 

*适用于：Azure Stack 集成系统* 

本主题介绍如何管理 Azure Stack Hub 上的 GPU VM。


## <a name="partitioned-gpu-vm-size"></a>已分区的 GPU VM 大小 

NVv4 系列虚拟机由 [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU 提供支持。 通过 NVv4 系列，Azure Stack Hub 正在引入使用部分 GPU 的虚拟机。 此大小可用于 GPU 加速的图形应用程序和虚拟桌面。 NVv4 虚拟机目前只支持 Windows 来宾操作系统。 

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数 | 
| --- | --- | --- | --- | --- | --- | --- | --- |   
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 | 

## <a name="patch-and-update-fru-behavior-of-vms"></a>VM 的修补升级以及 FRU 行为 

GPU VM 将在执行 Azure Stack Hub 的修补升级 (PnU) 以及硬件更换 (FRU) 等操作期间停机。 下表介绍了在这些活动期间观察到的 VM 状态，以及用户在这些操作后可以执行（以使这些 VM 再次可用）的手动操作。 

| 操作 | PnU - 快速更新 | PnU - 完全更新、OEM 更新 | FRU | 
| --- | --- | --- | --- | 
| VM 状态  | 在更新期间和更新后不可用，无需手动启动操作 | 在更新期间不可用。 在更新后可用，需要手动操作 | 在更新期间不可用。 在更新后可用，需要手动操作| 
| 手动操作 | 如果 VM 需要在更新期间可用，并且可用 GPU 分区存在，则可通过单击“重启”按钮从门户重启 VM。 在更新后，需要使用“重启”按钮从门户重启 VM | 在更新期间无法使 VM 可用。 完成更新后，需要使用“停止”按钮停止对 VM 解除分配，并使用“开始”按钮开始备份 | 在更新期间无法使 VM 可用。完成更新后，需要使用“停止”按钮停止对 VM 解除分配，并使用“开始”按钮开始备份。| 

## <a name="guest-driver-installation"></a>来宾驱动程序安装 

[本](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-amd-driver-setup)文档介绍了如何在启用 NVv4 GPU P 的 VM 中设置 AMD 来宾驱动程序，以及如何验证驱动程序的安装。 

## <a name="next-steps"></a>后续步骤 

[Azure Stack VM 功能](azure-stack-vm-considerations.md) 
