---
title: Azure Stack 中的客户计费模式概述Microsoft Docs
description: 了解如何对 Azure Stack 用户计费资源使用情况。
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
ms.date: 12/23/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: c889ebc0bc7b4074b375ebb4e64d1d38c7fcd177
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328881"
---
# <a name="billing-model-overview"></a>计费模型概述

作为 MDC 或 Azure Stack 中心耐用的用户，Microsoft 根据每个设备的时间长度向你计费。 费用是每个时段，并包含使用基本计算、存储和网络服务、应用服务、IoT 中心及其必备组件以及辅助服务（如 Key Vault）的权利。 还包括使用 Windows Server 作为来宾操作系统的情况。

实际使用情况不会按流量计费，并且不会报告给 Microsoft。

计费会在交付设备的订单后的14天内开始。 对于 Azure Stack 中心，初始计费周期为一年。 一年后，订单可延长一年，另一年的使用量将按计费。 如果未扩展订单，并且未返回设备，则每月将继续计费。 对于 Azure Stack 中心耐用，计费始终按月计费。

将设备返还给 Microsoft 后，在 Microsoft 仓库中收到设备后，就会立即停止计费。

## <a name="next-steps"></a>后续步骤

[使用情况 API 参考](analyze-usage-tzl.md)
