---
title: 管理更新
description: 了解如何在 Azure Stack 中心管理更新
author: IngridAtMicrosoft
ms.topic: article
ms.date: 10/01/2019
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 69deade8a7084f751fda6d9f71ad3e7f3f5fc0f8
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696579"
---
# <a name="manage-updates-in-azure-stack-hub"></a>管理 Azure Stack 集线器中的更新

从原始设备制造商（OEM）获取完整的和快速的更新、修补程序以及驱动程序和固件更新，这一切都有助于保持 Azure Stack 集线器的最新状态。 本文介绍了不同类型的更新、何时需要发布，以及在何处可以找到有关当前版本的详细信息。

> [!Note]  
> 不能将 Azure Stack 中心更新包应用到 Azure Stack 开发工具包（ASDK）。 更新包专为集成系统设计。 有关信息，请参阅重新[部署 ASDK](https://docs.microsoft.com/azure-stack/asdk/asdk-redeploy)。

## <a name="update-package-types"></a>更新包类型

对于集成系统，有三种类型的更新包：

- **Azure Stack 中心软件更新**。 Microsoft 负责 Microsoft 软件更新包的端到端服务生命周期。 这些包可以包括最新的 Windows Server 安全更新、非安全更新和 Azure Stack 集线器功能更新。 直接从 Microsoft 下载这些更新包。

    每个更新包都有相应的类型： **Full**或**Express**。

    **完整**更新包将更新缩放单位的物理主机操作系统，并要求更大的维护时段。

    **快速**更新包的作用域不是更新基础物理主机操作系统。

- **Azure Stack 中心修补程序**。 Microsoft 为 Azure Stack 中心提供修补程序，用于解决通常是预防或区分时间的特定问题。 每个修补程序都附带了相应的 Microsoft 知识库文章，其中详细介绍了问题、原因和解决方法。 下载并安装修补程序，这与 Azure Stack 中心的常规完整更新包相同。 修补程序是累积的，可以在几分钟内安装。

- **OEM 硬件供应商提供的更新**。 Azure Stack 集线器硬件合作伙伴负责与硬件相关的固件和驱动程序更新包的端到端服务生命周期（包括指南）。 此外，Azure Stack 集线器硬件合作伙伴拥有和维护硬件生命周期主机上所有软件和硬件的指导。 OEM 硬件供应商在其自己的下载站点上托管这些更新包。

## <a name="when-to-update"></a>更新时间

这三种类型的更新均已发布，并具有以下节奏：

- **Azure Stack 中心软件更新**。 Microsoft 通常每个月发布一次软件更新包。

- **Azure Stack 中心修补程序**。 修补程序是可随时发布的时间敏感的版本。

- **OEM 硬件供应商提供的更新**。 OEM 硬件供应商需要根据需要发布更新。

若要继续获得支持，必须在支持的 Azure Stack 中心软件版本上保留 Azure Stack 中心环境。 有关详细信息，请参阅[Azure Stack Hub 维护策略](azure-stack-update-servicing-policy.md)。

## <a name="where-to-get-notice-of-an-update"></a>在何处获取更新通知

更新通知因几个因素而异，例如与 internet 的连接和更新类型。

- **Microsoft 软件更新和修补程序**

    对于连接到 internet 的 Azure Stack 集线器实例，Microsoft 软件更新和修补程序的更新警报将显示在**更新**边栏选项卡中。 如果未显示 "**更新**" 边栏选项卡，请重新启动基础结构管理控制器 VM。

    如果你的实例未连接，并且你希望收到有关每个修补程序版本的通知，请订阅[RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)或[ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)馈送。

- **OEM 硬件供应商提供的更新**

    OEM 更新将取决于你的制造商。 你需要与 OEM 建立通信通道，以便能够了解你的 OEM 提供的需要应用的更新。 有关 Oem 和 OEM 更新过程的详细信息，请参阅[应用 Azure Stack 中心原始设备制造商（OEM）更新](azure-stack-update-oem.md)。

## <a name="update-processes"></a>更新进程

知道有更新后，请使用以下步骤来应用更新。

![Azure Stack 中心更新过程](./media/azure-stack-updates/azure-stack-update-process.png)

1. **规划更新**。

    准备 Azure Stack 中心，使更新过程尽可能顺利地进行，以便对用户的影响降至最低。 通知用户任何可能发生的服务中断，然后按照步骤准备要更新的实例。 务必按照[Azure Stack 中心更新前清单](release-notes-checklist.md)中的**所有**步骤操作，确保已完成应用更新所需的 presteps。 另外，请确保为要应用的更新类型安排适当的维护时段。

2. **上载并准备更新包**。

    对于连接 internet 的 Azure Stack 集线器环境，Azure Stack 中心软件更新和修补程序会自动导入到系统中并准备好进行更新。

    对于 internet 断开连接 Azure Stack 集线器环境和具有弱或间歇性 internet 连接的环境，更新包通过 Azure Stack 中心管理员门户导入到 Azure Stack 中心存储中。 有关上传和准备更新包的更多步骤，请参阅[上传和准备 Azure Stack 中心更新包](azure-stack-update-prepare-package.md)。

    不管 Azure Stack 集线器系统的 internet 连接，所有 OEM 更新包都将手动导入到你的环境中。 有关导入和准备更新包的更多步骤，请参阅[上传和准备 Azure Stack 中心更新包](azure-stack-update-prepare-package.md)。

3. **应用更新**。

    使用 Azure Stack 集线器中的**更新**边栏选项卡应用更新。 在更新过程中，监视更新进度并对其进行故障排除。 有关详细信息，请参阅[应用 Azure Stack 的中心更新](azure-stack-apply-updates.md)。

## <a name="the-update-resource-provider"></a>更新资源提供程序

Azure Stack 中心包含一个更新资源提供程序，用于处理 Microsoft 软件更新的应用程序。 此提供程序检查是否在所有物理主机、Service Fabric 应用和运行时以及所有基础结构虚拟机及其关联服务上应用了更新。

当更新安装时，你可以查看高级状态，因为更新过程针对 Azure Stack 集线器中的各种子系统（例如，物理主机和基础结构虚拟机）。

## <a name="next-steps"></a>后续步骤

- 若要开始更新过程，请按照中的步骤操作[Azure Stack 中心更新活动清单](release-notes-checklist.md)。
- 若要了解支持的 Azure Stack 中心版本，请参阅[Azure Stack 中心维护策略](azure-stack-servicing-policy.md)。  
- 若要了解有关当前更新和最新更新的详细信息，请参阅[Azure Stack 中心发行说明](release-notes.md)。
