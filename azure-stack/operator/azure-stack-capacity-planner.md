---
title: Azure Stack 容量计划程序 |Microsoft Docs
description: 了解适用于 Azure Stack 部署的容量规划。
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
ms.openlocfilehash: 98dafb9df568e5e14e5f1890103d0261e07beb98
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131353"
---
# <a name="azure-stack-capacity-planner"></a>Azure Stack Capacity Planner

Azure Stack 容量规划器是一个电子表格，显示了如何将不同的计算资源分配适合跨多个选中的硬件产品/服务。 

## <a name="worksheet-descriptions"></a>工作表说明
下表描述了每个工作表中可以从下载 Azure Stack Capacity Planner [ https://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner)。 

|工作表名称|描述|
|-----|-----|
|Version-Disclaimer|计算器、 版本号和发行日期的目的。|
|说明|为模型的容量规划一系列虚拟机 (Vm) 的分步说明。|
|DefinedSolutionSKUs|具有最多五个硬件定义表。 项是示例。 更改以匹配正在考虑的系统配置的详细信息。|
|DefineByVMFootprint|通过比较使用不同的大小和数量的 Vm 配置找到合适的硬件 SKU。|
|DefineByWorkloadFootprint|通过创建 Azure Stack 工作负荷的集合来找到相应的硬件 SKU。|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs 说明
此工作表最多包含五个硬件定义示例。 更改以匹配正在考虑的系统配置的详细信息。

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>由经过授权的硬件合作伙伴提供的硬件选择
Azure Stack 作为由解决方案合作伙伴安装了软件的集成系统提供。 解决方案合作伙伴提供他们自己的 Azure Stack 容量计划工具的权威版本。 使用这些工具执行最终的解决方案容量的讨论。

### <a name="multiple-ways-to-model-computing-resources"></a>通过多种方式来模型计算资源
资源在 Azure Stack Capacity Planner 建模取决于各种大小的 Azure Stack Vm。 Vm 的大小从最小、 基本 0，最多的最大，Standard_Fsv2 的范围。 您可以通过两种方式建模计算资源分配：

- 选择特定的硬件产品/服务，并查看各种资源的组合适合。 

- 创建的 VM 分配的特定组合，让 Azure 资源计算器显示的可用硬件 Sku 能够支持该 VM 配置。

此工具提供了用于分配 VM 资源的两个方法： 为 VM 的资源分配的一个单一集合，或最多六个不同的工作负荷配置的集合。 每个工作负荷配置可以包含不同的分配的可用 VM 资源。 下一步部分介绍了创建和使用每个这些分配模型的分步说明。 应修改包含在非背景阴影的单元格，或在此工作表上的 SKU 的下拉列表中的唯一值。 阴影的单元格内所做的更改可能会中断资源的计算。


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint 说明
若要使用的各种大小和数量的 Vm 的单个集合创建一个模型，请选择**DefineByVMFootprint**选项卡，并按照以下步骤：

1. 在此工作表的右上角，使用提供的下拉列表框控件，选择你想在每个硬件系统 (SKU) 中安装的服务器 （4 到 16 个，） 的初始数字。 可以在建模过程来查看它如何影响总体期间随时修改此数量的服务器的可用资源以了解你的资源分配模型。
2. 如果你想要针对一个特定的硬件配置的各种 VM 资源分配的模型，发现下方的蓝色下拉列表框**当前 SKU**页面的右上角中的标签。 下拉列表框中，然后选择你所需的硬件 SKU。
3. 现在，你准备好开始将各种大小的 Vm 添加到您的模型。 若要包含特定 VM 类型，请将数量值输入到该 VM 条目左侧的蓝色空心框中。

   > [!NOTE]
   > 总 VM 存储是指 VM （支持乘以 [1 TB] 的单个磁盘的最大容量的磁盘数） 的数据磁盘的总容量。 根据配置指示器，我们填充了可用的存储配置表中，因此，可以为每个 Azure Stack VM 选择所需的存储资源级别。 但是，务必要注意，您可以添加或更改在必要时可用的存储配置表。<br><br>每个 VM 从最初分配的本地临时存储开始。 以反映临时存储精简预配，可以更改本地 temp 数为下拉列表菜单，其中包括允许的最大临时存储中的任何内容。

4. 添加 Vm 时，你将看到的图表，显示可用的 SKU 资源更改。 这样，就可以查看在建模过程中添加各种大小和数量的 VM 的效果。 若要查看更改效果的另一个方法是观看**已使用**和**仍然可用**列出可用的 Vm 列表下方的号码。 这些数字反映了基于当前所选硬件 SKU 估计的值。
5. 创建您的 Vm 集后，您可以通过选择查找推荐的硬件 SKU**建议的 SKU**，在页面右上角下方**当前 SKU**标签。 使用此按钮，可以随后修改 VM 配置并查看哪一硬件支持每个配置。


## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint 说明
若要使用 Azure Stack 工作负载的集合创建一个模型，请选择**DefineByWorkloadFootprint**选项卡，并按照以下顺序的步骤。 使用可用的 VM 资源创建 Azure Stack 工作负荷。   

> [!TIP]
> 若要为 Azure Stack VM 更改提供的存储大小，请参阅上一部分的步骤 3 中的说明。

1. 在此工作表的右上角，使用提供的下拉列表框控件，选择你想在每个硬件系统 (SKU) 中安装的服务器 （4 到 16 个，） 的初始数字。
2. 如果你想要针对一个特定的硬件配置的各种 VM 资源分配的模型，发现下方的蓝色下拉列表框**当前 SKU**页面的右上角中的标签。 下拉列表框中，然后选择你所需的硬件 SKU。
3. 选择适当的存储大小为每个所需的 Azure Stack Vm 上**DefineByVMFootprint**页上，第三步的上一节中所述。 每个 VM 的存储大小在 DefineByVMFootprint 工作表中定义。
4. 在右上角向左的起始**DefineByWorkloadFootprint**页上，为最多六个不同工作负荷类型创建的配置。 输入该工作负荷中包含每个 VM 类型的数量。 通过将数值放入该工作负荷的名称的正下方的列执行此操作。 您可以修改工作负荷名称以便反映将受此特定配置的工作负荷的类型。
5. 可以通过输入的正下方的底部的列的值包括特定数量的每个工作负荷类型**数量**标签。
6. 已创建工作负荷类型和数量覆盖范围，选择**建议的 SKU**页上，右上角下方**当前 SKU**标签。 此时将显示与具有足够资源的最小 SKU 以支持此工作负荷的整体配置。
7. 您可以实现进一步建模的修改硬件 SKU，所选服务器的数目或更改的 VM 分配或工作负荷配置中的数量。 关联的图表显示即时反馈，显示所做的更改如何影响总体资源消耗。
8. 如果所做的更改满意后，选择**建议的 SKU**再次以显示适用于您的新配置的建议的 SKU。 此外可以选择下拉菜单以选择所需的 SKU。

## <a name="next-steps"></a>后续步骤
了解如何[适用于 Azure Stack 数据中心集成注意事项](azure-stack-datacenter-integration.md)。
