---
title: 使用 Azure 和 Azure Stack 中心为分析解决方案实现分层数据的模式。
description: 了解如何使用 Azure 和 Azure Stack 中心服务在混合云之间实现分层数据解决方案。
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: ac2c573e9ee1a2dad3afcdf86c9a6c273fb0f4e5
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73638306"
---
# <a name="tiered-data-for-analytics-pattern"></a>分析模式的分层数据

此模式说明了如何使用 Azure Stack 中心和 Azure 跨多个本地和云位置暂存、分析、处理、净化和存储数据。

## <a name="context-and-problem"></a>上下文和问题

当今技术领域的企业组织面临的问题之一就是保护数据存储、处理和分析。 注意事项包括：
- 数据内容
- 位置
- 安全和隐私要求
- 访问权限
- 维护
- 存储仓库

与 Azure Stack 中心结合在一起，可解决数据问题并提供低成本的解决方案。 此解决方案最好通过分布式制造或后勤公司来表达。 

此解决方案基于以下方案：
- 大型多分支制造组织。
- 需要在全局远程位置与总部总部之间进行快速、安全的数据存储、处理和分发。 
- 必须保持安全的员工和设备活动、设施信息以及业务报表数据。 必须适当地分发数据，并满足区域合规性策略和行业规定。

## <a name="solution"></a>解决方案

同时利用本地和公有云环境满足多设施企业的需求。 Azure Stack 中心提供快速、安全和灵活的解决方案，用于收集、处理、存储和分发本地和远程数据。 当位置和用户之间的安全、机密性、公司政策和法规要求可能不同时，尤其如此。 

![用于分析解决方案体系结构的分层数据](media/pattern-tiered-data-analytics/solution-architecture.png)

## <a name="components"></a>组件

此解决方案使用以下组件：

| 层 | 组件 | 描述 |
|----------|-----------|-------------|
| Azure | 存储空间 | [Azure 存储](/azure/storage/)帐户提供取证数据消耗终结点。 Azure 存储是 Microsoft 提供的适用于现代数据存储场景的云存储解决方案。 Azure 存储为适用于云的数据对象和文件系统服务提供了一个高度可缩放的对象存储。 它还提供可靠消息传送和 NoSQL 存储的消息存储。 |
| Azure Stack 中心 | 存储空间 | [Azure Stack 中心存储](/azure-stack/user/azure-stack-storage-overview)帐户用于多个服务：<br>- 用于原始数据存储的**Blob 存储**。 Blob 存储可以存储任何类型的文本或二进制数据，例如文档、媒体文件或应用程序安装程序。 每个 Blob 组织在容器中。 容器提供向对象组分配安全策略的有用方法。 一个存储帐户可以包含任意数目的容器，一个容器可以包含任意数目的 blob，最多可包含存储帐户的 500 TB 的容量限制。<br>用于数据存档 - **Blob 存储**。 对于冷数据存档，可通过低成本存储提供便利。 包含备份、媒体内容、科研数据、合规性和存档数据的冷数据示例。 通常，不常访问的任何数据都被视为 "冷存储"。 基于属性（如访问频率和保留期）来分层数据。 不常访问客户数据，但需要对热数据进行类似的延迟和性能。<br>为已处理的数据存储 - **队列存储**。 队列存储在应用程序组件之间提供云消息传送。 在设计规模应用程序时，应用程序组件通常是分离的，因此它们可以独立缩放。 队列存储提供了用于在应用程序组件之间进行通信的异步消息传送。  它们是在云中、在桌面上、在本地服务器上运行还是在移动设备上运行。 队列存储还支持管理异步任务以及构建过程工作流。 |
| | Azure Functions | [Azure Functions](/azure/azure-functions/)服务由[Azure Stack Hub](/azure-stack/operator/azure-stack-app-service-overview)资源提供程序上的 Azure App Service 提供。 Azure Functions 允许在简单的无服务器环境中执行代码，以运行脚本或代码来响应各种事件。 Azure Functions 扩展，无需使用所选的编程语言创建 VM 或发布 web 应用即可满足需求。 函数由解决方案用于：<br>- **数据**引入<br>- **数据 sterilization。** 手动触发的函数可以执行计划的数据处理、清理和存档。 示例可能包括每夜客户列表清理和每月报表处理。|

## <a name="issues-and-considerations"></a>问题和注意事项

决定如何实现此解决方案时，请注意以下几点：

### <a name="scalability"></a>可伸缩性 

Azure 功能和存储解决方案可进行缩放，以满足数据量和处理需求。 有关 Azure 可伸缩性信息和目标，请参阅[Azure 存储可伸缩性文档](/azure/storage/common/storage-scalability-targets)。 

### <a name="availability"></a>可用性

存储是此模式的主要可用性注意事项。 需要通过快速链接进行连接，以便进行大型数据量处理和分发。 

### <a name="manageability"></a>可管理性

此解决方案的可管理性取决于所使用的创作工具和源代码管理的参与。 

## <a name="next-steps"></a>后续步骤

若要详细了解本文中介绍的主题：
- 请参阅[Azure 存储](/azure/storage/)和[Azure Functions](/azure/azure-functions/)文档。 此模式可在 Azure 和 Azure Stack 中心大量使用 Azure 存储帐户和 Azure Functions。
- 有关最佳实践的详细信息，请参阅[混合应用程序设计注意事项](overview-app-design-considerations.md)，并回答其他问题。
- 请参阅[Azure Stack 系列产品和解决方案](/azure-stack)，详细了解产品和解决方案的整个产品组合。

准备好测试解决方案示例时，请继续学习[用于分析解决方案部署指南的分层数据](https://aka.ms/tiereddatadeploy)。 部署指南提供了有关部署和测试其组件的分步说明。