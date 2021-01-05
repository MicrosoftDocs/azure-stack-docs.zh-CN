---
title: Azure Stack Hub Capacity Planner
description: 了解如何使用 Azure Stack Hub Capacity Planner 电子表格进行部署。
author: PatAltimore
ms.topic: article
ms.date: 05/31/2019
ms.author: patricka
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: d78d52bf28671527bd5f9dbfd80efef401888264
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871525"
---
# <a name="azure-stack-hub-capacity-planner"></a>Azure Stack Hub Capacity Planner

Azure Stack Hub Capacity Planner 是显示不同计算资源分配如何配合硬件产品/服务选择的电子表格。

## <a name="worksheet-descriptions"></a>工作表说明

下表描述了 Azure Stack Hub Capacity Planner 中每个工作表（可从 [https://aka.ms/azstackcapacityplanner](https://aka.ms/azstackcapacityplanner) 下载）。

|工作表名称|说明|
|-----|-----|
|Version-Disclaimer|计算器用途、版本号和发行日期。|
|Instructions|有关为虚拟机 (VM) 集合的容量规划建模的分步说明。|
|DefinedSolutionSKUs|包含最多五个硬件定义的表。 这些条目都是示例。 请根据要考虑的系统配置更改详细信息。|
|DefineByVMFootprint|将配置与不同的 VM 大小和数量进行比较，以找出适当的硬件 SKU。|
|DefineByWorkloadFootprint|通过创建 Azure Stack Hub 工作负荷的集合来找到相应的硬件 SKU。|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs 说明

此工作表最多包含五个硬件定义示例。 请根据要考虑的系统配置更改详细信息。

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>由授权硬件合作伙伴提供的硬件选择

Azure Stack Hub 作为由解决方案合作伙伴安装了软件的集成系统提供。 解决方案合作伙伴提供他们自己的 Azure Stack Hub 容量规划工具授权版本。 最终的解决方案容量介绍中使用了这些工具。

### <a name="multiple-ways-to-model-computing-resources"></a>计算资源建模的多种方式

Azure Stack Hub Capacity Planner 内的资源模型取决于各种不同的 Azure Stack Hub VM 大小。 VM 大小的范围从最小的“基本 0”到最大的 Standard_Fsv2。 可以使用两种方式为计算资源分配建模：

- 选择特定的硬件产品/服务，并查看各种资源的组合中有哪些适用。

- 创建 VM 分配的特定组合，并让 Azure 资源计算器显示哪些可用硬件 SKU 能够支持该 VM 配置。

此工具提供了两种用于分配 VM 资源的方法：作为 VM 资源分配的单个集合，或者作为最多六个不同工作负荷配置的集合。 每个工作负荷配置可以包含可用 VM 资源的一个不同分配。 以下部分逐步说明如何创建和使用其中的每个分配模型。 包含于非背景阴影数据格，或此任务表之 SKU 下拉列表内的唯一值，应进行修改。阴影数据格内所作之更改可能中断资源的计算。 在着色的单元格内所做的更改可能会破坏资源计算。

## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint 说明

若要使用各种大小和数量的 VM 的单个集合创建模型，请选择“DefineByVMFootprint”选项卡并执行以下步骤  ：

1. 在此工作表的右上角，使用提供的下拉列表框控件选择想要安装在每个硬件系统 (SKU) 中的服务器的初始数量（在 4 到 16 之间）。 在建模过程可以随时修改此服务器数量，以便查看此值如何影响资源分配模型的整体可用资源。
2. 如果要针对一个特定的硬件配置为各种 VM 资源分配建模，请在页面的右上角找到“当前 SKU”选项卡正下方的蓝色下拉列表框  。 下拉此列表框，然后选择所需的硬件 SKU。
3. 现在可以开始将各种大小的 VM 添加到模型。 若要包含特定 VM 类型，请将数量值输入到该 VM 条目左侧的蓝色空心框中。

   > [!NOTE]
   > VM 存储总量是指 VM 的数据磁盘的总容量（受支持的磁盘的数目乘以单个磁盘的最大容量 [1 TB]）。 我们已根据配置指示器填充了“可用存储配置”表，以便你可以为每个 Azure Stack Hub VM 选择所需级别的存储资源。 但是，请务必注意，你可以根据需要添加或更改“可用存储配置”表。 <br><br>每个 VM 从最初分配的本地临时存储开始。 为反映临时存储的精简预配，local-temp 数量可以更改为包括最大可允许临时存储量的下拉菜单中的任何内容。

4. 添加 VM 时，你将看到显示可用 SKU 资源更改的图表。 在这些图表中可以查看在建模过程中添加各种大小和数量的 VM 的效果。 查看更改效果的另一种方法是观察“可用 VM”列表正下方列出的“已使用”和“仍可用”数目   。 这些数字反映了基于当前所选硬件 SKU 估计的值。
5. 创建 VM 集后，可以选择“建议的 SKU”查找建议的硬件 SKU。  此按钮位于页面的右上角，紧靠在“当前 SKU”标签之下。  使用此按钮，可以随后修改 VM 配置并查看哪一硬件支持每个配置。

## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint 说明

若要使用 Azure Stack Hub 工作负荷的集合创建模型，请选择“DefineByWorkloadFootprint”选项卡并执行这一序列的步骤  。 使用可用 VM 资源创建 Azure Stack Hub 工作负荷。

> [!TIP]
> 若要为 Azure Stack Hub VM 更改提供的存储大小，请参阅上一部分的步骤 3 中的说明。

1. 在此工作表的右上角，使用提供的下拉列表框控件选择想要安装在每个硬件系统 (SKU) 中的服务器的初始数量（在 4 到 16 之间）。
2. 如果要针对一个特定的硬件配置为各种 VM 资源分配建模，请在页面的右上角找到“当前 SKU”选项卡正下方的蓝色下拉列表框  。 下拉此列表框，然后选择所需的硬件 SKU。
3. 在“DefineByVMFootprint”页上，为所需的每个 Azure Stack Hub VM 选择适当的存储大小。  上一部分的步骤 3 已介绍此过程。 每个 VM 的存储大小在 DefineByVMFootprint 工作表中定义。
4. 从“DefineByWorkloadFootprint”页的左上角开始，针对最多六个不同的工作负荷类型创建配置  。 为每个包含在该工作负荷中的 VM 类型输入数量。 为此，可将数值放入该工作负荷的名称正下方的列中。 可以修改工作负荷名称以反映此特定配置将支持的工作负荷类型。
5. 可以包括每个工作负荷类型的特定数量，方法是在“数量”标签正下方该列的底部输入一个值  。
6. 创建好工作负荷类型和数量后，选择页右上角“当前 SKU”标签正下面的“建议的 SKU”   。 此时会显示具有足够的资源、可支持此工作负荷的整体配置的最小 SKU。
7. 进一步的建模可通过修改为硬件 SKU 选择的服务器数量或更改工作负荷配置中的 VM 分配或数量完成。 关联的图表将显示说明所做的更改如何影响总体资源消耗情况的即时反馈。
8. 如果你对更改感到满意，请再次选择“建议的 SKU”，以显示新配置的建议 SKU。  也可以从下拉菜单中选择所需的 SKU。

## <a name="next-steps"></a>后续步骤

了解 [Azure Stack Hub 的数据中心集成注意事项](azure-stack-datacenter-integration.md)。
