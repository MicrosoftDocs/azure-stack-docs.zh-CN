---
title: 监视 Azure Stack 集线器中的硬件运行状况
description: 了解如何监视 Azure Stack Hub 硬件组件的运行状况。
author: sethmanheim
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: a4f5d82979e209eae124f930d1f59abc1bb88b98
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2020
ms.locfileid: "83375177"
---
# <a name="monitor-azure-stack-hub-hardware-components"></a>监视 Azure Stack Hub 硬件组件

Azure Stack Hub 运行状况和监视系统监视存储子系统的状态，并根据需要引发警报。 运行状况和监视系统还可以为以下硬件组件引发警报：

- 系统风扇
- 系统温度
- 电源
- CPU
- 内存
- 启动驱动器

> [!NOTE]
> 在启用此功能之前，必须使用硬件合作伙伴验证它们已准备就绪。 硬件合作伙伴还应提供在 BMC 中启用此功能的详细步骤。

## <a name="snmp-listener-scenario"></a>SNMP 侦听器场景

SNMP v3 侦听器正在 TCP 端口 162 上的所有三个 ERCS 实例上运行。 基板管理控制器 (BMC) 必须配置为向 Azure Stack Hub 侦听器发送 SNMP 陷阱。 可以通过打开 "区域属性" 视图从管理员门户获取三个 PEP Ip。

向侦听器发送陷阱要求进行身份验证，并且必须使用与访问基本 BMC 本身相同的凭据。

如果 TCP 端口 162 上的三个 ERCS 实例中的任何一个实例收到 SNMP 陷阱，将在内部匹配 OID，并引发警报。 Azure Stack Hub 运行状况和监视系统仅接受硬件合作伙伴定义的 OID。 如果 OID 对于 Azure Stack 集线器是未知的，则它不会与警报匹配。

替换发生故障的组件后，会将事件从 BMC 发送到指示状态更改的 SNMP 侦听器。 然后，该警报会自动在 Azure Stack Hub 中关闭。

> [!NOTE]
> 在更换整个节点或主板后，现有警报不会自动关闭。 当 BMC 失去其配置时（例如恢复出厂设置），这同样适用。

## <a name="next-steps"></a>后续步骤

[防火墙集成](azure-stack-firewall.md)
