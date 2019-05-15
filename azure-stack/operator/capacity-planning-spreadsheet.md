---
title: 适用于 Azure Stack 的容量规划电子表格 |Microsoft Docs
description: 了解适用于 Azure Stack 部署的容量规划电子表格。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: prchint
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 1756499b7736c309b07856bf5edfba45f3c0e11a
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618871"
---
# <a name="azure-stack-capacity-planner"></a>Azure Stack Capacity Planner
Azure Stack Capacity Planner 是用于 Azure Stack 资源容量规划的电子表格。 使用该容量规划器可以设计计算资源的各种分配，并查看这些分配如何与所选的硬件产品/服务相适应。 下面提供了有关如何使用 Azure Stack 计算器的详细说明。

## <a name="worksheet-descriptions"></a>工作表说明
下面是可以从 [https://aka.ms/azstackcapacityplanner](https://aka.ms/azstackcapacityplanner) 下载的 Azure Stack Capacity Planner 电子表格中所含工作表的简短说明：

|标签名称|描述|
|-----|-----|
|Version-Disclaimer|计算器用途、版本号和发行日期的简要概述。|
|说明|提供有关如何使用 Azure Stack Capacity Planner 的详细说明。|
|DefinedSolutionSKUs|最多包含五个硬件定义的多列表。 此工作表中的条目为示例。 其用途是供用户更改详细信息，以匹配正在考虑使用或购买的系统配置。|
|DefineByVMFootprint|通过创建各种大小和数量的虚拟机的集合来找到相应的硬件 SKU。|
|DefineByWorkloadFootprint|通过创建 Azure Stack 工作负荷的集合来找到相应的硬件 SKU。|
|  |  |

## <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs 说明
此工作表最多包含五个硬件定义示例。 更改详细信息以匹配正在考虑使用或购买的系统配置。

### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>由授权硬件合作伙伴提供的硬件选择
Azure Stack 作为由解决方案合作伙伴安装了软件的集成系统提供。 这些解决方案合作伙伴将拥有自己的 Azure Stack 容量计划工具授权版本，并且这些工具应当用来结束解决方案容量讨论。

### <a name="multiple-ways-to-model-computing-resources"></a>用于对计算资源建模的多种方式
Azure Stack 规划器内用于资源建模的基本构建基块是各种大小的 Azure Stack 虚拟机 (VM)。 这些 VM 大小的范围从最小的“基本 0”到当前最大的 VM“Standard_Fsv2”。 根据你的需求，可以采用两种不同方式，通过此工具来使用各种数量的不同 VM 创建计算资源分配。

1. 规划器用户选择特定的硬件产品/服务，然后查看各种资源的哪些组合适合此硬件资源。 

2. 规划器用户创建 VM 分配的特定组合，然后让 Azure 资源计算器显示哪些可用硬件 SKU 能够支持该 VM 配置。

此工具提供了两种用于分配 VM 资源的方法：作为 VM 资源分配的单个集合，或者作为最多六个不同工作负荷配置的集合。 每个工作负荷配置可以包含可用 VM 资源的一个不同分配。 下面提供了用于创建和使用其中每种分配模式的详细信息。 只应修改此工作表上背景未着色的单元格中包含的值或 SKU 下拉列表中的值。 在着色的单元格内所做的更改可能会破坏资源计算。


## <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint 说明
若要使用各种大小和数量的 VM 的单个集合创建模型，请选择“DefineByVMFootprint”标签并执行这一序列的步骤。

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
8. 对所做的更改满意后，再次单击“建议的 SKU”按钮会显示为新配置建议的 SKU。


## <a name="next-steps"></a>后续步骤
了解 [Azure Stack 的数据中心集成注意事项](azure-stack-datacenter-integration.md)
