---
title: 管理 Azure Stack 中的更新 |Microsoft Docs
description: 了解如何管理 Azure Stack 中的更新
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 0cd83c7a16ef56e3432de7dcba39cc11ca20a379
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70008475"
---
# <a name="manage-updates-in-azure-stack-overview"></a>在 Azure Stack 中管理更新概述

适用范围：*Azure Stack 集成系统*

你需要通过应用 Azure Stack 完整和快速更新、修补程序和原始设备制造商 (OEM) 包更新来保持 Azure Stack 最新状态。 通过最新的更新保持 Azure Stack 最新的更新将最新的安全修补程序、产品更新、驱动程序和固件更新应用于您的系统。 本文概述了不同的更新包、这些版本包的节奏、在何处了解当前版本以及整体更新过程。

> [!Note]  
> 无法将 Azure Stack 更新包应用于 Azure Stack 开发工具包 (ASDK)。 更新包专为集成系统所设计。 有关信息，请参阅[重新部署 ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-redeploy)。

## <a name="update-package-types"></a>更新包类型

对于集成系统, 有三种类型的更新包:

-   **Azure Stack 软件更新**。 Microsoft 会负责 Microsoft 软件更新包的端到端服务生命周期。 这些包可以包括最新的 Windows Server 安全更新、非安全更新和 Azure Stack 功能更新。 直接从 Microsoft 下载这些更新包。

    每个更新包都有相应的类型**Full**或**Express**。 
 
    **完整**更新包将更新缩放单位的物理主机操作系统, 并要求更大的维护时段。 

    **快速**更新包的作用域, 并且不更新底层的物理主机操作系统。

-   **Azure Stack 修补程序**。 Microsoft 为 Azure Stack 提供修补程序, 用于解决通常是预防或区分时间的特定问题。 发布的每个修补程序都附带相应的 Microsoft 知识库文章，其中详细描述了问题、原因和解决方法。 下载并安装修补程序, 就像 Azure Stack 的常规完整更新包一样。 修补程序是累积的, 可以在几分钟内安装。

-   **OEM 硬件供应商提供的更新**。 Azure Stack 硬件合作伙伴负责硬件相关固件和驱动程序更新包的端到端服务生命周期（包括指导）。 此外，对于硬件生命周期主机上的所有软件和硬件，Azure Stack 硬件合作伙伴拥有并维护指导。 OEM 硬件供应商在自己的下载站点上托管这些更新包。

## <a name="when-to-update"></a>更新时间

这三种类型的更新均已发布, 并具有以下节奏:

-   **Azure Stack 软件更新**。 Microsoft 通常每个月发布一次软件更新包。

-   **Azure Stack 修补程序**。 修补程序是可随时发布的时间敏感的版本。

-   **OEM 硬件供应商提供的更新**。 OEM 硬件供应商会根据需要发布更新。

若要继续获得支持, 必须在支持的 Azure Stack 软件版本上保留 Azure Stack 环境。 有关详细信息, 请参阅[Azure Stack 维护策略](azure-stack-update-servicing-policy.md)。

## <a name="where-to-get-notice-of-an-update"></a>在何处获取更新通知

更新通知因几个因素而异, 例如与 Internet 的连接和更新类型。

- **Microsoft 软件更新和修补程序** 

    对于连接到 internet 的 Azure Stack 实例, Microsoft 软件更新和修补程序的更新警报将显示在更新边栏选项卡中。

    如果你的实例未连接, 并且你希望收到有关每个修补程序版本的通知, 请订阅[RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/32d322a8-acae-202d-e9a9-7371dccf381b/rss)或[ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/32d322a8-acae-202d-e9a9-7371dccf381b/atom)馈送。

- **OEM 硬件供应商提供的更新**

    OEM 更新将取决于你的制造商。 需要与 OEM 建立通信通道, 以便在需要应用的 OEM 更新时进行了解。 有关 Oem 和 OEM 更新过程的详细信息, 请参阅[Apply Azure Stack 原始设备制造商 (OEM) 更新](azure-stack-update-oem.md)。

## <a name="update-processes"></a>更新进程

知道有更新后, 请使用以下步骤来应用更新。

![Azure Stack 更新过程](./media/azure-stack-updates/azure-stack-update-process.png)

1. **规划更新**。

    准备 Azure Stack, 使更新过程尽可能顺利地进行, 以便对用户的影响最小。 向用户通知任何可能的服务中断, 然后按照步骤准备要更新的实例。 有关计划更新的更多步骤, 请参阅[计划 Azure Stack 更新](azure-stack-update-plan.md)。

2. **上载并准备更新包**。

    对于连接 internet 的 Azure Stack 环境, Azure Stack 软件更新和修补程序会自动导入到系统中并准备好进行更新。

    对于 internet 断开连接 Azure Stack 环境和具有弱或间歇性 internet 连接的环境, 更新包将通过 Azure Stack 管理员门户导入 Azure Stack 存储中。 有关上传和准备更新包的更多步骤, 请参阅[上传和准备 Azure Stack 更新包](azure-stack-update-prepare-package.md)。

    不管 Azure Stack 系统的 internet 连接, 所有 OEM 更新包都将手动导入到你的环境中。 有关导入和准备更新包的更多步骤, 请参阅[上传和准备 Azure Stack 更新包](azure-stack-update-prepare-package.md))。

3. **应用更新**。

    使用 Azure Stack 中的**更新**边栏选项卡应用更新。 在更新过程中, 监视更新进度, 并在必要时对更新过程进行故障排除。 有关计划更新的更多步骤, 请参阅[应用 Azure Stack 更新](azure-stack-apply-updates.md)。

## <a name="the-update-resource-provider"></a>更新资源提供程序

Azure Stack 包含一个更新资源提供程序, 用于处理 Microsoft 软件更新的应用程序。 此提供程序将检查所有物理主机、Service Fabric 应用程序和运行时以及所有基础结构虚拟机及其关联的服务上是否应用了更新。

当更新安装时，由于更新进程以 Azure Stack 中的各种子系统（例如，物理主机和基础结构虚拟机）为目标，因此你可以查看高级状态。

## <a name="next-steps"></a>后续步骤

- 若要开始更新过程, 请遵循参阅[计划 Azure Stack 更新](azure-stack-update-plan.md)中的步骤。
- 若要了解支持的 Azure Stack 版本, 请参阅[Azure Stack 服务策略](azure-stack-servicing-policy.md)。  
- 若要了解有关当前更新和最新更新的详细信息, 请参阅[Azure Stack 发行说明](azure-stack-release-notes-security-updates-1907.md)。
