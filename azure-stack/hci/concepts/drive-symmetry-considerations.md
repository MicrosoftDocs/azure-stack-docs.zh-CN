---
title: Azure Stack HCI 的驱动器对称注意事项
description: 本主题介绍了驱动器对称约束，并提供了支持的和不支持的配置的示例。
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/29/2020
ms.openlocfilehash: 6cf983d6cf64b0b41bb9710bdf720dd1777c9ad6
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064797"
---
# <a name="drive-symmetry-considerations-for-azure-stack-hci"></a>Azure Stack HCI 的驱动器对称注意事项

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

如果每台服务器都有完全相同的驱动器，则 Azure Stack HCI 的工作性能最佳。

事实上，我们认为这并不总是可行的，因为 Azure Stack HCI 设计为可以运行多年，可以随组织需求的增加而扩展。 现在，你可以购买大容量的 3 TB 硬盘驱动器；下一年，将不可能找到这么小的驱动器。 因此，预计会存在一定数量的混合搭配，而且我们也支持它。 但请记住，对称性越强越好。

本主题介绍了约束，并提供了支持的和不支持的配置的示例。

## <a name="constraints"></a>约束

本部分介绍有关驱动器类型、型号、大小和驱动器数的约束。

### <a name="type"></a>类型

所有服务器都应该有相同的[驱动器类型](choose-drives.md#drive-types)。

例如，如果一台服务器的驱动器类型为 NVMe，则所有服务器的驱动器类型都应该是 NVMe。

### <a name="number"></a>Number

所有服务器的每种类型的驱动器的数量应当相同。

例如，如果一台服务器有六个 SSD，则所有服务器都应当有六个 SSD。

   > [!NOTE]
   > 在出现故障时或者在添加或删除驱动器时，驱动器数量可以暂时不同。

### <a name="model"></a>“模型”

建议尽可能使用相同型号和固件版本的驱动器。 如果无法做到这一点，请仔细选择尽可能相似的驱动器。 我们不建议混搭同一类型的具有截然不同的性能或耐用性特征的驱动器（除非一个是缓存，另一个是容量），因为 Azure Stack HCI 会均匀分布 IO，而不是基于型号进行区分。

   > [!NOTE]
   > 可以混搭类似的 SATA 和 SAS 驱动器。

### <a name="size"></a>大小

建议尽可能使用大小相同的驱动器。 使用不同大小的容量驱动器可能会导致某些容量不可用，使用不同大小的缓存驱动器可能不会提高缓存性能。 有关详细信息，请参阅下一节。

   > [!WARNING]
   > 不同服务器上的容量驱动器大小不同可能会导致容量闲置。

## <a name="understand-capacity-imbalance"></a>了解：容量不平衡

Azure Stack HCI 可以很好地应对驱动器之间和服务器之间的容量不平衡问题。 即使不平衡情况很严重，所有功能仍将继续工作。 但是，如果不是每台服务器中都提供了某个容量，则该容量可能无法使用，这取决于多个因素。

若要查看发生此问题的原因，请考虑下面的简化图。 每个彩色框表示一个镜像数据副本。 例如，标有 A、A' 和 A'' 的框是相同数据的三个副本。 为了实现服务器容错，这些副本必须存储在不同的服务器中。

### <a name="stranded-capacity"></a>闲置容量

如图所示，服务器 1 (10 TB) 和服务器 2 (10 TB) 已满。 服务器 3 有更大的驱动器，因此它的总容量更大 (15 TB)。 但是，若要在服务器 3 上存储更多的三向镜像数据，则已满的服务器 1 和服务器 2 上也需要存在副本。 服务器 3 上剩余的 5 TB 容量无法使用 – 它是“闲置”容量。

![三向镜像，三台服务器，闲置容量](media/drive-symmetry-considerations/Size-Asymmetry-3N-Stranded.png)

### <a name="optimal-placement"></a>最佳放置

反过来，如果四台服务器的容量分别为 10 TB、10 TB、10 TB 和 15 TB 并且具有三向镜像复原能力，则可以有效地放置副本以使用所有可用容量，如图所示。 只要有此可能性，存储空间直通分配器就会查找并使用最佳放置，不会留下闲置容量。

![三向镜像，四台服务器，无闲置容量](media/drive-symmetry-considerations/Size-Asymmetry-4N-No-Stranded.png)

服务器数量、复原能力、容量不平衡的严重程度以及其他因素会影响到容量是否会闲置。 **为谨慎起见，通常会假设只有每台服务器中都提供的容量能保证可以使用。**

## <a name="understand-cache-imbalance"></a>了解：缓存不平衡

Azure Stack HCI 可以很好地应对驱动器之间和服务器之间的缓存不平衡问题。 即使不平衡情况很严重，所有功能仍将继续工作。 而且，Azure Stack HCI 始终使用所有可用缓存，直至达到最大容量。

但是，使用不同大小的缓存驱动器可能不会一致地改善或如预期那样改善缓存性能：只有缓存驱动器较大的[驱动器绑定](cache.md#server-side-architecture)的 IO 能够看到性能改进。 Azure Stack HCI 在各个绑定之间平均分配 IO，不会根据缓存与容量之比进行区分。

![缓存不平衡](media/drive-symmetry-considerations/Cache-Asymmetry.png)

   > [!TIP]
   > 请参阅[了解缓存](cache.md)，详细了解缓存绑定。

## <a name="example-configurations"></a>示例配置

下面是一些支持的和不支持的配置：

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-models-between-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: 支持：服务器之间的型号不同

前两台服务器使用 NVMe 型号“X”，但第三台服务器使用非常类似的 NVMe 型号“Z”。

| 服务器 1                    | 服务器 2                    | 服务器 3                    |
|-----------------------------|-----------------------------|-----------------------------|
| 2 x NVMe 型号 X（缓存）    | 2 x NVMe 型号 X（缓存）    | 2 x NVMe 型号 Z（缓存）    |
| 10 x SSD 型号 Y（容量） | 10 x SSD 型号 Y（容量） | 10 x SSD 型号 Y（容量） |

这是受支持的。

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-models-within-server"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: 支持：服务器内的型号不同

每台服务器混合使用一些不同但非常类似的 HDD 型号“Y”和“Z”。 每台服务器总共有 10 个 HDD。

| 服务器 1                   | 服务器 2                   | 服务器 3                   |
|----------------------------|----------------------------|----------------------------|
| 2 x SSD 型号 X（缓存）    | 2 x SSD 型号 X（缓存）    | 2 x SSD 型号 X（缓存）    |
| 7 x HDD 型号 Y（容量） | 5 x HDD 型号 Y（容量） | 1 x HDD 型号 Y（容量） |
| 3 x HDD 型号 Z（容量） | 5 x HDD 型号 Z（容量） | 9 x HDD 型号 Z（容量） |

这是受支持的。

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-sizes-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: 支持：服务器之间的大小不同

前两台服务器使用 4 TB HDD，但第三台服务器使用非常相似的 6 TB HDD。

| 服务器 1                | 服务器 2                | 服务器 3                |
|-------------------------|-------------------------|-------------------------|
| 2 x 800 GB NVMe（缓存） | 2 x 800 GB NVMe（缓存） | 2 x 800 GB NVMe（缓存） |
| 4 x 4 TB HDD（容量） | 4 x 4 TB HDD（容量） | 4 x 6 TB HDD（容量） |

这是受支持的，但会导致容量闲置。

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-sizes-within-server"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: 支持：服务器内的大小不同

每台服务器混搭 1.2 TB SSD 和非常类似的 1.6 TB SSD。 每台服务器总共有 4 个 SSD。

| 服务器 1                 | 服务器 2                 | 服务器 3                 |
|--------------------------|--------------------------|--------------------------|
| 3 x 1.2 TB SSD（缓存）   | 2 x 1.2 TB SSD（缓存）   | 4 x 1.2 TB SSD（缓存）   |
| 1 x 1.6 TB SSD（缓存）   | 2 x 1.6 TB SSD（缓存）   | -                        |
| 20 x 4 TB HDD（容量） | 20 x 4 TB HDD（容量） | 20 x 4 TB HDD（容量） |

这是受支持的。

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-different-types-of-drives-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: 不支持：服务器之间的驱动器类型不同

服务器 1 有 NVMe，但其他服务器没有。

| 服务器 1            | 服务器 2            | 服务器 3            |
|---------------------|---------------------|---------------------|
| 6 x NVMe（缓存）    | -                   | -                   |
| -                   | 6 x SSD（缓存）     | 6 x SSD（缓存）     |
| 18 x HDD（容量） | 18 x HDD（容量） | 18 x HDD（容量） |

不支持此操作。 每台服务器中的驱动器类型应当相同。

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-different-number-of-each-type-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: 不支持：服务器之间每种类型的数量不同

服务器 3 有比其他服务器更多的驱动器。

| 服务器 1            | 服务器 2            | 服务器 3            |
|---------------------|---------------------|---------------------|
| 2 x NVMe（缓存）    | 2 x NVMe（缓存）    | 4 x NVMe（缓存）    |
| 10 x HDD（容量） | 10 x HDD（容量） | 20 x HDD（容量） |

不支持此操作。 每台服务器中每种类型的驱动器数目应相同。

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-only-hdd-drives"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: 不支持：仅使用 HDD 驱动器

所有服务器仅连接了 HDD 驱动器。

|服务器 1|服务器 2|服务器 3|
|-|-|-|
|18 x HDD（容量） |18 x HDD（容量）|18 x HDD（容量）|

不支持此操作。 需要添加至少两个附加到每台服务器的缓存驱动器（NvME 或 SSD）。

## <a name="summary"></a>摘要

概括而言，群集中每个服务器的驱动器都应具有相同的类型，各类型驱动器的数量也应相同。 支持根据需要混搭驱动器型号和驱动器大小，但需注意上述注意事项。

| 约束 | 状态 |
|--|--|
| 每台服务器中的驱动器类型相同 | **必需** |
| 每台服务器中每种类型的数目相同 | **必需** |
| 每台服务器中的驱动器型号相同 | 建议 |
| 每台服务器中的驱动器大小相同 | 建议 |

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [系统要求](system-requirements.md)
- [选择驱动器](choose-drives.md)
