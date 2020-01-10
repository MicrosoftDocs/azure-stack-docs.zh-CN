---
title: Azure Stack 中心 Capacity Planner |Microsoft Docs
description: 了解 Azure Stack 中心部署的容量规划。
services: azure-stack
documentationcenter: ''
author: prchint
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: deff6d15374105b56e504da4edf89eecc8f6a640
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75816847"
---
# <a name="azure-stack-hub-capacity-planner"></a>Azure Stack 中心 Capacity Planner

Azure Stack 中心 Capacity Planner 是一个电子表格，其中显示了不同的计算资源分配如何适应选择的硬件产品。 

## <a name="worksheet-descriptions"></a>工作表说明
下表描述了 Azure Stack 中心 Capacity Planner 中的每个工作表，可以从[https://aka.ms/azstackcapacityplanner](https://aka.ms/azstackcapacityplanner)下载该工作表。 

|工作表名称|Description|
|-----|-----|
|版本免责声明|计算器的用途、版本号和发布日期。|
|Instructions|为虚拟机（Vm）的集合制定容量规划模型的分步说明。|
|DefinedSolutionSKUs|表，最多有五个硬件定义。 这些项是示例。 更改详细信息以匹配正在考虑的系统配置。|
|DefineByVMFootprint|通过比较不同大小和 Vm 数量的配置，查找相应的硬件 SKU。|
|DefineByWorkloadFootprint|通过创建 Azure Stack 集线器工作负载的集合来查找适当的硬件 SKU。|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs 说明
此工作表最多包含五个硬件定义示例。 更改详细信息以匹配正在考虑的系统配置。

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>授权硬件合作伙伴提供的硬件选择
Azure Stack 中心作为集成系统提供，由解决方案合作伙伴安装软件。 解决方案合作伙伴提供自己的 Azure Stack 集线器容量规划工具的权威版本。 使用这些工具最终讨论解决方案容量。

### <a name="multiple-ways-to-model-computing-resources"></a>建模计算资源的多种方法
Azure Stack 中心内的资源建模 Capacity Planner 取决于 Azure Stack 集线器 Vm 的各种大小。 Vm 的大小从最小的基本0到最大的 Standard_Fsv2。 可以通过两种不同的方式为计算资源分配建模：

- 选择特定的硬件产品，并查看各种资源的组合。 

- 创建 VM 分配的特定组合，并让 Azure 资源计算器显示能够支持此 VM 配置的可用硬件 Sku。

此工具提供了两种分配 VM 资源的方法：作为 VM 资源分配的单个集合，或者作为最多六个不同工作负荷配置的集合。 每个工作负荷配置可以包含不同的可用 VM 资源分配。 下一节将介绍如何创建和使用其中每个分配模型的分步说明。 应修改非背景阴影单元中或此工作表上 SKU 下拉列表中包含的值。 在阴影单元格中所做的更改可能会破坏资源计算。


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint 说明
若要使用各种大小和 Vm 数量的单个集合创建模型，请选择 " **DefineByVMFootprint** " 选项卡，然后执行以下步骤：

1. 在此工作表的右上角，使用提供的下拉列表框控件来选择要在每个硬件系统（SKU）中安装的初始服务器数（4到16个）。 在建模过程中，可以随时修改此数量的服务器，以了解这会如何影响资源分配模型的总体可用资源。
2. 如果要针对某个特定硬件配置对各种 VM 资源分配进行建模，请在页面右上角的 "**当前 SKU** " 标签下直接找到蓝色下拉列表框。 下拉此列表框，然后选择所需的硬件 SKU。
3. 你现在已准备好开始将各种大小的 Vm 添加到你的模型。 若要包含特定 VM 类型，请在该 VM 条目左侧的蓝色 "带外框" 框中输入数量值。

   > [!NOTE]
   > VM 总存储空间指的是 VM 数据磁盘的总容量（受支持磁盘的数量乘以单个磁盘的最大容量 [1 TB]）。 根据配置指示器，我们已填充可用的存储配置表，因此，你可以为每个 Azure Stack 中心 VM 选择所需的存储资源级别。 但是，请务必注意，可以根据需要添加或更改可用存储配置表。<br><br>每个 VM 都以最初分配的本地临时存储开始。 若要反映 temp 存储的精简设置，可以将本地-临时号码更改为下拉菜单中的任何内容，包括允许的最大临时存储量。

4. 添加 Vm 时，会看到图表显示了可用的 SKU 资源的变化。 这使您可以在建模过程中查看添加各种 Vm 大小和数量的影响。 查看更改效果的另一种方法是，观看可用 Vm 列表正下方列出的已**使用**和**仍可用**的数字。 这些数字根据当前所选的硬件 SKU 来反映估算值。
5. 创建 Vm 集后，可以通过选择页面右上角的 "**建议的 sku**"，在**当前 SKU**标签正下方找到建议的硬件 sku。 使用此按钮，你可以修改 VM 配置，并查看支持每个配置的硬件。


## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint 说明
若要使用 Azure Stack 中心工作负载的集合创建模型，请选择 " **DefineByWorkloadFootprint** " 选项卡，然后按照此顺序执行此步骤。 使用可用 VM 资源创建 Azure Stack 集线器工作负荷。   

> [!TIP]
> 若要更改为 Azure Stack 中心 VM 提供的存储大小，请参阅上一节中步骤3中的注释。

1. 在此工作表的右上角，使用提供的下拉列表框控件来选择要在每个硬件系统（SKU）中安装的初始服务器数（4到16个）。
2. 如果要针对某个特定硬件配置对各种 VM 资源分配进行建模，请在页面右上角的 "**当前 SKU** " 标签下直接找到蓝色下拉列表框。 下拉此列表框，然后选择所需的硬件 SKU。
3. 在**DefineByVMFootprint**页上，为每个所需的 Azure Stack 中心 vm 选择适当的存储大小，如前一部分中的步骤3所述。 DefineByVMFootprint 工作表中定义了每个 VM 的存储大小。
4. 从**DefineByWorkloadFootprint**页的左上角开始，为多达六个不同的工作负荷类型创建配置。 输入该工作负荷中包含的每个 VM 类型的数量。 为此，可将数值置于该工作负荷的名称下直接的列中。 您可以修改工作负荷名称以反映此特定配置将支持的工作负荷的类型。
5. 您可以通过在 "**数量**" 标签的正下方输入列底部的值，来包含每个工作负荷类型的特定数量。
6. 创建工作负荷类型和数量后，请选择页面右上角的 "**建议的 sku** "，并将其直接置于**当前 SKU**标签之下。 这会显示具有足够资源的最小 SKU，以支持工作负荷的整体配置。
7. 可以通过修改为硬件 SKU 选择的服务器数，或更改工作负荷配置中的 VM 分配或数量，来完成进一步的建模。 关联的关系图显示即时反馈，其中显示了所做更改对总体资源消耗的影响。
8. 如果对更改感到满意，请再次选择**建议的 sku** ，以显示建议用于新配置的 sku。 你还可以选择下拉菜单来选择所需的 SKU。

## <a name="next-steps"></a>后续步骤
了解[Azure Stack 集线器的数据中心集成注意事项](azure-stack-datacenter-integration.md)。
