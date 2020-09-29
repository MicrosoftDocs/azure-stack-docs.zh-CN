---
title: 启用卷加密、重复数据删除和压缩-Azure Stack HCI
description: 本主题介绍如何使用 Windows 管理中心 Azure Stack HCI 中使用卷加密、重复数据删除和压缩。
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 09/03/2020
ms.openlocfilehash: 9599a4d24d93e545c964de91ad10b905c79b42a1
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573490"
---
# <a name="enable-volume-encryption-deduplication-and-compression-in-azure-stack-hci"></a>在 Azure Stack HCI 中启用卷加密、删除重复和压缩

> 适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

本主题介绍如何使用 Windows 管理中心在 Azure Stack HCI 中的卷上启用 BitLocker 加密。 还介绍了如何在卷上启用重复数据删除和压缩。 若要了解如何创建卷，请参阅 [创建卷](create-volumes.md)。

## <a name="turn-on-bitlocker-to-protect-volumes"></a>启用 BitLocker 来保护卷
若要在 Windows 管理中心中启用 BitLocker：

1. 连接到存储空间直通群集，然后在“工具”窗格上，选择“卷” 。
1. 在 " **卷** " 页上，选择 " **清单** " 选项卡，然后在 " **可选功能**" 下切换 **加密 (BitLocker) ** 切换。

    :::image type="content" source="media/volume-encryption-deduplication/bitlocker-toggle-switch.png" alt-text="用于启用 BitLocker 的切换开关":::

1. 在“加密 (BitLocker)”弹出项中，选择“开始”，然后在“启用加密”页上，提供凭据以完成工作流  。

>[!NOTE]
   > 如果显示“先安装 BitLocker 功能”弹出项，请按照其说明在群集中的每个服务器上安装该功能，然后重新启动服务器。

## <a name="turn-on-deduplication-and-compression"></a>启用重复数据删除和压缩
重复数据删除和压缩按卷进行管理。 重复数据删除和压缩使用后处理模型，这意味着在运行之前不会出现节省费用。 在此过程中，它将处理所有文件，甚至是之前的文件。

若要在 Windows 管理中心的卷上启用重复数据删除和压缩：

1. 连接到存储空间直通群集，然后在“工具”窗格上，选择“卷” 。
1. 在“卷”页上选择“库存”选项卡。 
1. 在卷列表中，选择要管理的卷的名称。
1. 在 "卷详细信息" 页上，切换到 " **重复数据删除和压缩** " 切换。
1. 在 " **启用重复数据删除** " 窗格上，选择 "重复数据删除" 模式。

    Windows Admin Center 可让你针对不同的工作负荷选择现成的配置文件，而无需进行复杂的设置。 如果你不确定要如何设置，请使用默认设置。

1. 选择 " **启用重复数据删除**"。

观看有关如何打开重复数据删除和压缩的快速视频。 视频不显示加密。

> [!VIDEO https://www.youtube-nocookie.com/embed/PRibTacyKko]

启用卷加密对卷性能的影响较小，通常低于10%，但影响取决于硬件和工作负荷。 重复数据删除和压缩还会对性能产生影响-有关详细信息，请参阅 [确定哪些工作负荷是重复数据删除的候选](/windows-server/storage/data-deduplication/install-enable#enable-dedup-candidate-workloads)项。

<!---Add info on greyed out ReFS option? --->

大功告成！ 根据需要重复此步骤以保护卷中的数据。

## <a name="next-steps"></a>后续步骤
有关相关主题和其他存储管理任务，另请参阅：

- [存储空间直通概述](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [规划卷](../concepts/plan-volumes.md)
- [扩展卷](extend-volumes.md)
- [删除卷](delete-volumes.md)