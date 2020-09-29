---
title: 管理网络资源
titleSuffix: Azure Stack Hub
description: 了解如何管理网络资源，包括 MAC 地址池和区域中公共 IP 地址的使用。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: scottnap
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: b96485e74286cbbbd2ff20f2378dc7c452849b52
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487390"
---
# <a name="manage-network-resources-in-azure-stack-hub"></a>在 Azure Stack Hub 中管理网络资源

## <a name="mac-address-pool"></a>MAC 地址池

Azure Stack Hub 使用静态 MAC 地址池自动生成 MAC 地址并将其分配给虚拟机 (VM)。 此 MAC 地址池是在部署过程中自动生成的，并使用以下范围：

- StartMacAddress：00-1D-D8-B7-00-00
- EndMacAddress：00-1D-D8-F4-FF-FF

> [!Note]  
> 此 MAC 地址池在每个 Azure Stack Hub 系统中都是相同的，并且是不可配置的。

你可能会遇到 VM 的 MAC 地址重复的情况，具体取决于虚拟网络如何与现有公司网络进行连接。

使用 Azure Stack Hub 管理员 PowerShell 模块中的 cmdlet [Get-AzsMacAddressPool](/powershell/module/azs.fabric.admin/get-azsmacaddresspool) 可以找到有关 MAC 地址池利用率的详细信息。

## <a name="view-public-ip-address-consumption-in-azure-stack-hub"></a>查看 Azure Stack Hub 中的公共 IP 地址使用情况

云管理员可以查看：
 - 分配给租户的公共 IP 地址数。
 - 仍然可以分配的公共 IP 地址的数目。
 - 已经在该位置分配的公共 IP 地址的百分比。

“公共 IP 池用量”磁贴显示在多个公共 IP 地址池中使用的公共 IP 地址数目。  对于每个 IP 地址，此磁贴显示租户 IaaS VM 实例、Fabric 基础结构服务以及由租户显式创建的公共 IP 地址资源的用量。

此磁贴的用途是让 Azure Stack Hub 操作员了解此位置中使用的公共 IP 地址数。 管理员可以通过该数字来确定此资源是否不足。

“租户资源”下的“公共 IP 地址”菜单项只列出租户显式创建的公共 IP 地址。    可以在“资源提供程序” -> “网络”窗格中找到该菜单项。   “公共 IP 池用量”磁贴上的“已用”公共 IP 地址数目始终不同于（大于）“租户资源”下“公共 IP 地址”磁贴上的数目。    

### <a name="view-the-public-ip-address-usage-information"></a>查看公共 IP 地址用量信息

查看区域中已使用的公共 IP 地址总数：

1. 在 Azure Stack Hub 管理员门户中，选择“所有服务”。  然后，在“管理”  类别下，选择“网络”  。
1. “网络”窗格会在“概览”部分显示“公共 IP 池用量”磁贴。   

    ![Azure Stack Hub 管理员门户中的“网络资源提供程序”窗格](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-01.png)

“已用”数字代表公共 IP 地址池中已分配的公共 IP 地址数目。  “可用”数字代表公共 IP 地址池中尚未分配的仍可使用的公共 IP 地址数目。  “已用百分比”数字代表已使用或已分配地址占该位置公共 IP 地址池中公共 IP 地址总数的百分比。 

### <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>查看租户订阅创建的公共 IP 地址

在“租户资源”下选择“公共 IP 地址”。   查看特定区域中通过租户订阅显式创建的公共 IP 地址列表。

![Azure Stack Hub 管理员门户中的租户公共 IP 地址](media/azure-stack-viewing-public-ip-address-consumption/ip-address-consumption-02.png)

可能会看到，某些动态分配的公共 IP 地址显示在列表中， 但这些地址没有关联的地址。 此地址资源已在网络资源提供程序中创建，但尚未在网络控制器中创建。

在将某个地址绑定到接口、网络接口卡 (NIC)、负载均衡器或虚拟网关之前，网络控制器不会将该地址分配给此资源。 将公共 IP 地址绑定到接口后，网络控制器会分配一个 IP 地址。 此地址显示在“地址”字段中。 

### <a name="view-the-public-ip-address-information-summary-table"></a>查看公共 IP 地址信息摘要表

在不同情况下，分配的公共 IP 地址决定了要将地址显示在哪个列表中。

| **公共 IP 地址分配案例** | **显示在用量摘要中** | **显示在租户公共 IP 地址列表中** |
| --- | --- | --- |
| 尚未分配给 NIC 或负载均衡器（暂时性）的动态公共 IP 地址。 |否 |是 |
| 已分配给 NIC 或负载均衡器的动态公共 IP 地址。 |是 |是 |
| 已分配给租户 NIC 或负载均衡器的静态公共 IP 地址。 |是 |是 |
| 已分配给结构基础结构服务终结点的静态公共 IP 地址。 |是 |否 |
| 为 IaaS VM 实例隐式创建的、在虚拟网络上用于出站 NAT 的公共 IP 地址。 每当租户创建 VM 实例，使 VM 能够将信息发送到 Internet 时，将在幕后创建这些地址。 |是 |否 |

## <a name="next-steps"></a>后续步骤

[在 Azure Stack Hub 中管理存储帐户](azure-stack-manage-storage-accounts.md)
