---
title: 在 Azure Stack Hub 中监视硬件运行状况
description: 了解如何监视 Azure Stack Hub 硬件组件的运行状况。
author: sethmanheim
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 6eb455dbcf30b1d77818b13d1fc7db6b6b11c6f9
ms.sourcegitcommit: 62fc0592fdec706ade2b14e685448256ad0b4fe9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2020
ms.locfileid: "93239557"
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
> 在启用此功能之前，必须向硬件合作伙伴确认它们已就绪。 硬件合作伙伴还会提供在基板管理控制器 (BMC) 中启用此功能的详细步骤。 对于版本2005及更高版本，必须将基本板管理控制器中的用户加密设置为 AES。 

## <a name="snmp-listener-scenario"></a>SNMP 侦听器场景

SNMP v3 侦听器正在 TCP 端口 162 上的所有三个 ERCS 实例上运行。 BMC 必须配置为向 Azure Stack Hub 侦听器发送 SNMP 陷阱。 可以通过打开区域属性视图，从管理员门户中获取三个 PEP IP。

向侦听器发送陷阱要求进行身份验证，并且必须使用与访问基本 BMC 本身相同的凭据。

如果 TCP 端口 162 上的三个 ERCS 实例中的任何一个实例收到 SNMP 陷阱，将在内部匹配 OID，并引发警报。 Azure Stack Hub 运行状况和监视系统仅接受硬件合作伙伴定义的 OID。 如果 OID 对于 Azure Stack Hub 是未知的，则不会将其与警报匹配。

更换发生故障的组件后，系统会从 BMC 将一个指示状态更改的事件发送到 SNMP 侦听器。 然后，该警报会自动在 Azure Stack Hub 中关闭。

> [!NOTE]
> 在更换整个节点或主板后，现有警报不会自动关闭。 当 BMC 失去其配置时（例如恢复出厂设置），这同样适用。

## <a name="next-steps"></a>后续步骤

[防火墙集成](azure-stack-firewall.md)
