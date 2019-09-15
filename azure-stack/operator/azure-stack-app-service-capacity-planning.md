---
title: Azure Stack 中的应用服务服务器角色的容量规划 |Microsoft Docs
description: 了解 Azure Stack 中应用服务服务器角色的容量规划。
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/13/2019
ms.openlocfilehash: 80dc7bae2371025fba82531b08216606580176e1
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70975202"
---
# <a name="capacity-planning-for-app-service-server-roles-in-azure-stack"></a>Azure Stack 中的应用服务服务器角色的容量规划

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

若要设置 Azure Stack 上的 Azure App Service 的生产就绪部署，你必须计划系统支持的容量。  

本文提供了有关应当为任何生产部署使用的最少计算实例和计算 SKU 数量的指南。

可以使用这些准则来计划应用服务容量策略。

| 应用服务服务器角色 | 建议的最少实例数 | 建议的计算 SKU|
| --- | --- | --- |
| 控制器 | 2 | A1 |
| 前端 | 2 | A1 |
| 管理 | 2 | A3 |
| 发布者 | 2 | A1 |
| Web 辅助角色 - 共享 | 2 | A1 |
| Web 辅助角色 - 专用 | 每层 2 个 | A1 |

## <a name="controller-role"></a>控制器角色

**建议的最小值**：两个 A1 Standard 实例

Azure 应用服务控制器通常消耗较少的 CPU、内存和网络资源。 但是，为了实现高可用性，必须具有两个控制器。 两个控制器也是允许的最大控制器数。 在部署期间可以直接通过安装程序创建第二个网站控制器。

## <a name="front-end-role"></a>前端角色

**建议的最小值**：两个 A1 Standard 实例

前端根据 Web 辅助角色可用性将请求路由到 Web 辅助角色。 为实现高可用性，应配备一个以上前端，并且可以配备两个以上。 对于容量计划用途，请考虑每个核心每秒可以处理大约 100 个请求。

## <a name="management-role"></a>管理角色

**建议的最小值**：两个 A3 Standard 实例

Azure 应用经典部署模型角色负责应用服务 Azure 资源管理器和 API 终结点、门户扩展（管理员、租户、函数门户）和数据服务。 在生产环境中，管理服务器角色通常仅需要大约 4-GB RAM。 但是，当执行许多管理任务（例如创建网站）时，它可能会经历很高的 CPU 利用率水平。 为实现高可用性，应当将一台以上服务器分配给此角色，并且每台服务器至少两个核心。

## <a name="publisher-role"></a>发布服务器角色

**建议的最小值**：两个 A1 Standard 实例

如果许多用户同时发布，则发布服务器角色可能会经历很高的 CPU 使用率。 为实现高可用性，请务必提供多个发布服务器角色。 发布服务器仅处理 FTP/FTPS 流量。

## <a name="web-worker-role"></a>Web 辅助角色

**建议的最小值**：两个 A1 Standard 实例

为实现高可用性，应至少具有四个 web 工作进程角色：两个用于共享网站模式，两个用于你计划提供的每个专用辅助角色层。 共享计算模式和专用计算模式向租户提供不同的服务级别。 如果你有符合下述情况的许多客户，则可能需要更多 Web 辅助角色：

- 使用专用的计算模式辅助角色层（资源密集型）。
- 在共享计算模式下运行。

在用户为专用计算模式 SKU 创建应用服务计划后，在该应用服务计划中指定的 Web 辅助角色将不再可供用户使用。

若要在消耗计划模型下向用户提供 Azure Functions，则必须部署共享 Web 辅助角色。

决定要使用的共享 Web 辅助角色数量时，请查看以下注意事项：

- **内存**：内存是对 Web 辅助角色最重要的资源。 从磁盘交换虚拟内存时，内存不足会影响网站性能。 每台服务器的操作系统需要大约 1.2 GB 的 RAM。 可以使用高于此阈值的 RAM 来运行网站。
- **活动网站的百分比**：通常，在 Azure Stack 部署的 Azure App Service 中，大约 5% 的应用处于活动状态。 但是，在任何给定时刻处于活动状态的应用的百分比可以更高或更低。 如果活动应用速率为 5%，则在 Azure Stack 部署中 Azure App Service 的最大应用数应小于活动网站数的20倍（5 x 20 = 100）。
- **平均内存占用**：在生产环境中观察到的应用的平均内存占用约为 70 MB。 使用此空间时，将按如下所示计算跨所有 web 辅助角色计算机或 Vm 分配的内存：

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   例如，如果在运行10个 web 工作进程角色的环境中存在5000应用，则每个 web 辅助角色 VM 应具有 7060 MB RAM：

   `5,000 * 70 * 0.05 - (10 * 1044) = 7060 (= about 7 GB)`

   有关添加更多辅助角色实例的信息，请参阅[添加更多辅助角色](azure-stack-app-service-add-worker-roles.md)。

### <a name="additional-considerations-for-dedicated-workers-during-upgrade-and-maintenance"></a>专用辅助角色在升级和维护期间的其他注意事项

在升级和维护辅助角色期间，Azure Stack 上的 Azure 应用服务一次针对每个辅助角色层的 20% 的资源执行维护。  因此，云管理员必须始终维护每个辅助角色层的未分配工作线程数的 20%，以确保其租户在升级和维护期间不会遇到任何服务损失。  例如，如果在辅助角色层中有10个工作线程，则应确保2未分配为允许升级和维护。 如果已分配了完整的10个工作线程，则应该向上扩展辅助角色层以维护未分配的工作线程池。 

在升级和维护期间，Azure App Service 会将工作负荷转移到未分配的工作线程，以确保工作负荷将继续运行。 但是，如果在升级过程中没有未分配的工作线程，则可能会导致租户工作负荷停机。 与共享辅助角色相关，客户无需预配其他工作人员，因为服务会自动在可用工作线程内分配租户应用。 为实现高可用性，此层中有两个辅助角色的最低要求。

云管理员可以在 Azure Stack 管理员门户的 "应用服务管理" 区域中监视其辅助角色分配。 导航到“应用服务”，然后在左窗格中选择“辅助角色层”。 “辅助角色层”表会显示辅助角色层的名称、大小、使用的映像、可用辅助角色数目（未分配）、每层中的辅助角色总数，以及辅助角色层的整体状态。

![应用服务管理 - 辅助角色层][1]

## <a name="file-server-role"></a>文件服务器角色

对于文件服务器角色，可以使用独立的文件服务器进行开发和测试。 例如，在 Azure Stack 开发工具包（ASDK）上部署 Azure App Service 时，可以使用此[模板](https://aka.ms/appsvconmasdkfstemplate)。  对于生产用途，应当使用预先配置的 Windows 文件服务器，或使用预先配置的非 Windows 文件服务器。

在生产环境中，文件服务器角色会经历密集的磁盘 I/O。 由于它包含用户网站的所有内容和应用程序文件，因此应为此角色预配置以下资源之一：

- Windows 文件服务器
- Windows 文件服务器群集
- 非 Windows 文件服务器
- 非 Windows 文件服务器群集
- NAS（网络附加存储）设备

有关详细信息，请参阅[预配文件服务器](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server)。

## <a name="next-steps"></a>后续步骤

[在 Azure Stack 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-capacity-planning/worker-tier-allocation.png