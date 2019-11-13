---
title: Azure Stack 中的配额类型 |Microsoft Docs
titleSuffix: Azure Stack
description: 查看和编辑 Azure Stack 中可用于服务和资源的不同配额类型。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: xiaofmao
ms.lastreviewed: 12/07/2018
ms.openlocfilehash: 29a154c5c446019e762b1312b9ef2f8a23cc4790
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955303"
---
# <a name="quota-types-in-azure-stack"></a>Azure Stack 中的配额类型

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*

[配额](service-plan-offer-subscription-overview.md#plans)定义用户订阅可以预配或使用的资源限制。 例如，配额可能允许用户最多创建五个虚拟机（Vm）。 每个资源可以有其自己的配额类型。

> [!IMPORTANT]
> 可能需要长达两个小时的时间，新配额才能在用户门户中使用，或在强制更改配额之前出现。

## <a name="compute-quota-types"></a>计算配额类型

| 类型 | **默认值** | **说明** |
| --- | --- | --- |
| 最大 Vm 数 | 50 | 订阅可以在此位置创建的 Vm 的最大数量。 |
| VM 核心的最大数目 | 100 | 订阅可以在此位置创建的内核的最大数目（例如，A3 VM 具有四个核心）。 |
| 可用性集的最大数目 | 10 | 可在此位置创建的可用性集的最大数量。 |
| 虚拟机规模集的最大数量 | 100 | 可在此位置创建的规模集的最大数目。 |
| 标准托管磁盘的最大容量（以 GB 为限） | 2048 | 可在此位置创建的标准托管磁盘的最大容量。 此值是所有标准托管磁盘的分配大小和所有标准快照的已使用大小的总和。 |
| 高级托管磁盘的最大容量（以 GB 为限） | 2048 | 可在此位置创建的高级托管磁盘的最大容量。 此值是所有高级托管磁盘的分配大小和所有高级快照的已使用大小的总和。 |

> [!NOTE]
> 非托管磁盘（页 blob）的最大容量不同于托管磁盘配额。 可以在 "**存储配额**" 的 "**最大容量（GB）** " 中设置此值。

## <a name="storage-quota-types"></a>存储配额类型

| **Item** | **默认值** | **说明** |
| --- | --- | --- |
| 最大容量（GB） |2048 |此位置中的订阅可以使用的总存储容量。 此值是所有 blob （包括非托管磁盘）和所有关联的快照、表、队列所用的总大小。 |
| 存储帐户总数 |20 |订阅可以在此位置创建的存储帐户的最大数量。 |

> [!NOTE]
> 当一个订阅中超过**最大容量（GB）** 时，将无法在此订阅中创建新的存储资源。 但你可以在 Vm 中使用在此订阅中创建的非托管磁盘 continuely，这可能会导致总使用的容量超出配额限制。<br>托管磁盘的最大容量不同于总存储配额。 可以在 "**计算配额**" 中设置此值。

## <a name="network-quota-types"></a>网络配额类型

| **Item** | **默认值** | **说明** |
| --- | --- | --- |
| 最大虚拟网络 |50 |订阅可以在此位置创建的虚拟网络的最大数量。 |
| 最大虚拟网络网关 |第 |订阅可以在此位置创建的虚拟网络网关（VPN 网关）的最大数量。 |
| 最大网络连接 |2 |订阅可以在此位置中所有虚拟网络网关创建的网络连接的最大数量（点到点或站点到站点）。 |
| 最大公共 Ip |50 |订阅可以在此位置创建的公共 IP 地址的最大数目。 |
| 最大 Nic |100 |订阅可以在此位置创建的网络接口的最大数目。 |
| 最大负载均衡器 |50 |订阅可以在此位置创建的负载均衡器的最大数目。 |
| 最大网络安全组 |50 |订阅可以在此位置创建的网络安全组的最大数量。 |

## <a name="view-an-existing-quota"></a>查看现有配额

可以通过两种不同的方式查看现有配额：

### <a name="plans"></a>计划

1. 在管理员门户的左侧导航窗格中，选择 "**计划**"。
2. 通过单击其名称，选择要查看其详细信息的计划。
3. 在打开的边栏选项卡中，选择 "**服务和配额**"。
4. 在 "**名称**" 列中单击要查看的配额。

    [![Azure Stack 管理员门户中的配额](media/azure-stack-quota-types/quotas1sm.png "在管理员门户中查看配额")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>资源提供程序

1. 在管理员门户的默认仪表板上，找到 "**资源提供程序**" 磁贴。
2. 选择要查看其配额的服务，如**计算**、**网络**或**存储**。
3. 选择 "**配额**"，然后选择要查看的配额。

## <a name="edit-a-quota"></a>编辑配额

可以通过两种不同的方式编辑配额：

### <a name="edit-a-plan"></a>编辑计划

1. 在管理员门户的左侧导航窗格中，选择 "**计划**"。
2. 通过单击其名称来选择要编辑配额的计划。
3. 在打开的边栏选项卡中，选择 "**服务和配额**"。
4. 在 "**名称**" 列中单击要编辑的配额，将其选中。

    [![Azure Stack 管理员门户中的配额](media/azure-stack-quota-types/quotas1sm.png "在管理员门户中查看配额")](media/azure-stack-quota-types/quotas1.png#lightbox)

5. 在打开的边栏选项卡中，选择 "**计算**"、"**在网络中编辑**" 或 "**在存储中编辑**"。

    ![在 Azure Stack 管理员门户中编辑计划](media/azure-stack-quota-types/quotas3.png "在 Azure Stack 管理员门户中编辑计划")

或者，您可以按照以下步骤编辑配额：

1. 在管理员门户的默认仪表板上，找到 "**资源提供程序**" 磁贴。
2. 选择要修改其配额的服务，如**计算**、**网络**或**存储**。
3. 接下来，选择 "**配额**"，然后选择要更改的配额。
4. 在 "**设置存储配额**"、"**设置计算配额**" 或 "**设置网络配额**" 窗格上（具体取决于你选择编辑的配额类型），编辑值，然后选择 "**保存**"。

### <a name="edit-original-configuration"></a>编辑原始配置
  
您可以选择编辑配额的原始配置，而不是[使用加载项计划](create-add-on-plan.md)。 编辑配额时，新配置会自动应用到使用该配额的所有计划以及使用这些计划的所有现有订阅。 配额的编辑不同于使用外接程序计划提供已修改的配额，用户选择订阅该配额。

配额的新值全局应用于所有使用修改的配额的计划，以及所有使用这些计划的现有订阅。

## <a name="next-steps"></a>后续步骤

- [了解有关服务、计划、产品/服务和配额的详细信息。](service-plan-offer-subscription-overview.md)
- [创建计划时创建配额。](azure-stack-create-plan.md)
