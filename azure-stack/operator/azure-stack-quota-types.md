---
title: Azure Stack 中的配额类型 | Microsoft Docs
description: 查看和编辑可用于 Azure Stack 中的服务和资源的不同配额类型。
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
ms.openlocfilehash: e3e7ae6cc29756486ae5c292de6fea7e5259ecc1
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283452"
---
# <a name="quota-types-in-azure-stack"></a>Azure Stack 中的配额类型

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

[配额](service-plan-offer-subscription-overview.md#plans)定义用户订阅可以预配或使用的资源限制。 例如，配额可能允许用户最多创建五个 VM。 每个资源可以有其自己的配额类型。

> [!IMPORTANT]
> 在用户门户中出现可用的新配额或者强制实施更改的配额可能需要长达两小时的时间。

## <a name="compute-quota-types"></a>计算配额类型

| 类型 | **默认值** | **说明** |
| --- | --- | --- |
| 虚拟机的数目上限 | 50 | 订阅可以在此位置创建的虚拟机数目上限。 |
| 虚拟机核心的数目上限 | 100 | 订阅可以在此位置创建的核心数目上限（例如，A3 VM 有四个核心）。 |
| 可用性集的数目上限 | 10 | 可以在此位置创建的可用性集数目上限。 |
| 虚拟机规模集的数目上限 | 100 | 可以在此位置创建的虚拟机规模集数目上限。 |
| 标准托管磁盘的最大容量 (GB) | 2048 | 可以在此位置中创建的标准托管磁盘的最大容量。 |
| 高级托管磁盘的最大容量 (GB) | 2048 | 可以在此位置中创建的高级托管磁盘的最大容量。 |

> [!NOTE]  
> 非托管磁盘（页 Blob）的最大容量独立于托管磁盘配额。 可以在“存储配额”中设置此值。

## <a name="storage-quota-types"></a>存储配额类型

| **Item** | **默认值** | **说明** |
| --- | --- | --- |
| 最大容量 (GB) |2048 |可供此位置的订阅使用的总存储容量（包括 Blob 以及所有相关联的快照、表、队列）。 |
| 存储帐户的总数 |20 |订阅可以在此位置创建的存储帐户数目上限。 |

> [!NOTE]  
> 托管磁盘的最大容量独立于总存储配额。 可以在“计算配额”中设置此值。

## <a name="network-quota-types"></a>网络配额类型

| **Item** | **默认值** | **说明** |
| --- | --- | --- |
| 虚拟网络数目上限 |50 |订阅可以在此位置创建的虚拟网络数目上限。 |
| 虚拟网络网关数目上限 |第 |订阅可以在此位置创建的虚拟网络网关（VPN 网关）数目上限。 |
| 网络连接数目上限 |2 |订阅可以在此位置跨所有虚拟网络网关创建的网络连接（点到点或站点到站点）数目上限。 |
| 公共 IP 数目上限 |50 |订阅可以在此位置创建的公共 IP 地址数目上限。 |
| NIC 数目上限 |100 |订阅可以在此位置创建的网络接口数目上限。 |
| 负载均衡器数目上限 |50 |订阅可以在此位置创建的负载均衡器数目上限。 |
| 网络安全组数目上限 |50 |订阅可以在此位置创建的网络安全组数目上限。 |

## <a name="view-an-existing-quota"></a>查看现有配额

可以通过两种不同的方式查看现有配额：

### <a name="plans"></a>计划

1. 在管理员门户的左侧导航窗格中，选择“计划”。
2. 单击要查看其详细信息的计划的名称，将其选中。
3. 在打开的边栏选项卡中，选择“服务和配额”。
4. 在“名称”列中单击要查看的配额，将其选中。

    [![配额](media/azure-stack-quota-types/quotas1sm.png "查看配额")](media/azure-stack-quota-types/quotas1.png#lightbox)

### <a name="resource-providers"></a>资源提供程序

1. 在管理门户的默认仪表板上，找到“资源提供程序”磁贴。
2. 选择要查看其配额的服务，例如“计算”、“网络”或“存储”。
3. 选择“配额”，然后选择要查看的配额。

## <a name="edit-a-quota"></a>编辑配额

可以通过两种不同的方式编辑配额：

### <a name="edit-a-plan"></a>编辑计划

1. 在管理员门户的左侧导航窗格中，选择“计划”。
2. 单击要编辑其配额的计划的名称，将其选中。
3. 在打开的边栏选项卡中，选择“服务和配额”。
4. 在“名称”列中单击要编辑的配额，将其选中。
    [![配额](media/azure-stack-quota-types/quotas1sm.png "查看配额")](media/azure-stack-quota-types/quotas1.png#lightbox)

5. 在打开的边栏选项卡中，选择“在计算中编辑”、“在网络中编辑”或“在存储中编辑”。
    ![配额](media/azure-stack-quota-types/quotas3.png "查看配额")

或者，可以按照以下过程来编辑配额：

1. 在管理员门户的默认仪表板上，找到“资源提供程序”磁贴。
2. 选择要修改其配额的服务，例如“计算”、“网络”或“存储”。
3. 接下来选择“配额”，然后选择要更改的配额。
4. 在“设置存储配额”、“设置计算配额”或“设置网络配额”窗格（具体取决于选择进行编辑的配额的类型）中编辑值，然后选择“保存”。

### <a name="edit-original-configuration"></a>编辑原始配置
  
可以选择编辑配额的原始配置，而不[使用附加计划](create-add-on-plan.md)。 编辑配额时，新配置会自动全局应用到使用该配额的所有计划，以及使用这些计划的所有现有订阅。 编辑配额的效果不同于使用用户选择订阅的附加计划来提供修改的配额。

该配额的新值将全局应用到使用已修改配额的所有计划，以及使用这些计划的所有现有订阅。

## <a name="next-steps"></a>后续步骤

- [了解有关服务、计划、产品/服务和配额的详细信息。](service-plan-offer-subscription-overview.md)
- [创建计划时创建配额。](azure-stack-create-plan.md)
