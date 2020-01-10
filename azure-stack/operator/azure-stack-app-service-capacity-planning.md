---
title: Azure Stack 集线器中的应用服务服务器角色的容量规划 |Microsoft Docs
description: 了解 Azure Stack 集线器中应用服务服务器角色的容量规划。
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
ms.openlocfilehash: d03a7a848213be2f57556616e9fdbf4cd3a0f3e2
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75809060"
---
# <a name="capacity-planning-for-app-service-server-roles-in-azure-stack-hub"></a>Azure Stack 中心中的应用服务服务器角色的容量规划

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

若要在 Azure Stack 中心设置生产就绪的 Azure App Service 部署，你必须计划系统支持的容量。  

本文提供了有关可用于任何生产部署的计算实例和计算 Sku 的最小数量的指导。

你可以使用这些准则规划应用服务容量策略。

| 应用服务服务器角色 | 建议的最小实例数 | 建议的计算 SKU|
| --- | --- | --- |
| 控制器 | 2 | A1 |
| 前端 | 2 | A1 |
| 管理 | 2 | A3 |
| 发布者 | 2 | A1 |
| Web 工作进程-共享 | 2 | A1 |
| Web 工作进程-专用 | 每层2个 | A1 |

## <a name="controller-role"></a>控制器角色

**建议的最小值**：两个 A1 标准实例

Azure App Service 控制器通常会消耗 CPU、内存和网络资源的低消耗。 但是，为了实现高可用性，你必须有两个控制器。 两个控制器也是允许的最大控制器数。 在部署过程中，你可以直接从安装程序创建第二个网站控制器。

## <a name="front-end-role"></a>前端角色

**建议的最小值**：两个 A1 标准实例

前端根据 web 工作进程的可用性将请求路由到 web 工作进程。 为实现高可用性，应该有多个前端，并且可有两个以上的前端。 对于容量规划用途，请设想每个核心每秒可处理大约 100 个请求。

## <a name="management-role"></a>管理角色

**建议的最小值**：两个 A3 标准实例

Azure 应用经典部署模型角色负责应用服务 Azure 资源管理器和 API 终结点、门户扩展（管理员、租户、函数门户）和数据服务。 在生产环境中，管理服务器角色通常只需要大约 4 GB RAM。 但是，在执行大量管理任务（如网站创建）时，其 CPU 利用率水平可能较高。 为实现高可用性，应将多个服务器分配给此角色，并且每个服务器至少有两个内核。

## <a name="publisher-role"></a>发布者角色

**建议的最小值**：两个 A1 标准实例

如果许多用户同时发布，则发布者角色可能会出现 CPU 使用率较高的情况。 为实现高可用性，请确保有多个发布者角色可用。 发布服务器仅处理 FTP/FTPS 流量。

## <a name="web-worker-role"></a>Web 辅助角色

**建议的最小值**：两个 A1 标准实例

为实现高可用性，应至少具有四个 web 工作进程角色：两个用于共享网站模式，两个用于你计划提供的每个专用辅助角色层。 共享和专用计算模式向租户提供不同级别的服务。 如果很多客户都需要更多的 web 辅助进程，你可能需要：

- 使用专用计算模式辅助角色层（消耗大量资源）。
- 在共享计算模式下运行。

用户为专用计算模式 SKU 创建应用服务计划后，该应用服务计划中指定的 web 辅助进程数将不再可供用户使用。

若要为消耗计划模型中的用户提供 Azure Functions，必须部署共享的 web 工作进程。

在决定要使用的共享 web 工作进程角色数时，请查看以下注意事项：

- **内存**：内存是 web 辅助角色最重要的资源。 内存不足在从磁盘交换虚拟内存时影响网站性能。 每台服务器需要大约 1.2 GB RAM 用于操作系统。 此阈值以上的 RAM 可用于运行网站。
- **活动网站百分比**：通常，在 Azure Stack 中心部署的 Azure App Service 中，大约5% 的应用处于活动状态。 但是，在任何给定时刻处于活动状态的应用的百分比可以更高或更低。 如果活动应用速率为5%，则 Azure Stack 中心部署中 Azure App Service 的最大应用数应小于活动网站数的20倍（5 x 20 = 100）。
- **平均内存占用量**：在生产环境中观察到的应用的平均内存占用量约为 70 MB。 使用此空间时，将按如下所示计算跨所有 web 辅助角色计算机或 Vm 分配的内存：

   `Number of provisioned applications * 70 MB * 5% - (number of web worker roles * 1044 MB)`

   例如，如果在运行10个 web 工作进程角色的环境中存在5000应用，则每个 web 辅助角色 VM 应具有 7060 MB RAM：

   `5,000 * 70 * 0.05 - (10 * 1044) = 7060 (= about 7 GB)`

   有关添加更多辅助角色实例的信息，请参阅[添加更多辅助角色](azure-stack-app-service-add-worker-roles.md)。

### <a name="additional-considerations-for-dedicated-workers-during-upgrade-and-maintenance"></a>升级和维护期间专用工作人员的其他注意事项

在辅助角色的升级和维护过程中，Azure App Service Azure Stack 中心将在任何一次的每个辅助角色层上执行维护。  因此，云管理员必须始终维护每个辅助角色层的未分配工作线程数的20%，以确保其租户在升级和维护期间不会遇到任何服务损失。  例如，如果在辅助角色层中有10个工作线程，则应确保2未分配为允许升级和维护。 如果已分配了完整的10个工作线程，则应该向上扩展辅助角色层以维护未分配的工作线程池。 

在升级和维护期间，Azure App Service 会将工作负荷转移到未分配的工作线程，以确保工作负荷将继续运行。 但是，如果在升级过程中没有未分配的工作线程，则可能会导致租户工作负荷停机。 与共享辅助角色相关，客户无需预配其他工作人员，因为服务会自动在可用工作线程内分配租户应用。 为实现高可用性，此层中有两个辅助角色的最低要求。

云管理员可以在 Azure Stack 中心管理员门户的 "应用服务管理" 区域中监视其辅助角色分配。 导航到 "应用服务"，然后在左侧窗格中选择 "辅助角色层"。 辅助角色层表显示了辅助角色层名称、大小、使用的图像、可用辅助进程的数量（未分配）、每个层中的辅助进程总数和辅助角色层的总体状态。

![应用服务管理-辅助角色层][1]

## <a name="file-server-role"></a>文件服务器角色

对于文件服务器角色，可以使用独立的文件服务器进行开发和测试。 例如，在 Azure Stack 开发工具包（ASDK）上部署 Azure App Service 时，可以使用此[模板](https://aka.ms/appsvconmasdkfstemplate)。  对于生产用途，应使用预先配置的 Windows 文件服务器或预配置的非 Windows 文件服务器。

在生产环境中，文件服务器角色遇到大量的磁盘 i/o。 由于它包含用户网站的所有内容和应用程序文件，因此应为此角色预配置以下资源之一：

- Windows 文件服务器
- Windows 文件服务器群集
- 非 Windows 文件服务器
- 非 Windows 文件服务器群集
- NAS （网络附加存储）设备

有关详细信息，请参阅[预配文件服务器](azure-stack-app-service-before-you-get-started.md#prepare-the-file-server)。

## <a name="next-steps"></a>后续步骤

[在 Azure Stack 集线器上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-capacity-planning/worker-tier-allocation.png