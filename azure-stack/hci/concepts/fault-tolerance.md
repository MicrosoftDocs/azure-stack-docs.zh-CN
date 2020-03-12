---
title: Azure Stack HCI 中的容错和存储效率
description: 有关复原选项的讨论存储空间直通包括镜像和奇偶校验。
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: 9ace3960b4c54461a4153c4997694e6d17ee4fd1
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2020
ms.locfileid: "79025696"
---
# <a name="fault-tolerance-and-storage-efficiency-in-azure-stack-hci"></a>Azure Stack HCI 中的容错和存储效率

>适用于： Windows Server 2019

本主题介绍存储空间直通中可用的复原选项，并概述每个规模要求、存储效率以及每个选项的总体优点和利弊。 它还提供了一些使用说明来帮助你入门，并参考了一些精彩的文章、博客和其他内容，你可以在其中了解更多详细信息。

如果已熟悉存储空间，可能需要跳到 "[摘要](#summary)" 部分。

## <a name="overview"></a>概述

就其核心而言，存储空间是指为你的数据提供容错，通常称为 "复原"。 它的实现类似于 RAID，只不过跨服务器分布并在软件中实现。

与 RAID 一样，存储空间有几种不同的方法可实现此目的，这会在容错、存储效率和计算复杂性之间做出不同的权衡。 它们广泛分为两类： "镜像" 和 "奇偶校验"，后者有时称为 "擦除代码"。

## <a name="mirroring"></a>镜像

镜像通过保留所有数据的多个副本来提供容错能力。 这与 RAID-1 最相似。 如何对数据进行条带化和放置是一项非常重要的操作（请参阅[此博客](https://blogs.technet.microsoft.com/filecab/2016/11/21/deep-dive-pool-in-spaces-direct/)了解详细信息），但最好是说，使用镜像存储的任何数据都将被完整地多次写入。 每个副本都写入不同的物理硬件（不同服务器中的不同驱动器），这些硬件被认为独立地失败。

存储空间提供两种形式的镜像： "双向" 和 "三向"。

### <a name="two-way-mirror"></a>“双向镜像”

双向镜像写入两个内容的副本。 其存储效率为50% –写入 1 TB 的数据，需要至少 2 TB 的物理存储容量。 同样，你需要至少两个[硬件 "容错域"](/windows-server/failover-clustering/fault-domains) –具有存储空间直通，这意味着两台服务器。

![双向镜像](media/fault-tolerance/two-way-mirror-180px.png)

   >[!WARNING]
   > 如果有两个以上的服务器，我们建议改用三向镜像。

### <a name="three-way-mirror"></a>“三向镜像”

三向镜像写入所有内容的三个副本。 其存储效率为33.3% –写入 1 TB 的数据，需要至少 3 TB 的物理存储容量。 同样，你需要至少三个硬件容错域–具有存储空间直通，这意味着三台服务器。

三向镜像一次可以安全地容忍至少[两个硬件问题（驱动器或服务器）](#examples)。 例如，如果你在突然另一个驱动器或服务器发生故障时重启一台服务器，则所有数据都将保持安全且持续可访问。

![三向镜像](media/fault-tolerance/three-way-mirror-180px.png)

## <a name="parity"></a>Parity

奇偶校验编码（通常称为 "擦除编码"）通过按位算法提供容错，这可能会使其变[得非常复杂](https://www.microsoft.com/research/wp-content/uploads/2016/02/LRC12-cheng20webpage.pdf)。 与镜像相比，这种方式的效果不是很明显，而且还有很多有用的联机资源（例如，此第三方[进一步的擦除编码指南](http://smahesh.com/blog/2012/07/01/dummies-guide-to-erasure-coding/)），可以帮助您了解这一点。 Sufficed 说，它可以提供更高的存储效率，而不会降低容错能力。

存储空间提供了两种奇偶校验： "单一" 奇偶校验和 "双重" 奇偶校验，后者使用一种称为 "本地重建代码" 的高级技术，其规模更大。

> [!IMPORTANT]
> 对于大多数性能敏感的工作负载，我们建议使用镜像。 若要了解有关如何根据工作负荷平衡性能和容量的详细信息，请参阅[规划卷](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type)。

### <a name="single-parity"></a>单个奇偶校验
单个奇偶校验仅保留一个按位奇偶校验符号，每次只提供一次故障容错。 它最接近于 RAID-5。 若要使用单一奇偶校验，需要至少三个硬件容错域–使用存储空间直通，这意味着三台服务器。 由于三向镜像在相同的规模下提供了更多容错能力，因此，我们反对使用单一奇偶校验。 不过，如果你坚持使用它，则它是完全受支持的。

   >[!WARNING]
   > 我们反对使用单个奇偶校验，因为它一次只能安全地容忍一个硬件故障：如果你在突然另一个驱动器或服务器发生故障时重启一台服务器，你将会遇到停机。 如果只有三台服务器，我们建议使用三向镜像。 如果有四个或更多，请参阅下一节。

### <a name="dual-parity"></a>双重奇偶校验

双重奇偶校验实现了簧片-所罗门群岛纠错代码，以保留两个按位奇偶校验符号，从而提供与三向镜像相同的容错（即一次最多两个故障），但具有更高的存储效率。 它最接近于 RAID-5。 若要使用双重奇偶校验，你至少需要四个硬件容错域–使用存储空间直通，这意味着四个服务器。 在此规模下，存储效率为50% –若要存储 2 TB 的数据，需要 4 TB 的物理存储容量。

![双重奇偶校验](media/fault-tolerance/dual-parity-180px.png)

双重奇偶校验的存储效率增加了你拥有的硬件容错域的数量，从50% 提高到80%。 例如，在7（有存储空间直通的情况下，这意味着七台服务器），效率会转至66.7% –存储 4 TB 的数据，只需 6 TB 的物理存储容量。

![双奇偶校验](media/fault-tolerance/dual-parity-wide-180px.png)

请参阅 "[摘要](#summary)" 部分，了解每个规模的双方和本地重建代码的效率。

### <a name="local-reconstruction-codes"></a>本地重建代码

存储空间引入了由 Microsoft Research 开发的一种高级技术，称为 "本地重建代码" 或 LRC。 大规模，双奇偶校验使用 LRC 将其编码/解码拆分为几个较小的组，以减少进行写入或从失败中恢复所需的开销。

对于硬盘驱动器（HDD），组大小为四个符号;对于固态硬盘（SSD），组大小为六个符号。 例如，以下是布局在硬盘驱动器和12个硬件容错域（即12个服务器）上的外观，其中包含四个数据符号组。 它实现了72.7% 的存储效率。

![本地重建代码](media/fault-tolerance/local-reconstruction-codes-180px.png)

我们建议这一深入[了解本地重建代码如何处理各种故障方案，以及](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/)我们的[Claus Joergensen](https://twitter.com/clausjor)，这是我们的 eminently。

## <a name="mirror-accelerated-parity"></a>镜像加速的奇偶校验

存储空间直通卷可以是部分镜像和部分奇偶校验。 首先将土地写入镜像部分，并在以后逐渐移到奇偶校验部分。 实际上，这是[使用镜像加速擦除编码](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/)。

若要混合使用三向镜像和双奇偶校验，你至少需要四个容错域，即四个服务器。

镜像加速奇偶校验的存储效率介于使用所有镜像或所有奇偶校验的情况之间，取决于你选择的比例。 例如，此演示文稿37分钟的演示显示了各种混合，其中包含12个服务器[，实现46%、54% 和65% 效率](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s)。

> [!IMPORTANT]
> 对于大多数性能敏感的工作负载，我们建议使用镜像。 若要了解有关如何根据工作负荷平衡性能和容量的详细信息，请参阅[规划卷](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type)。

## <a name="summary"></a>摘要

本部分总结了存储空间直通中可用的复原类型、每种类型使用的最小缩放要求、每种类型可以容忍的故障数，以及相应的存储效率。

### <a name="resiliency-types"></a>复原类型

|    复原          |    故障容错       |    存储效率      |
|------------------------|----------------------------|----------------------------|
|    “双向镜像”      |    1                       |    50.0%                   |
|    “三向镜像”    |    2                       |    33.3%                   |
|    双重奇偶校验         |    2                       |    50.0%-80.0%           |
|    Mixed               |    2                       |    33.3%-80.0%           |

### <a name="minimum-scale-requirements"></a>最小缩放要求

|    复原          |    所需的最小容错域   |
|------------------------|-------------------------------------|
|    “双向镜像”      |    2                                |
|    “三向镜像”    |    3                                |
|    双重奇偶校验         |    4                                |
|    Mixed               |    4                                |

   >[!TIP]
   > 除非使用的是[机箱或机架容错](/windows-server/failover-clustering/fault-domains)，否则容错域的数量是指服务器的数量。 只要满足存储空间直通的最低要求，每个服务器中的驱动器数量就不会影响可以使用的复原类型。

### <a name="dual-parity-efficiency-for-hybrid-deployments"></a>混合部署的双重奇偶校验效率

此表显示了混合部署的每个规模的双奇偶校验和本地重建代码的存储效率，同时包含硬盘（HDD）和固态硬盘（SSD）。

|    容错域      |    布局           |    效率   |
|-----------------------|---------------------|-----------------|
|    2                  |    –                |    –            |
|    3                  |    –                |    –            |
|    4                  |    RS 2 + 2           |    50.0%        |
|    5                  |    RS 2 + 2           |    50.0%        |
|    6                  |    RS 2 + 2           |    50.0%        |
|    7                  |    RS 4 + 2           |    66.7%        |
|    8                  |    RS 4 + 2           |    66.7%        |
|    9                  |    RS 4 + 2           |    66.7%        |
|    10                 |    RS 4 + 2           |    66.7%        |
|    11                 |    RS 4 + 2           |    66.7%        |
|    12                 |    LRC （8，2，1）    |    72.7%        |
|    13                 |    LRC （8，2，1）    |    72.7%        |
|    14                 |    LRC （8，2，1）    |    72.7%        |
|    15                 |    LRC （8，2，1）    |    72.7%        |
|    16                 |    LRC （8，2，1）    |    72.7%        |

### <a name="dual-parity-efficiency-for-all-flash-deployments"></a>所有闪存部署的双重奇偶校验效率

此表显示了所有闪存部署（仅包含固态硬盘（SSD））的每个规模的双奇偶校验和本地重建代码的存储效率。 奇偶校验布局可以使用更大的组大小，并在所有闪存配置中实现更好的存储效率。

|    容错域      |    布局           |    效率   |
|-----------------------|---------------------|-----------------|
|    2                  |    –                |    –            |
|    3                  |    –                |    –            |
|    4                  |    RS 2 + 2           |    50.0%        |
|    5                  |    RS 2 + 2           |    50.0%        |
|    6                  |    RS 2 + 2           |    50.0%        |
|    7                  |    RS 4 + 2           |    66.7%        |
|    8                  |    RS 4 + 2           |    66.7%        |
|    9                  |    RS 6 + 2           |    75.0%        |
|    10                 |    RS 6 + 2           |    75.0%        |
|    11                 |    RS 6 + 2           |    75.0%        |
|    12                 |    RS 6 + 2           |    75.0%        |
|    13                 |    RS 6 + 2           |    75.0%        |
|    14                 |    RS 6 + 2           |    75.0%        |
|    15                 |    RS 6 + 2           |    75.0%        |
|    16                 |    LRC （12，2，1）   |    80.0%        |

## <a name="examples"></a>示例

除非只有两台服务器，否则我们建议使用三向镜像和/或双重奇偶校验，因为它们提供了更好的容错能力。 具体而言，它们可确保即使两个容错存储空间直通域（这意味着两台服务器）受到并发故障的影响，所有数据仍保持安全且可连续访问。

### <a name="examples-where-everything-stays-online"></a>所有内容保持联机状态的示例

这六个示例展示了三向镜像和/或双重奇偶校验**可以**容忍的情况。

- **1.** 一个驱动器丢失（包括缓存驱动器）
- **2.** 服务器丢失

![容错-示例-1-2](media/fault-tolerance/Fault-Tolerance-Example-12.png)

- **3.** 一个服务器和一个驱动器丢失
- **4.** 不同服务器中的两个驱动器丢失

![容错-示例-3-4-4](media/fault-tolerance/Fault-Tolerance-Example-34.png)

- **5.** 如果两个以上的服务器受到影响，则会丢失两个以上的驱动器
- **6.** 两台服务器丢失

![容错-示例-5-6 和-6](media/fault-tolerance/Fault-Tolerance-Example-56.png)

...在每种情况下，所有卷都将保持联机状态。 （请确保群集保持仲裁。）

### <a name="examples-where-everything-goes-offline"></a>所有内容脱机的示例

在其生存期内，存储空间可以容忍任意数量的故障，因为它会在给定足够时间的情况下，恢复到每个故障后恢复到完全复原。 但是，在任何给定时刻，最多可以安全地影响两个容错域。 因此，以下示例说明了什么三向镜像和/或双重奇偶校验**不能**容忍。

- **7.** 在三个或更多服务器上一次丢失驱动器
- **8.** 三个或更多服务器一次丢失

![容错-示例-7-8 和-8](media/fault-tolerance/Fault-Tolerance-Example-78.png)

## <a name="usage"></a>用法

查看[在存储空间直通中创建卷](/windows-server/storage/storage-spaces/create-volumes)。

## <a name="next-steps"></a>后续步骤

若要进一步阅读本文中所述的主题，请参阅以下内容：

- [Azure 中由 Microsoft Research 进行的擦除编码](https://www.microsoft.com/research/publication/erasure-coding-in-windows-azure-storage/)
- [本地重建代码和加速奇偶校验卷](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/)
- [存储管理 API 中的卷](https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/)
- [Microsoft Ignite 2016 的存储效率演示](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s)
- [存储空间直通的容量计算器预览版](https://aka.ms/s2dcalc)
