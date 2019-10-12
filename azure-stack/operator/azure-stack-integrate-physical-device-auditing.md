---
title: 将物理设备审核与 Azure Stack datacenter 集成 |Microsoft Docs
description: 了解如何将物理设备访问审核与 Azure Stack 数据中心集成。
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 06/10/2019
keywords: ''
ms.openlocfilehash: b5fa17b3913db7ebec210fc3bf986bac6414368e
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277222"
---
# <a name="integrate-physical-device-auditing-with-your-azure-stack-datacenter"></a>将物理设备审核与 Azure Stack 数据中心集成

Azure Stack 中的所有物理设备（例如基板管理控制器 (BMC) 和网络交换机）都会发出审核日志。 可以将审核日志集成到整体审核解决方案中。 由于设备因不同的 Azure Stack OEM 硬件供应商而异，所以请联系你的供应商来获取有关审核集成的文档。 以下各部分提供了有关 Azure Stack 中的物理设备审核的一些常规信息。  

## <a name="physical-device-access-auditing"></a>物理设备访问审核

Azure Stack 中的所有物理设备都支持使用 TACACS 或 RADIUS。 支持包括访问基板管理控制器 (BMC) 和网络交换机。

Azure Stack 解决方案不附带 RADIUS 或 TACACS 内置。 但是，经验证，这些解决方案支持使用市面上现有的 RADIUS 或 TACACS 解决方案。

对于 RADIUS，只有 MSCHAPv2 经过了验证。 它代表使用 RADIUS 的最安全实现。 请咨询 OEM 硬件供应商，在 Azure Stack 解决方案包含的设备中启用 TACAS 或 RADIUS。

## <a name="syslog-forwarding-for-network-devices"></a>网络设备的 Syslog 转发

Azure Stack 中的所有物理网络设备都支持 syslog 消息。 Azure Stack 解决方案不随 syslog 服务器一起提供。 但是，经验证，这些设备支持将消息发送到市面上现有的 syslog 解决方案。

Syslog 目标地址是针对部署所收集的可选参数，但也可以在部署后添加此参数。 要在网络设备上配置 syslog 转发，请咨询你的 OEM 硬件供应商。

## <a name="next-steps"></a>后续步骤

[服务策略](azure-stack-servicing-policy.md)
