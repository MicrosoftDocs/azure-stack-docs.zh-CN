---
title: Azure Stack HCI 中的数据中心防火墙概述
description: 使用本主题了解 Azure Stack HCI 中的数据中心防火墙。
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/17/2020
ms.openlocfilehash: 34efe18aee4a62481d81a5e9a810a0a71d97b063
ms.sourcegitcommit: 40d3f3f0ac088d1590d1fb64ca05ac1dabf4e00c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874951"
---
# <a name="what-is-datacenter-firewall"></a>什么是数据中心防火墙？

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

数据中心防火墙是网络层、5元组 (协议、源和目标端口号、源和目标 IP 地址) 、有状态、多租户软件定义的网络 (SDN) 防火墙。 数据中心防火墙可跨虚拟网络和传统 VLAN 网络的网络层保护东西部和北南部流量。

## <a name="how-datacenter-firewall-works"></a>数据中心防火墙的工作原理

通过创建应用于子网或网络接口的访问控制列表 (Acl) 来启用和配置数据中心防火墙。 在每个租户虚拟机 (VM) 的 vSwitch 端口上强制实施防火墙策略。 策略通过租户门户推送， [网络控制器](network-controller-overview.md) 将其分发给所有适用的主机。

租户管理员可以安装和配置防火墙策略来帮助保护其网络免受来自 internet 和 intranet 网络的不需要的通信。

:::image type="content" source="media/datacenter-firewall/multitenant-firewall-overview.png" alt-text="网络堆栈中的数据中心防火墙" border="false":::

服务提供商管理员或租户管理员可以通过网络控制器和 northbound Api 来管理数据中心防火墙策略。 你还可以使用 Windows 管理中心配置和管理数据中心防火墙策略。

## <a name="advantages-for-cloud-service-providers"></a>云服务提供商的优势

数据中心防火墙为 Csp 提供以下优点：

- 可提供给租户的高度可扩展、可管理和 diagnosable 的基于软件的防火墙解决方案

- 无需中断租户防火墙策略即可自由地将租户 Vm 移到不同的计算主机

    - 部署为 vSwitch 端口主机代理防火墙

    - 租户 Vm 获取分配给其 vSwitch 主机代理防火墙的策略

    - 防火墙规则配置在每个 vSwitch 端口中，独立于运行 VM 的实际主机

- 向独立于租户来宾操作系统的租户 Vm 提供保护

## <a name="advantages-for-tenants"></a>租户的优点

数据中心防火墙为租户提供了以下优势：

- 能够定义防火墙规则来帮助保护面向 internet 的工作负荷和网络上的内部工作负荷

- 能够定义防火墙规则，以帮助保护同一第2层上的 Vm 之间的流量 (L2) 子网以及不同 L2 子网上的 Vm 之间的通信

- 能够定义防火墙规则来帮助保护和隔离租户本地网络及其虚拟网络与服务提供商之间的网络流量

- 能够将防火墙策略应用于传统的 VLAN 网络和基于覆盖的虚拟网络

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [使用数据中心防火墙 Azure Stack HCI 中的 Software-Defined 网络](../manage/use-datacenter-firewall.md)
- [Azure Stack HCI 中的 SDN](software-defined-networking.md)