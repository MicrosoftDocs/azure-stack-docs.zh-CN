---
title: Azure Stack 上的 GPU Vm |Microsoft Docs
description: Azure Stack 中的 GPU 计算参考。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2020
ms.author: justinha
ms.reviewer: kivenkat
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: c80817f7fe92916e1d83ae7bc1e83290d25e2906
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182929"
---
# <a name="gpu-vms-on-azure-stack"></a>Azure Stack 上的 GPU Vm 

*适用于：Azure Stack 集成系统* 

本主题介绍如何在 Azure Stack 集线器上管理 GPU Vm。


## <a name="partitioned-gpu-vm-size"></a>分区的 GPU VM 大小 

NVv4 系列虚拟机由 [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU 提供支持。 通过 NVv4 系列，Azure Stack Hub 正在引入使用部分 GPU 的虚拟机。 此大小可用于 GPU 加速的图形应用程序和虚拟桌面。 NVv4 虚拟机目前只支持 Windows 来宾操作系统。 

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | GPU | GPU 内存：GiB | 最大数据磁盘数 | 最大 NIC 数 | 
| --- | --- | --- | --- | --- | --- | --- | --- |   
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 | 

## <a name="patch-and-update-fru-behavior-of-vms"></a>Vm 的修补和更新、FRU 行为 

GPU VM 将在执行 Azure Stack Hub 的修补升级 (PnU) 以及硬件更换 (FRU) 等操作期间停机。 下表介绍了在这些活动期间观察到的 VM 状态，以及用户在这些操作后可以执行（以使这些 VM 再次可用）的手动操作。 

| 操作 | PnU - 快速更新 | PnU - 完全更新、OEM 更新 | FRU | 
| --- | --- | --- | --- | 
| VM 状态  | 在更新期间和更新后不可用，无需手动启动操作 | 在更新期间不可用。 在更新后可用，需要手动操作 | 在更新期间不可用。 在更新后可用，需要手动操作| 
| 手动操作 | 如果需要在更新过程中提供 VM，则可通过单击 "重启" 按钮从门户重新启动 VM。 VM 需要使用 "重新启动" 按钮从门户更新后重启 | 在更新期间无法使 VM 可用。 完成更新后，需要使用 "停止" 按钮停止释放 VM，并使用 "开始" 按钮开始备份 | 在更新过程中无法使用 VM。完成更新后，需要使用 "停止" 按钮停止释放 VM，并使用 "启动" 按钮开始备份。| 

## <a name="guest-driver-installation"></a>来宾驱动程序安装 

[本](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-amd-driver-setup) 文档介绍如何在已启用 NVv4 GPU P 的 VM 中设置 AMD 来宾驱动程序，以及如何验证驱动程序安装的步骤。 

## <a name="next-steps"></a>后续步骤 

[Azure Stack VM 功能](azure-stack-vm-considerations.md) 
