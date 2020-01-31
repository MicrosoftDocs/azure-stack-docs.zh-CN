---
title: 管理 Azure Stack 集线器中的网络资源
description: 管理员可以管理网络资源，包括 MAC 地址池和区域中公共 IP 地址的消耗
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: 949c00edfbe7e5daff8c1cd17c20681c3a7d9265
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76880207"
---
# <a name="manage-network-resources"></a>管理网络资源

## <a name="mac-address-pool"></a>MAC 地址池

Azure Stack 集线器使用静态 MAC 地址池自动生成 MAC 地址并将其分配给虚拟机。
此 MAC 地址池是在部署过程中自动生成的，并使用以下范围：

- StartMacAddress： 00-1D-D8-B7-00-00
- EndMacAddress： 00-1D-D8-F4-FF-FF

> [!Note]  
> 此 MAC 地址池在每个 Azure Stack 集线器系统中是相同的，不可配置。

根据虚拟网络与现有公司网络的连接方式，可能需要虚拟机的复制 MAC 地址。

有关 MAC 地址池利用率的详细信息，请参阅 Azure Stack 中心管理员 PowerShell 模块中的 cmdlet [AzsMacAddressPool](https://docs.microsoft.com/powershell/module/azs.fabric.admin/get-azsmacaddresspool) 。

## <a name="view-public-ip-address-consumption-in-azure-stack-hub"></a>查看 Azure Stack 集线器中的公共 IP 地址使用情况

作为云管理员，你可以查看：
 - 已分配给租户的公共 IP 地址数。
 - 仍可用于分配的公共 IP 地址数。
 - 已在该位置分配的公共 IP 地址的百分比。

"**公共 ip 池用量**" 磁贴显示公共 ip 地址池使用的公共 ip 地址数。 对于每个 IP 地址，该磁贴显示租户 IaaS VM 实例、结构基础结构服务和租户显式创建的公共 IP 地址资源的使用情况。

磁贴的用途是为 Azure Stack 中心操作员指定此位置中使用的公共 IP 地址数。 此数字可帮助管理员确定此资源是否不足。

"**租户资源**" 下的 "**公共 ip 地址**" 菜单项只列出*租户显式创建*的公共 ip 地址。 可以在 "**资源提供程序**" 的 "**网络**" 窗格中找到菜单项。 "**公共 ip 池用量**" 磁贴上已**使用**的公共 ip 地址数始终不同于（大于） "**租户资源**" 下 "**公共 IP 地址**" 磁贴上的数字。

### <a name="view-the-public-ip-address-usage-information"></a>查看公共 IP 地址使用情况信息

若要查看已在区域中使用的公共 IP 地址总数，请执行以下操作：

1. 在 Azure Stack 中心管理员门户中，选择 "**所有服务**"。 然后，在 "**管理**" 类别下选择 "**网络**"。
1. "**网络**" 窗格显示 "**概述**" 部分中的 "**公共 IP 池用量**" 磁贴。

    ![网络资源提供程序窗格](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-01.png)

**使用**的数字代表公共 ip 地址池中分配的公共 ip 地址数。 "**可用**" 数字代表公共 ip 地址池中尚未分配并仍可用的公共 ip 地址数。 **% Used** number 代表在该位置的公共 ip 地址池中的公共 ip 地址总数的百分比中的已使用或已分配地址的数量。

### <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>查看租户订阅创建的公共 IP 地址

选择 "**租户资源**" 下的 "**公共 IP 地址**"。 查看特定区域中的租户订阅显式创建的公共 IP 地址的列表。

![租户公共 IP 地址](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-02.png)

你可能会注意到，已经动态分配的一些公共 IP 地址显示在列表中。 但是，尚未与地址相关联。 地址资源已在网络资源提供程序中创建，但尚未在网络控制器中创建。

网络控制器在绑定到接口、网络接口卡（NIC）、负载均衡器或虚拟网络网关之前，不会为资源分配地址。 公共 IP 地址绑定到接口后，网络控制器会分配 IP 地址。 该地址将显示在 "**地址**" 字段中。

### <a name="view-the-public-ip-address-information-summary-table"></a>查看公共 IP 地址信息摘要表

在不同的情况下，会分配公共 IP 地址，确定地址是出现在一个列表中还是出现在另一个列表中。

| **公共 IP 地址分配案例** | **出现在使用情况摘要中** | **显示在 "租户公共 IP 地址" 列表中** |
| --- | --- | --- |
| 尚未分配给 NIC 或负载均衡器的动态公共 IP 地址（临时） |否 |是 |
| 分配给 NIC 或负载均衡器的动态公共 IP 地址。 |是 |是 |
| 分配给租户 NIC 或负载均衡器的静态公共 IP 地址。 |是 |是 |
| 分配给结构基础结构服务终结点的静态公共 IP 地址。 |是 |否 |
| 为 IaaS VM 实例隐式创建的公共 IP 地址，并且用于虚拟网络上的出站 NAT。 只要租户创建 VM 实例，就会在幕后创建这些信息，以便 Vm 可以将信息发送到 Internet。 |是 |否 |

## <a name="next-steps"></a>后续步骤

[管理 Azure Stack 集线器中的存储帐户](azure-stack-manage-storage-accounts.md)
