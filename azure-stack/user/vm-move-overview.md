---
title: 将 VM 移动到 Azure Stack Hub
description: 了解可以用于将 VM 移动到 Azure Stack Hub 的不同方式。
author: mattbriggs
ms.topic: conceptual
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: cfcb5b494374fedaa5bdced131a8a42203aa5eb0
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99246565"
---
# <a name="move-a-vm-to-azure-stack-hub-overview"></a>将 VM 移动到 Azure Stack Hub 概述

可以将虚拟机 (VM) 从你的环境移动到 Azure Stack Hub。 在规划移动工作负载时，需要预期一些限制。 本文列出了 Azure Stack Hub 中对虚拟硬盘 (VHD) 的要求。 Azure Stack Hub 需要第一 (1) 代 VHD。 VM 需要进行通用化或专用化。 使用通用化 VM 作为在 Azure Stack 中创建的 VM 的基础。 专用 VM 包含用户帐户。 若要迁移、准备和下载 VHD，请验证 VHD 是否满足要求，将映像上传到 Azure Stack Hub 中的存储帐户，然后在云中创建 VM。 如果有更复杂的迁移任务，可以在“迁移到 Azure Stack Hub”白皮书中找到完整讨论。

自定义映像有两种形式：通用和专用 。

- **通用映像**  
  通用磁盘映像是使用 Sysprep 准备的用于删除任何唯一信息（如用户帐户）的映像，从而使它能够重复用于创建多个 VM。 在创建 Azure Stack Hub 云操作员规划用作市场项的映像时，通用 VHD 非常合适。 通过管理员门户或管理员终结点提供的映像是平台映像。

- **专用映像**  
  专用磁盘映像是包含原始 VM 中的用户帐户、应用程序和其他状态数据的现有 VM 中虚拟硬盘 (VHD) 的副本。 将 VM 迁移到 Azure Stack Hub 时通常采用此格式。 需要将 VM 从本地迁移到 Azure Stack Hub 时，专用 VHD 非常适合此操作。

将映像移动到 Azure Stack Hub 时，请考虑要如何使用映像。

- 个人工作负载  
    你的本地环境或全局 Azure 中可能有一台用于开发或特定任务的计算机，并且你要充分利用通过 Azure Stack Hub 在私有云中托管它。 你要保留该计算机上的数据和用户帐户。 要将此计算机作为专用映像移动到 Azure Stack Hub。

- 黄金映像  
    你可能要在工作组中共享公用 VM 配置和应用程序集。 你不需要与 Azure Stack Hub 域（目录租户）之外的用户共享映像。 在这种情况下，需要通过删除数据和用户帐户来通用化映像。 随后可以与租户中的其他用户共享此映像。

- Azure Stack Hub 市场产品/服务  
    云操作员可以使用通用化映像作为 市场产品/服务的基础。 准备好映像后，云操作员便可使用自定义映像为 Azure Stack Hub 实例创建市场产品/服务。 用户可以从映像创建自己的 VM，如同创建市场中的任何其他产品/服务。 需要与云操作员合作创建此产品/服务。

## <a name="verify-vhd-requirements"></a>验证 VHD 要求

> [!IMPORTANT]  
> 准备 VHD 时，必须准备好实施以下要求，否则无法在 Azure Stack Hub 中使用 VHD。
> 在上传映像之前，请考虑以下因素：
> - Azure Stack Hub 仅支持来自第一 (1) 代 VM 的映像。
> - VHD 属于固定类型。 Azure Stack Hub 不支持动态磁盘 VHD。
> - VHD 的最小虚拟大小至少为 20 MB。
> - VHD 已调整，也就是说，虚拟大小必须是 1 MB 的倍数。
> - VHD blob 长度 = 虚拟大小 + vhd 页脚长度 (512)。 在 Blob 末尾有一小段脚注，描述了 VHD 的属性。 

可以在[验证 VHD](vm-move-from-azure.md#verify-your-vhd) 中找到修复 VHD 的步骤

## <a name="methods-of-moving-a-vm"></a>移动 VM 的方法

对于以下方案，可以手动将 VM 移动到 Azure Stack Hub 中：

| 方案 | Instructions |
| --- | --- |
| 全球 Azure 到 Azure Stack Hub | 在全球 Azure 中准备 VHD，然后上传到 Azure Stack Hub。 有关详细信息，请参阅[将 VM 从 Azure 移动到 Azure Stack Hub](vm-move-from-azure.md)。 |
| 本地通用化到 Azure Stack Hub | 在 Hyper-V 中以本地方式准备 VHD 并通用化 VHD，然后上传到 Azure Stack Hub。 有关详细信息，请参阅[将通用化 VM 从本地移动到 Azure Stack Hub](vm-move-generalized.md)。 |
| 本地专用化到 Azure Stack Hub | 在 Hyper-V 中以本地方式准备专用化 VHD，然后上传到 Azure Stack Hub。 有关详细信息，请参阅[将专用化 VM 从本地移动到 Azure Stack Hub](vm-move-specialized.md)。 |

## <a name="migrate-to-azure-stack-hub"></a>迁移到 Azure Stack Hub

可以在 Azure 全球中由 AzureCAT 专家撰写的指南中找到用于批量规划和将工作负载迁移到 Azure Stack Hub 的详细信息、检查表和最佳做法。 该指南重点介绍迁移在物理服务器或现有虚拟化平台上运行的现有应用程序。 通过将这些工作负载转移到 Azure Stack Hub IaaS 环境，团队可受益于更顺利的运营、自助部署、标准化硬件配置和 Azure 一致性。

[获取白皮书。](https://azure.microsoft.com/resources/migrate-to-azure-stack-hub-patterns-and-practices-checklists/)

还可以找到有关云采用框架中的迁移的指导。 有关详细信息，请参阅[规划 Azure Stack Hub 迁移](/azure/cloud-adoption-framework/scenarios/azure-stack/plan)。 

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub VM 简介](azure-stack-compute-overview.md)

[将自定义 VM 映像添加到 Azure Stack Hub](../operator/azure-stack-add-vm-image.md)