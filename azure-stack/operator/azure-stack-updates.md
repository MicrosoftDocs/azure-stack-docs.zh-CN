---
title: 管理更新
description: 了解如何在 Azure Stack Hub 中管理更新
author: sethmanheim
ms.topic: how-to
ms.date: 02/08/2021
ms.author: sethm
ms.lastreviewed: 07/16/2020
ms.reviewer: niy
ms.openlocfilehash: 163485eddb18f0fdc1400aa61fee6b944c397cbf
ms.sourcegitcommit: 824fd33fd5d6aa0c0dac06c21b592bdb60378940
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99850859"
---
# <a name="manage-updates-in-azure-stack-hub"></a>在 Azure Stack Hub 中管理更新

原始设备制造商 (OEM) 提供的完整和快速更新、修补程序以及驱动程序与固件更新都有助于让 Azure Stack Hub 保持最新状态。 本文介绍不同类型的更新、其预期发布时间，以及在何处可以找到有关当前版本的详细信息。

> [!NOTE]  
> 无法将 Azure Stack Hub 更新包应用于 Azure Stack 开发工具包 (ASDK)。 更新包专为集成系统所设计。 有关信息，请参阅[重新部署 ASDK](../asdk/asdk-redeploy.md)。

## <a name="update-package-types"></a>更新包类型

集成系统有三种类型的更新包：

- **Azure Stack Hub 软件更新**。 Microsoft 会负责 Microsoft 软件更新包的端到端服务生命周期。 这些包可以包括最新的 Windows Server 安全更新、非安全更新和 Azure Stack Hub 功能更新。 可以直接从 Microsoft 下载这些更新包。

    每个更新包都有对应的类型：“完整”或“快速”。 

    **完整** 更新包更新缩放单元中的物理主机操作系统，需要的维护时段更长。

    **快速** 更新包有范围限制，不更新基础的物理主机操作系统。

- **Azure Stack Hub 修补程序**。 Microsoft 提供 [Azure Stack Hub 的修补程序](azure-stack-servicing-policy.md#hotfixes)（通常是预防性或时效性的程序）来解决特定问题。 每个修补程序都附带了相应的 Microsoft 知识库文章，其中详细说明了该程序包中的修补程序。 可以像下载和安装普通的 Azure Stack Hub 完整更新包一样下载和安装修补程序。 修补程序是累积性的，在几分钟内即可完成安装。

   从版本 2005 开始，更新到新的主版本（如 1.2002.x 到 1.2005.x）时，会自动安装新的主版本中的最新修补程序（如果有）。 在此之后，如果发布了适用于你的内部版本的修补程序，则应安装它。

- **OEM 硬件供应商提供的更新**。 Azure Stack Hub 硬件合作伙伴负责硬件相关固件和驱动程序更新包的端到端服务生命周期（包括指导）。 此外，对于硬件生命周期主机上的所有软件和硬件，Azure Stack Hub 硬件合作伙伴拥有并维护指导。 OEM 硬件供应商在自己的下载站点上托管这些更新包。

## <a name="when-to-update"></a>何时更新

这三种类型的更新按以下频率发布：

- **Azure Stack Hub 软件更新**。 Microsoft 每年都会发布多个完整的快速软件更新包。

- **Azure Stack Hub 修补程序**。 修补程序是随时可发布的时效性版本。 如果要从一个主版本升级到另一个版本 (例如，1.2002 到 1.2005) ，则会自动安装最新的修补程序（如果已为该新主版本发布的修补程序）。

- **OEM 硬件供应商提供的更新**。 OEM 硬件供应商会根据需要发布更新。

若要继续获得支持，必须在 Azure Stack Hub 环境中保留支持的 Azure Stack Hub 软件版本。 有关详细信息，请参阅 [Azure Stack Hub 服务策略](azure-stack-servicing-policy.md)。

## <a name="how-to-know-an-update-is-available"></a>如何知道推出了更新

更新通知根据多种因素而异，例如，是否与 Internet 建立了连接，以及更新的类型。

- **Microsoft 软件更新和修补程序**

    Microsoft 软件更新和修补程序的更新警报会显示在已连接到 Internet 的 Azure Stack Hub 实例的“更新”边栏选项卡中。 如果未显示“更新”边栏选项卡，请重启基础结构管理控制器 VM。

    如果你的实例未连接，并且你希望收到有关每个修补程序版本的通知，请订阅 [RSS 源](https://azurestackhubdocs.azurewebsites.net/xml/hotfixes.rss)。

- **OEM 硬件供应商提供的更新**

    OEM 更新取决于制造商。 您必须与 OEM 建立通信通道，以便您可以了解 OEM 中需要应用的更新。 有关 OEM 和 OEM 更新过程的详细信息，请参阅[应用 Azure Stack Hub 原始设备制造商 (OEM) 更新](azure-stack-update-oem.md)。

### <a name="major-version-to-major-version"></a>主版本到主版本

主版本到主版本的更新必须分步进行：当前环境只能更新到下一个主版本，无法跳过主版本更新。

例如，如果 Azure Stack Hub 环境为 1908.x，而最新的可用更新版本为 2002.x，需要从 1908 更新到 1910，然后再更新到 2002。

从版本 2005 开始，更新到新的主版本（如 1.2002.x 到 1.2005.x）时，会自动安装新的主版本中的最新修补程序（如果有）。

### <a name="hotfixes-within-major-versions"></a>主版本中的修补程序

在同一主版本号内，Azure Stack Hub 可能会发布多个修补程序。 修补程序是累积的，最新的修补程序包包括该版本过去的所有修补程序。 有关详细信息，请参阅[修补程序](azure-stack-servicing-policy.md#hotfixes)。

## <a name="update-process"></a>更新过程

知道有可用的更新后，使用以下步骤应用更新。

![Azure Stack Hub 更新过程](./media/azure-stack-updates/azure-stack-update-process.svg)

1. **规划更新**

    准备好 Azure Stack Hub，使更新过程能够尽量顺畅地进行，并尽量减轻对用户造成的影响。 向用户通知任何可能的服务中断，然后遵循准备要更新的实例的步骤操作。 请务必遵循 [Azure Stack Hub 更新前查检表](release-notes-checklist.md)中的所有步骤，确保完成应用更新所需的先决条件。 另外，请确保根据要应用的更新类型安排适当的维护时段。

2. **上传和准备更新包**

    对于已连接到 Internet 的 Azure Stack Hub 环境，Azure Stack Hub 软件更新和修补程序会自动导入系统并做好更新准备。

    对于已断开 Internet 连接的 Azure Stack Hub 环境，以及 Internet 连接质量很差或间歇性连接的环境，更新包将通过 Azure Stack Hub 管理员门户导入 Azure Stack Hub 存储。 有关上传和准备更新包的详细步骤，请参阅[上传和准备 Azure Stack Hub 更新包](azure-stack-update-prepare-package.md)。

    无论 Azure Stack Hub 系统是否建立了 Internet 连接，都需要手动将所有 OEM 更新包导入到环境中。 有关导入和准备更新包的详细步骤，请参阅[上传和准备 Azure Stack Hub 更新包](azure-stack-update-prepare-package.md)。

3. **应用更新**

    使用 Azure Stack Hub 门户中的“更新”边栏选项卡应用更新。 在更新过程中，可以监视更新进度和进行故障排除。 有关详细信息，请参阅[应用 Azure Stack Hub 更新](azure-stack-apply-updates.md)。

## <a name="the-update-resource-provider"></a>更新资源提供程序

Azure Stack Hub 包含用于处理 Microsoft 软件更新应用程序的更新资源提供程序。 此提供程序将检查所有物理主机、Service Fabric 应用和运行时以及所有基础结构虚拟机及其关联的服务上是否应用了更新。

当更新安装时，由于更新进程以 Azure Stack Hub 中的各种子系统（例如，物理主机和基础结构虚拟机）为目标，因此你可以查看高级状态。

## <a name="next-steps"></a>后续步骤

- 若要开始更新过程，请按照 [Azure Stack Hub 更新活动清单](release-notes-checklist.md)中的步骤操作。
- 若要了解支持的 Azure Stack Hub 版本，请参阅 [Azure Stack Hub 服务策略](azure-stack-servicing-policy.md)。  
- 若要详细了解当前更新和最近的更新，请参阅 [Azure Stack Hub 发行说明](release-notes.md)。
