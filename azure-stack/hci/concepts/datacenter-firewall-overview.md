---
title: Azure Stack HCI 和 Windows Server 中的数据中心防火墙概述
description: 使用本主题了解 Azure Stack HCI 和 Windows Server 中的数据中心防火墙。
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 02/02/2021
ms.openlocfilehash: 78ed83e5be35bacb51d6e03d88d7662663117f61
ms.sourcegitcommit: 0e58c5cefaa81541d9280c0e8a87034989358647
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99510884"
---
# <a name="what-is-datacenter-firewall"></a>什么是数据中心防火墙？

> 适用于： Azure Stack HCI，版本 20H2;Windows Server 2019;Windows Server 2016

数据中心防火墙是一种网络层、5 元组（协议、源端口号、目标端口号、源 IP 地址和目标 IP 地址）、有状态的多租户软件定义的网络 (SDN) 防火墙。 数据中心防火墙可保护虚拟网络和传统 VLAN 网络的网络层中的东西流量和南北流量流。

## <a name="how-datacenter-firewall-works"></a>数据中心防火墙的工作原理

可以通过创建应用于子网或网络接口的访问控制列表 (ACL) 来启用和配置数据中心防火墙。 在每个租户虚拟机 (VM) 的 vSwitch 端口上强制实施防火墙策略。 策略通过租户门户推送，然后[网络控制器](network-controller-overview.md)将其分发给所有适用的主机。

租户管理员可安装和配置防火墙策略来帮助保护其网络免受来自 Internet 和 Intranet 网络的意外流量的影响。

:::image type="content" source="media/datacenter-firewall/multitenant-firewall-overview.png" alt-text="网络堆栈中的数据中心防火墙" border="false":::

服务提供商管理员或租户管理员可通过网络控制器和 Northbound API 管理数据中心防火墙策略。 还可使用 Windows Admin Center 配置和管理数据中心防火墙策略。

## <a name="advantages-for-cloud-service-providers"></a>为云服务提供商提供的优势

数据中心防火墙为 CSP 提供以下优势：

- 可提供给租户的高度可缩放、可管理且可诊断的基于软件的防火墙解决方案

- 无需中断租户防火墙策略即可自由地将租户 VM 移到其他计算主机

    - 部署为 vSwitch 端口主机代理防火墙

    - 租户 VM 获取分配给其 vSwitch 主机代理防火墙的策略

    - 防火墙规则在每个 vSwitch 端口中配置，独立于运行 VM 的实际主机

- 独立于租户来宾操作系统为租户 VM 提供保护

## <a name="advantages-for-tenants"></a>为租户提供的优势

数据中心防火墙为租户提供以下优势：

- 能够定义防火墙规则来帮助保护网络上面向 Internet 的工作负载和内部工作负载

- 能够定义防火墙规则来帮助保护同一第 2 层 (L2) 子网上的 VM 之间的流量，以及不同 L2 子网上的 VM 之间的流量

- 能够定义防火墙规则来帮助保护和隔离租户本地网络与服务提供商的虚拟网络之间的网络流量

- 能够将防火墙策略应用于传统的 VLAN 网络和基于覆盖的虚拟网络

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [使用数据中心防火墙 Azure Stack HCI 和 Windows Server 中的 Software-Defined 网络](../manage/use-datacenter-firewall.md)
- [Azure Stack HCI 和 Windows Server 中的 SDN](software-defined-networking.md)