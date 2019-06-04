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
ms.openlocfilehash: aec1d6fab044c731501433e6578257e8b09570eb
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2019
ms.locfileid: "66460999"
---
# <a name="azure-stack-capacity-planner"></a>Azure Stack Capacity Planner

Azure Stack 容量规划器是一个电子表格，显示了如何将不同的计算资源分配适合跨多个选中的硬件产品/服务。 

## <a name="worksheet-descriptions"></a>工作表说明
下表包含每个工作表的说明在 Azure Stack 容量规划器，可以从下载[ https://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner)。 

|标签名称|描述|
|-----|-----|
|Version-Disclaimer|计算器、 版本号和发行日期的目的。|
|说明|为模型的容量规划的虚拟机集合的分步说明。|
|DefinedSolutionSKUs|具有最多五个硬件定义表。 项是示例。 更改以匹配正在考虑的系统配置的详细信息。|
|DefineByVMFootprint|通过比较使用不同的大小和数量的 Vm 配置找到合适的硬件 SKU。|
|DefineByWorkloadFootprint|通过创建 Azure Stack 工作负荷的集合来找到相应的硬件 SKU。|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs 说明
此工作表最多包含五个硬件定义示例。 更改以匹配正在考虑的系统配置的详细信息。

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>由授权硬件合作伙伴提供的硬件选择
Azure Stack 作为由解决方案合作伙伴安装了软件的集成系统提供。 解决方案合作伙伴提供他们自己的 Azure Stack 容量计划工具的权威版本。 使用这些工具执行最终的解决方案容量的讨论。

### <a name="multiple-ways-to-model-computing-resources"></a>通过多种方式来模型计算资源
资源在 Azure Stack planner 建模取决于各种大小的 Azure Stack Vm。 Vm 的大小从最小的基本 0 到最大 Standard_Fsv2 的范围。 您可以通过两种方式建模计算资源分配：

- 选择特定的硬件产品/服务并查看各种资源的组合适合。 

- 创建的 VM 分配的特定组合，让 Azure 资源计算器显示的可用硬件 Sku 能够支持该 VM 配置。

此工具提供了用于分配 VM 资源的两个方法： 为 VM 的资源分配的一个单一集合，或最多六个不同的工作负荷配置的集合。 每个工作负荷配置可以包含可用 VM 资源的一个不同分配。 下一步部分介绍了创建和使用每个这些分配模型的分步说明。 只应修改此工作表上背景未着色的单元格中包含的值或 SKU 下拉列表中的值。 在着色的单元格内所做的更改可能会破坏资源计算。


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint 说明
若要创建使用各种大小和数量的 Vm 的单个集合的模型，选择"DefineByVMFootprint"选项卡，并按照以下步骤：

1. 在此工作表的右上角，使用提供的下拉列表框控件选择想要安装在每个硬件系统 (SKU) 中的服务器的初始数量（在 4 到 16 之间）。 在建模过程可以随时修改此服务器数量，以便查看此值如何影响资源分配模型的整体可用资源。
2. 如果要针对一个特定的硬件配置为各种 VM 资源分配建模，请在页面的右上角找到“当前 SKU”标签正下方的蓝色下拉列表框。 下拉此列表框，然后选择所需的硬件 SKU。
3. 现在就可以开始将各种大小的 VM 添加到模型了。 若要包含特定 VM 类型，请将数量值输入到该 VM 条目左侧的蓝色空心框中。

   > [!NOTE]
   > VM 存储总量是指 VM 的数据磁盘的总容量（受支持的磁盘的数目 * 单个磁盘的最大容量 (1 TB)）。 我们已根据配置指示器填充了“可用存储配置”表，以便你可以为每个 Azure Stack VM 选择所需级别的存储资源。 但是，请务必注意，你可以根据需要添加或更改“可用存储配置”表。<br><br>每个 VM 从最初分配的本地临时存储开始。 为反映临时存储的精简预配，local-temp 数量可以更改为包括最大可允许临时存储量的下拉菜单中的任何内容。

4. 添加 VM 时，你将看到显示可用 SKU 资源更改的图表。 这样，就可以查看在建模过程中添加各种大小和数量的 VM 的效果。 查看更改效果的另一种方法是观察“可用 VM”列表正下方列出的“已使用”和“仍可用”数目。 这些数字反映了基于当前所选硬件 SKU 估计的值。
5. 创建 VM 集后，可以通过单击页面右上角的“建议的 SKU”按钮（位于“当前 SKU”标签正下方）来查找建议的硬件 SKU。 使用此按钮，可以随后修改 VM 配置并查看哪一硬件支持每个配置。


## <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint 说明
若要使用 Azure Stack 工作负荷的集合创建模型，请选择“DefineByWorkloadFootprint”标签并执行这一序列的步骤。 Azure Stack 工作负荷是使用可用 VM 资源创建的。   

> [!TIP]
> 若要为 Azure Stack VM 更改提供的存储大小，请参阅上一部分的步骤 3 中的说明。

1. 在此页面的右上角，使用提供的下拉列表框控件选择想要安装在每个硬件系统 (SKU) 中的服务器的初始数量（在 4 到 16 之间）。
2. 如果要针对一个特定的硬件配置为各种 VM 资源分配建模，请在页面的右上角找到“当前 SKU”标签正下方的蓝色下拉列表框。 下拉此列表框，然后选择所需的硬件 SKU。
3. 在 DefineByVMFootprint 页上为每个所需的 Azure Stack VM 选择适当的大小，如上面 DefineByVMFootprint 说明的步骤 3 所述。 每个 VM 的存储大小在 DefineByVMFootprint 工作表中定义。
4. 从 DefineByWorkloadFootprint 页的左上角开始，通过输入每一工作负荷中包含的每个 VM 类型的数量，为最多六个不同工作负荷类型创建配置。 这通过将数值放入该工作负荷的名称正下方的列中完成。 可以修改工作负荷名称以反映此特定配置将支持的工作负荷类型。
5. 可以包括每个工作负荷类型的特定数量，方法是在“数量”标签正下方该列的底部输入一个值。
6. 创建工作负荷类型和数量后，单击页面右上角的“建议的 SKU”按钮（位于“当前 SKU”标签的正下方）将导致带有足以支持此整体工作负荷配置的资源的最小 SKU 显示出来。
7. 进一步的建模可通过修改为硬件 SKU 选择的服务器数量或更改工作负荷配置中的 VM 分配或数量完成。 关联的图表将显示说明所做的更改如何影响总体资源消耗情况的即时反馈。
8. 当所做的更改满意后时，单击**建议的 SKU**再次将显示适用于您的新配置的建议的 SKU。 您还可以单击下拉菜单以选择所需的 SKU。

## <a name="next-steps"></a>后续步骤
了解 [Azure Stack 的数据中心集成注意事项](azure-stack-datacenter-integration.md)
