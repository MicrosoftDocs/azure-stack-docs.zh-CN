---
title: 监视 Azure Stack 集线器硬件运行状况
description: 了解如何监视 Azure Stack 集线器硬件组件的运行状况。
author: sethmanheim
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 4fe942e6a4230d9807780507de8b658a6e0ed4c3
ms.sourcegitcommit: c653efe7cd5177bf61c1a321ba7f38c0cdf90346
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77575136"
---
# <a name="monitor-azure-stack-hub-hardware-components"></a>监视 Azure Stack 集线器硬件组件

Azure Stack 中心运行状况和监视系统监视存储子系统的状态，并根据需要引发警报。 运行状况和监视系统也可以为下列硬件组件引发警报：

- 系统风扇
- 系统温度
- 电源
- CPU
- 内存
- 启动驱动器

> [!NOTE]
> 在启用此功能之前，必须与硬件合作伙伴确认它们已就绪。 硬件合作伙伴还将提供在 BMC 中启用此功能的详细步骤。

## <a name="snmp-listener-scenario"></a>SNMP 侦听器方案

SNMP v3 侦听器正在 TCP 端口162上的所有三个 ERCS 实例上运行。 基板管理控制器（BMC）必须配置为向 Azure Stack 集线器侦听器发送 SNMP 陷阱。 可以通过打开 "区域属性" 视图，从管理门户中获取三个 PEP Ip。

向侦听器发送陷阱需要身份验证，并且必须使用与访问基本 BMC 本身相同的凭据。

在 TCP 端口162上的三个 ERCS 实例中的任何一个实例上收到 SNMP 陷阱时，将在内部匹配 OID，并引发警报。 Azure Stack 中心运行状况和监视系统仅接受硬件伙伴定义的 Oid。 如果 OID 对于 Azure Stack 集线器是未知的，则它不会与警报匹配。

替换发生故障的组件后，会将一个事件从 BMC 发送到指示状态更改的 SNMP 侦听器，并在 Azure Stack 集线器中自动关闭该警报。

> [!NOTE]
> 在更换整个节点或主板后，现有警报不会自动关闭。 当 BMC 失去其配置时，这同样适用。例如，由于恢复出厂设置。

## <a name="next-steps"></a>后续步骤

[防火墙集成](azure-stack-firewall.md)
