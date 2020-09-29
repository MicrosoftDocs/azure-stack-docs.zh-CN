---
title: 将 VM 移到 Azure Stack 中心
description: 了解将 VM 移动到 Azure Stack 集线器的不同方式。
author: mattbriggs
ms.topic: conceptual
ms.date: 9/8/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 932fed2c6097fbbf41606f365b0c5a424bf1381b
ms.sourcegitcommit: 9a340b383dcf42c85bc6ec0d01ff3c9ae29dfe4c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89609740"
---
# <a name="move-a-vm-to-azure-stack-hub-overview"></a>将 VM 移到 Azure Stack 集线器概述

你可以将虚拟机从环境 (VM) 移到 Azure Stack 中心。 在规划移动工作负荷时，需要有一些限制。 本文列出了 Azure Stack 集线器中虚拟硬盘 (VHD) s 的要求。 Azure Stack 集线器需要第一代 (1) VHD。 你的 VM 需要通用化或专用化。 使用通用化 Vm 作为 Azure Stack 中创建的 VM 的基础。 专用 VM 包含用户帐户。 若要迁移、准备和下载 VHD，请验证 VHD 满足要求，将映像上传到 Azure Stack 集线器中的存储帐户，然后在云中创建 VM。 如果有更复杂的迁移任务，可以在 *迁移到 Azure Stack 中心* 白皮书中找到完整的讨论。

自定义映像有两种形式：通用和专用 。

- **通用映像**  
  通用磁盘映像是使用 Sysprep 准备的用于删除任何唯一信息（如用户帐户）的映像，从而使它能够重复用于创建多个 VM。 通用 Vhd 适用于创建 Azure Stack 中心云操作员计划用作 marketplace 项的映像的时间。 通过管理员门户或管理员终结点提供的映像是 **平台映像**。

- **专用映像**  
  专用磁盘映像是包含原始 VM 中的用户帐户、应用程序和其他状态数据的现有 VM 中虚拟硬盘 (VHD) 的副本。 将 VM 迁移到 Azure Stack Hub 时通常采用此格式。 当你需要将 Vm 从本地迁移到 Azure Stack 中心时，专用 Vhd 非常合适。

将图像移到 Azure Stack 集线器中时，请考虑要如何使用映像。

- **个人工作负荷**  
    你的本地环境或全局 Azure 中可能有一台用于开发或特定任务的计算机，并且你想要充分利用 Azure Stack 集线器在私有云中托管它。 你希望保留计算机上的数据和用户帐户。 需要将此计算机作为专用映像移动到 Azure Stack 中心。

- **黄金图**  
    你可能想要在工作组中共享公用 VM 配置和应用程序集。 你将不需要与 Azure Stack 中心域外的用户共享映像 (directory 租户) 。 在这种情况下，需要通过删除数据和用户帐户来通用化映像。 然后，你可以与租户中的其他用户共享此映像。

- **Azure Stack 中心市场产品**  
    云操作员可以使用通用化映像作为 marketplace 产品的基础。 准备好映像后，云操作员可使用自定义映像为 Azure Stack 中心实例创建 marketplace 产品/服务。 用户可以从映像创建自己的 VM，就像应用商店中的任何其他产品。 需要使用云操作员来创建此产品/服务。

## <a name="verify-vhd-requirements"></a>验证 VHD 要求

> [!IMPORTANT]  
> 准备 VHD 时，必须准备好以下要求，否则将无法在 Azure Stack 集线器中使用 VHD。
> 在上传映像之前，请考虑以下事项：
> - Azure Stack 中心仅支持第1代 () Vm 中的映像。
> - VHD 的类型为固定。 Azure Stack Hub 不支持动态磁盘 VHD。
> - VHD 的最小虚拟大小至少为 20 MB。
> - VHD 是对齐的，也就是说，虚拟大小必须是 1 MB 的倍数。
> - VHD blob 长度 = 虚拟大小 + vhd 脚注长度 (512) 。 在 Blob 末尾有一小段脚注，描述了 VHD 的属性。 

可以在[验证 vhd](vm-move-from-azure.md#verify-your-vhd)时找到修复 vhd 的步骤

## <a name="methods-of-moving-a-vm"></a>移动 VM 的方法

可以在以下情况下手动将 VM 移到 Azure Stack 集线器中：

| 方案 | 说明 |
| --- | --- |
| 全球性 Azure 到 Azure Stack 中心 | 在全球 Azure 中准备 VHD，并将其上传到 Azure Stack 中心。 有关详细信息，请参阅 [将 VM 从 Azure 移到 Azure Stack 中心](vm-move-from-azure.md)。 |
| 本地通用化到 Azure Stack 中心 | 准备 VHD 并在 Hyper-v 中本地通用化 VHD，并将其上传到 Azure Stack 集线器。 有关详细信息，请参阅 [将通用化 VM 从本地移到 Azure Stack 中心](vm-move-generalized.md)。 |
| 本地专用于 Azure Stack 中心 | 在 Hyper-v 中本地准备专用 VHD，并将其上传到 Azure Stack 集线器。 有关详细信息，请参阅 [将专用 VM 从本地移到 Azure Stack 中心](vm-move-specialized.md)。 |

## <a name="migrate-to-azure-stack-hub"></a>迁移到 Azure Stack 中心

你可以在 Azure Global AzureCAT 专家撰写的指南中找到详细信息、检查表和最佳实践，以批量规划和将工作负荷迁移到 Azure Stack 中心。 本指南重点介绍在物理服务器上或在现有虚拟化平台上运行的现有应用程序的迁移。 通过将这些工作负载转移到 Azure Stack Hub IaaS 环境，团队可受益于更顺利的运营、自助部署、标准化硬件配置和 Azure 一致性。

[获取白皮书。](https://azure.microsoft.com/resources/migrate-to-azure-stack-hub-patterns-and-practices-checklists/)

你还可以在云采用框架中找到有关迁移的指南。 有关详细信息，请参阅 [计划 Azure Stack 中心迁移](/azure/cloud-adoption-framework/scenarios/azure-stack/plan)。 

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub VM 简介](azure-stack-compute-overview.md)

[将自定义 VM 映像添加到 Azure Stack Hub](../operator/azure-stack-add-vm-image.md)