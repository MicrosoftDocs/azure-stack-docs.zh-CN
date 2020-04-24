---
title: 管理更新
description: 了解如何在 Azure Stack Hub 中管理更新
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 48bc8e2df857b2e1c352ffaa55ef900672d4f78b
ms.sourcegitcommit: a3ae6dd8670f8fb24224880df7eee256ebbcc4ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772506"
---
# <a name="manage-updates-in-azure-stack-hub"></a>在 Azure Stack Hub 中管理更新

原始设备制造商 (OEM) 提供的完整和快速更新、修补程序以及驱动程序与固件更新都有助于让 Azure Stack Hub 保持最新状态。 本文介绍不同类型的更新、其预期发布时间，以及在何处可以找到有关当前版本的详细信息。

> [!Note]  
> 无法将 Azure Stack Hub 更新包应用于 Azure Stack 开发工具包 (ASDK)。 更新包专为集成系统所设计。 有关信息，请参阅[重新部署 ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-redeploy)。

## <a name="update-package-types"></a>更新包类型

集成系统有三种类型的更新包：

- **Azure Stack Hub 软件更新**。 Microsoft 会负责 Microsoft 软件更新包的端到端服务生命周期。 这些包可以包括最新的 Windows Server 安全更新、非安全更新和 Azure Stack Hub 功能更新。 可以直接从 Microsoft 下载这些更新包。

    每个更新包都有对应的类型：“完整”或“快速”。  

    **完整**更新包更新缩放单元中的物理主机操作系统，需要的维护时段更长。

    **快速**更新包有范围限制，不更新基础的物理主机操作系统。

- **Azure Stack Hub 修补程序**。 Microsoft 为 Azure Stack 中心提供修补程序，用于解决通常是预防或区分时间的特定问题。 发布的每个修补程序都附带相应的 Microsoft 知识库文章，其中详细描述了问题、原因和解决方法。 可以像下载和安装普通的 Azure Stack Hub 完整更新包一样下载和安装修补程序。 修补程序是累积性的，在几分钟内即可完成安装。

- **OEM 硬件供应商提供的更新**。 Azure Stack Hub 硬件合作伙伴负责硬件相关固件和驱动程序更新包的端到端服务生命周期（包括指导）。 此外，对于硬件生命周期主机上的所有软件和硬件，Azure Stack Hub 硬件合作伙伴拥有并维护指导。 OEM 硬件供应商在自己的下载站点上托管这些更新包。

## <a name="when-to-update"></a>何时更新

这三种类型的更新按以下频率发布：

- **Azure Stack Hub 软件更新**。 Microsoft 通常每个月发布一次软件更新包。

- **Azure Stack Hub 修补程序**。 修补程序是随时可发布的时效性版本。

- **OEM 硬件供应商提供的更新**。 OEM 硬件供应商会根据需要发布更新。

若要继续获得支持，必须在 Azure Stack Hub 环境中保留支持的 Azure Stack Hub 软件版本。 有关详细信息，请参阅 [Azure Stack Hub 服务策略](azure-stack-update-servicing-policy.md)。

## <a name="where-to-get-notice-of-an-update"></a>在何处获取更新通知

更新通知根据多种因素而异，例如，是否与 Internet 建立了连接，以及更新的类型。

- **Microsoft 软件更新和修补程序**

    Microsoft 软件更新和修补程序的更新警报会显示在已连接到 Internet 的 Azure Stack Hub 实例的“更新”边栏选项卡中  。 如果未显示“更新”边栏选项卡，请重启基础结构管理控制器 VM  。

    如果在实例未连接的情况下希望获得每个修补程序版本的通知，请订阅 [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) 或 [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) 源。

- **OEM 硬件供应商提供的更新**

    OEM 更新取决于制造商。 需要与 OEM 建立信道，以便了解需要应用的来自 OEM 的更新。 有关 OEM 和 OEM 更新过程的详细信息，请参阅[应用 Azure Stack Hub 原始设备制造商 (OEM) 更新](azure-stack-update-oem.md)。

## <a name="update-processes"></a>更新过程

知道有可用的更新后，使用以下步骤应用更新。

![Azure Stack Hub 更新过程](./media/azure-stack-updates/azure-stack-update-process.svg)

1. **规划更新**。

    准备好 Azure Stack Hub，使更新过程能够尽量顺畅地进行，并尽量减轻对用户造成的影响。 向用户通知任何可能的服务中断，然后遵循准备要更新的实例的步骤操作。 请务必遵循 [Azure Stack Hub 更新前查检表](release-notes-checklist.md)中的**所有**步骤，确保完成应用更新所需的先决步骤。 另外，请确保根据要应用的更新类型安排适当的维护时段。

2. **上传和准备更新包**。

    对于已连接到 Internet 的 Azure Stack Hub 环境，Azure Stack Hub 软件更新和修补程序会自动导入系统并做好更新准备。

    对于已断开 Internet 连接的 Azure Stack Hub 环境，以及 Internet 连接质量很差或间歇性连接的环境，更新包将通过 Azure Stack Hub 管理员门户导入 Azure Stack Hub 存储。 有关上传和准备更新包的详细步骤，请参阅[上传和准备 Azure Stack Hub 更新包](azure-stack-update-prepare-package.md)。

    无论 Azure Stack Hub 系统是否建立了 Internet 连接，都需要手动将所有 OEM 更新包导入到环境中。 有关导入和准备更新包的详细步骤，请参阅[上传和准备 Azure Stack Hub 更新包](azure-stack-update-prepare-package.md)。

3. **应用更新**。

    使用 Azure Stack Hub 中的“更新”边栏选项卡应用更新。  在更新过程中，可以监视更新进度和进行故障排除。 有关详细信息，请参阅[应用 Azure Stack Hub 更新](azure-stack-apply-updates.md)。

## <a name="the-update-resource-provider"></a>更新资源提供程序

Azure Stack Hub 包含用于处理 Microsoft 软件更新应用程序的更新资源提供程序。 此提供程序将检查所有物理主机、Service Fabric 应用和运行时以及所有基础结构虚拟机及其关联的服务上是否应用了更新。

当更新安装时，由于更新进程以 Azure Stack Hub 中的各种子系统（例如，物理主机和基础结构虚拟机）为目标，因此你可以查看高级状态。

## <a name="next-steps"></a>后续步骤

- 若要开始更新过程，请按照 [Azure Stack Hub 更新活动清单](release-notes-checklist.md)中的步骤操作。
- 若要了解支持的 Azure Stack Hub 版本，请参阅 [Azure Stack Hub 服务策略](azure-stack-servicing-policy.md)。  
- 若要详细了解当前更新和最近的更新，请参阅 [Azure Stack Hub 发行说明](release-notes.md)。
