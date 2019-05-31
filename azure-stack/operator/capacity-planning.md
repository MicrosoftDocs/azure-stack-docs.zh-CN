---
title: Azure Stack 的容量规划 | Microsoft Docs
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
ms.date: 05/30/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/30/2019
ms.openlocfilehash: a67a5010da2a67fb002a351b04a12fe7671fb73b
ms.sourcegitcommit: 4e0b450c91c6515794b663a39f9a4b8b49999918
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66411704"
---
# <a name="azure-stack-capacity-planning"></a>Azure Stack 容量规划
评估 Azure Stack 解决方案时，必须在硬件配置方面做出选择，因为它直接影响到 Azure Stack 云的总体容量。 这些是有关 CPU、内存密度、存储配置和总体解决方案规模或服务器数目的常规选择。 不同于传统的虚拟化解决方案，简单地评估这些组件并不能很好地确定可用的容量。 这方面的第一个原因是 Azure Stack 构建为在解决方案自身内部托管基础结构或管理组件。 第二个原因是软件的容量的某些解决方案容量的保留用于支持复原，以尽量减少的租户工作负荷造成中断的方式的解决方案更新。 

> [!IMPORTANT]
> 此容量规划信息和[Azure Stack 容量规划器](https://aka.ms/azstackcapacityplanner)是 Azure Stack 规划和配置决策的起点。 它不是作为你自己的调查和分析的替代。 Microsoft 不做任何陈述或者任何明示或暗示的担保，此处提供的信息。
 

Azure Stack 解决方案旨在作为超聚合群集的计算和存储。 聚合允许在群集中，称为共享祑砰甧秖*缩放单位*。 在 Azure Stack 缩放单位提供的可用性和可伸缩性的资源。 扩展单元包含的一组 Azure Stack 服务器，称为*主机*。 基础结构软件驻留在一组 Vm，并共享为租户 Vm 相同的物理服务器。 所有 Azure Stack Vm 然后进行都管理的缩放单位的 Windows Server 群集技术和单独的 HYPER-V 实例。 获取和管理 Azure Stack，简化了缩放单位。 缩放单位还允许用于移动和所有服务 （租户和基础结构） 的可伸缩性的跨 Azure Stack。 

## <a name="azure-stack-compute"></a>Azure Stack 计算

[VM 大小](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)Azure Stack 支持的是那些在 Azure 上受支持的子集。 Azure 在多方面施加资源限制，以避免资源（服务器本地和服务级别）的过度消耗。 如果未对租户使用资源施加一些限制，则当一些租户过度使用资源时，另一些租户的体验就会变差。 VM 的网络出口在 Azure Stack 上有与 Azure 限制一致的带宽上限。 对于存储资源，存储 IOPS 限制已经实现了在 Azure Stack，以避免资源过度的存储访问的租户。

>[!IMPORTANT]
>[Azure Stack 容量规划器](https://aka.ms/azstackcapacityplanner)不会考虑或保证 IOPS 性能。

### <a name="vm-placement"></a>VM 放置

在 Azure Stack 中，租户 VM 放置是通过自动放置引擎个可用主机。 放置 Vm 时的只有两个注意事项是该 VM 类型的主机上是否有足够的内存和虚拟机属于[可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)或是[虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)。  

若要实现高可用性的 Azure Stack 中的多 VM 生产系统，需要将 Vm 分散到多个容错域的可用性集放置。 在可用性集中的容错域定义为缩放单位中的单个节点。 为了与 Azure 保持一致，Azure Stack 支持的可用性集最多有三个容错域。 放置在可用性集中的虚拟机将通过跨多个容错域，即，Azure Stack 主机尽可能均匀地分布它们物理上彼此隔离。 出现硬件故障时，发生故障的容错域中的 VM 会在其他容错域中重启，但在将其置于容错域中时，会尽可能让其与同一可用性集中的其他 VM 隔离。 当主机重新联机后时，Vm 将重新平衡，以维持高可用性。  

虚拟机规模集使用可用性集背面结束，并确保每个虚拟机规模集实例位于不同的容错域中。 这意味着它们使用单独的 Azure Stack 基础结构节点。 例如，在 4 节点 Azure Stack 系统，可能有其中的 3 个实例的虚拟机规模集将在 3 个单独的 Azure Stack 节点上放置 3 个虚拟机规模集实例的 4 节点容量所致的创建失败的情况。 此外，Azure Stack 节点可以在不同级别之前尝试放置, 填满中。 

Azure Stack 不超量使用内存。 但是，允许一个过度提交的物理内核数。 由于现有的虚拟与物理内核作为一个因素的过度预配之比不查看放置算法，每个主机可能具有不同的比。 作为 Microsoft，我们不提供指南上的物理到虚拟内核比由于工作负荷和服务级别要求。 

### <a name="azure-stack-memory"></a>Azure Stack 内存 

Azure Stack 方法旨在保持运行已成功预配的 Vm。 例如，如果主机处于脱机状态，由于硬件故障，Azure Stack 将尝试重新启动该 VM 在另一台主机上。 第二个示例是修补和更新 Azure Stack 软件。 如果需要重新启动物理主机，尝试继续执行到解决方案中的另一个可用主机的主机上的 Vm。   

此 VM 管理或移动可仅实现保留的内存容量，以允许重新启动或迁移发生时。 主机内存总量的部分是保留和租户 VM 放置虚拟机不可用。 

你可以查看显示在 Azure Stack 中的可用和已用内存在管理门户中的饼图。 下图显示了 Azure Stack 中 Azure Stack 缩放单位上的物理内存容量：

![物理内存容量](media/azure-stack-capacity-planning/physical-memory-capacity.png)

已用的内存由多个组件组成。 以下组件使用的内存的使用部分中的饼图。  

- 主机操作系统使用情况或保留 – 这在使用操作系统 (OS) 的内存主机、 虚拟内存页表、 主机 OS 和空间直通的内存缓存运行的进程。 
- 基础结构服务 – 这些是构成 Azure Stack Vm 的基础结构。 从 Azure Stack 1902年发布版本，这需要占用 242 GB 的 31 Vm + (4 GB x 节点数)。 这种内部服务结构允许在将来引入新开发的基础结构服务。
- 复原能力保留-Azure Stack 保留了一部分内存允许的租户可用性在单个主机发生故障期间以及修补和更新期间允许成功的实时迁移的 Vm。 
- 租户 Vm – 这些是租户的 Azure Stack 用户创建的 Vm。 除了运行虚拟机，使用的构造已经到达了任何虚拟机的内存。 这意味着，中的 Vm **Creating**或**失败**状态或关闭的情况下从来宾内 Vm 将会占用内存。 但是，Vm 已解除分配使用停止解除分配选项不会使用从 Azure Stack 的内存。 

了解在门户上的内存使用情况的最佳方式是使用[Azure Stack 容量规划器](https://aka.ms/azstackcapacityplanner)了解各种工作负荷的影响。 以下计算是由规划器使用的相同。

此计算会导致可用于租户 VM 放置的总数、 可用内存。 此内存容量是在整个 Azure Stack 缩放单位。 


  VM 放置虚拟机的可用内存 = 主机内存总量的复原能力保留的内存使用的运行租户的 Vm-Azure Stack 基础结构开销<sup>1</sup>

  复原保留 = H + R * ((N-1) * H) + V * (N-2)

> 其中：
> - H = 单服务器内存的大小
> - N = 缩放单元的大小（服务器数）
> - R = 的操作系统保留 OS 开销，这是在此公式.15<sup>2</sup>
> - V = 缩放单元中的最大 VM

  <sup>1</sup> azure Stack 基础结构开销 = 242 GB + (4 GB x 节点数)。 大约 31 Vm 用于托管 Azure Stack 的基础结构，并且总共使用大约 242 GB + (4 GB x 的节点) 的内存和 146 虚拟核心数。 使用这个数目的 VM 是为了进行必要的服务隔离，使之符合安全性、可伸缩性、服务和修补方面的要求。 这种内部服务结构允许在将来引入新开发的基础结构服务。 

  <sup>2</sup>操作系统保留的开销 = 15%(。 15) 的节点内存。 操作系统保留值是一个估计值，具体取决于服务器的物理内存容量和常规的操作系统开销。


值 V（缩放单元中的最大 VM）是动态变化的，具体取决于最大的租户 VM 内存大小。 例如，最大 VM 值可能是 7 GB 或 112 GB，或者是 Azure Stack 解决方案中任何其他受支持的 VM 内存大小。 更改 Azure Stack fabric 上的最大 VM 将导致保留除 VM 本身的内存中增加的复原能力的增加。 

> [!NOTE]
> 网络方面的容量规划要求很少，因为能够配置的只有公共 VIP 的大小。 有关如何将多个公共 IP 地址添加到 Azure Stack 的信息，请参阅[添加公共 IP 地址](azure-stack-add-ips.md)。

## <a name="azure-stack-storage"></a>Azure Stack 存储 
以下部分提供 Azure Stack 存储容量规划信息，帮助你针对解决方案的存储需求进行规划。

### <a name="uses-and-organization-of-storage-capacity"></a>存储容量的使用和组织
Azure Stack 的超聚合配置允许共享物理存储设备。 可用存储的三个主要部分包括：基础结构存储、租户虚拟机的临时存储，以及为 Azure 一致存储 (ACS) 服务的 Blob、表和队列提供支持的存储。

### <a name="spaces-direct-cache-and-capacity-tiers"></a>空间直通缓存和容量层
存储容量可以用于操作系统、本地日志记录、转储和其他临时基础结构存储需求。 此本地存储容量独立于受存储空间直通配置管理的存储设备（设备和容量独立）。 其余存储设备置于单个存储容量池中，不管在缩放单元中的服务器数量为多少。 这些设备分为两种类型：缓存设备和容量设备。  缓存设备都只是该-缓存。 空间直通将使用这些设备来写回和读取缓存。 这些缓存设备的容量使用，而不是致力于到带格式的虚拟磁盘的带格式的、"可见"容量。 容量设备用于此目的，并提供"家庭"的位置管理存储空间的数据。

所有存储容量都由 Azure Stack 基础结构进行直接分配和管理。 进行容量扩展时，操作员需选择配置或分配，或者对选择进行处理。 进行这些设计决策需遵循解决方案要求，是在初始安装/部署过程中或容量扩展过程中自动完成的。 有关复原的详细信息、为重新生成保留的容量以及其他详细信息视为设计的一部分。 

操作员可以选择进行全闪存或混合存储配置：

![Azure 存储容量规划](media/azure-stack-capacity-planning/storage.png)

在全闪存配置中，配置可以是双层配置，也可以是单层配置。  如果配置是单层配置，则所有容量设备将是相同类型（例如 NVMe 或 SATA SSD 或 SAS SSD），并且不使用缓存设备。 在双层全闪存配置中，典型配置是 NVMe 作为缓存设备，然后 SATA 或 SAS SSD 作为容量设备。

在混合双层配置中，缓存可在 NVMe、SATA 或 SAS SSD 之间进行选择，而容量则为 HDD。 

存储空间直通和 Azure Stack 存储配置概述如下：
- 一个缩放单元一个存储空间池（所有存储设备在单个池中配置）
- 虚拟磁盘创建为三副本镜像，以确保最佳性能和复原能力
- 每个虚拟磁盘格式化为一个 ReFS 文件系统
- 虚拟磁盘容量计算，并在一种方式将保留一个容量设备的数量的未分配的池中的数据容量中分配。 这相当于一个服务器一个容量驱动器。
- 每个 ReFS 文件系统都会启用 BitLocker 进行静态数据加密。 

虚拟磁盘自动创建，其容量如下所示：

|名称|容量计算|描述|
|-----|-----|-----|
|本地/启动设备|至少 340 GB<sup>1</sup>|用于操作系统映像和“本地”基础结构 VM 的单个服务器存储|
|基础结构|3.5 TB|所有 Azure Stack 基础结构使用量|
|VmTemp|见下<sup>2</sup>|租户虚拟机附加了一个临时磁盘，该数据存储在这些虚拟磁盘中|
|ACS|见下<sup>3</sup>|Azure 一致存储容量，为 Blob、表和队列提供服务|

<sup>1</sup> 对 Azure Stack 解决方案合作伙伴有最低存储容量要求。

<sup>2</sup> 用于租户虚拟机临时磁盘的虚拟磁盘大小按服务器中物理内存的比率进行计算。 如下面的表（针对 Azure IaaS VM 大小）中所述，临时磁盘在分配给虚拟机的物理内存中占一定的比率。 完成 Azure Stack 中的"临时磁盘"存储的分配将在捕获大多数使用情况下完成，但可能无法满足所有临时磁盘存储需求。 选择比率时，既要让临时存储可用，又要确保不将解决方案的大多数存储容量仅用于临时磁盘容量。 在缩放单元中，将为一个服务器创建一个临时存储磁盘。 在缩放单元的存储池中，临时存储的容量不会超出总体可用存储容量的 10%。 计算如以下示例所示：

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> 创建的供 ACS 使用的虚拟磁盘对剩余容量进行了简单的划分。 如前所述，所有虚拟磁盘是三向镜像和未分配的容量来用于每个服务器的一个容量驱动器的价值。 上面枚举的各种虚拟磁盘会先进行分配，剩余容量随后用于 ACS 虚拟磁盘。

## <a name="azure-stack-capacity-planner"></a>Azure Stack Capacity Planner
Azure Stack 容量规划器是一个电子表格，显示了如何将不同的计算资源分配适合跨多个选中的硬件产品/服务。 

### <a name="worksheet-descriptions"></a>工作表说明
下表包含每个工作表的说明在 Azure Stack 容量规划器，可以从下载[ https://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner)。 

|标签名称|描述|
|-----|-----|
|Version-Disclaimer|计算器、 版本号和发行日期的目的。|
|说明|为模型的容量规划的虚拟机集合的分步说明。|
|DefinedSolutionSKUs|具有最多五个硬件定义表。 项是示例。 更改以匹配正在考虑的系统配置的详细信息。|
|DefineByVMFootprint|通过比较使用不同的大小和数量的 Vm 配置找到合适的硬件 SKU。|
|DefineByWorkloadFootprint|通过创建 Azure Stack 工作负荷的集合来找到相应的硬件 SKU。|
|  |  |

### <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs 说明
此工作表最多包含五个硬件定义示例。 更改以匹配正在考虑的系统配置的详细信息。

#### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>由授权硬件合作伙伴提供的硬件选择
Azure Stack 作为由解决方案合作伙伴安装了软件的集成系统提供。 解决方案合作伙伴提供他们自己的 Azure Stack 容量计划工具的权威版本。 使用这些工具执行最终的解决方案容量的讨论。

#### <a name="multiple-ways-to-model-computing-resources"></a>通过多种方式来模型计算资源
资源在 Azure Stack planner 建模取决于各种大小的 Azure Stack Vm。 Vm 的大小从最小的基本 0 到最大 Standard_Fsv2 的范围。 您可以通过两种方式建模计算资源分配：

- 选择特定的硬件产品/服务并查看各种资源的组合适合。 

- 创建的 VM 分配的特定组合，让 Azure 资源计算器显示的可用硬件 Sku 能够支持该 VM 配置。

此工具提供了用于分配 VM 资源的两个方法： 为 VM 的资源分配的一个单一集合，或最多六个不同的工作负荷配置的集合。 每个工作负荷配置可以包含可用 VM 资源的一个不同分配。 下一步部分介绍了创建和使用每个这些分配模型的分步说明。 只应修改此工作表上背景未着色的单元格中包含的值或 SKU 下拉列表中的值。 在着色的单元格内所做的更改可能会破坏资源计算。


### <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint 说明
若要创建使用各种大小和数量的 Vm 的单个集合的模型，选择"DefineByVMFootprint"选项卡，并按照以下步骤：

1. 在此工作表的右上角，使用提供的下拉列表框控件选择想要安装在每个硬件系统 (SKU) 中的服务器的初始数量（在 4 到 16 之间）。 在建模过程可以随时修改此服务器数量，以便查看此值如何影响资源分配模型的整体可用资源。
2. 如果要针对一个特定的硬件配置为各种 VM 资源分配建模，请在页面的右上角找到“当前 SKU”标签正下方的蓝色下拉列表框。 下拉此列表框，然后选择所需的硬件 SKU。
3. 现在就可以开始将各种大小的 VM 添加到模型了。 若要包含特定 VM 类型，请将数量值输入到该 VM 条目左侧的蓝色空心框中。

   > [!NOTE]
   > VM 存储总量是指 VM 的数据磁盘的总容量（受支持的磁盘的数目 * 单个磁盘的最大容量 (1 TB)）。 我们已根据配置指示器填充了“可用存储配置”表，以便你可以为每个 Azure Stack VM 选择所需级别的存储资源。 但是，请务必注意，你可以根据需要添加或更改“可用存储配置”表。<br><br>每个 VM 从最初分配的本地临时存储开始。 为反映临时存储的精简预配，local-temp 数量可以更改为包括最大可允许临时存储量的下拉菜单中的任何内容。

4. 添加 VM 时，你将看到显示可用 SKU 资源更改的图表。 这样，就可以查看在建模过程中添加各种大小和数量的 VM 的效果。 查看更改效果的另一种方法是观察“可用 VM”列表正下方列出的“已使用”和“仍可用”数目。 这些数字反映了基于当前所选硬件 SKU 估计的值。
5. 创建 VM 集后，可以通过单击页面右上角的“建议的 SKU”按钮（位于“当前 SKU”标签正下方）来查找建议的硬件 SKU。 使用此按钮，可以随后修改 VM 配置并查看哪一硬件支持每个配置。


### <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint 说明
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
