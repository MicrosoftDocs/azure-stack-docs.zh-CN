---
title: Azure Stack 中的客户计费模型概述 |Microsoft Docs
description: 了解 Azure Stack 用户如何针对模块化数据中心 (MDC) 中的资源使用情况进行计费。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: 15f894a668374be5380f322d368b76d88bb93cba
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910748"
---
# <a name="billing-model-overview---modular-data-center-mdc"></a>计费模型概述-模块化数据中心 (MDC) 

作为 MDC 或 Azure Stack 中心耐用的用户，Microsoft 根据每个设备的时间长度向你计费。 收费为每期，并包含使用基本计算、存储和网络服务的权利。 你需要根据应用服务、事件中心和任何其他 PaaS 服务的使用情况收费，以及在 Azure Stack Hub 耐用和 MDC 上运行的 Windows Server PAYG Vm。 如果你完全断开连接并且无法报告使用数据，则必须获取 PaaS 服务的容量许可证，并为 Windows Vm 自带许可证。

计费会在交付设备的订单后的14天内开始。 对于 Azure Stack 中心，初始计费周期为一年。 一年后，订单可延长一年，另一年的使用量将按计费。 如果未扩展订单，并且未返回设备，则每月将继续计费。 对于 Azure Stack 中心耐用，计费始终按月计费。

将设备返还给 Microsoft 后，在 Microsoft 仓库中收到设备后，就会立即停止计费。

## <a name="next-steps"></a>后续步骤

[使用情况 API 参考](analyze-usage-tzl.md)
