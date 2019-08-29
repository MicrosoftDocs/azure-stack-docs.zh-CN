---
title: 什么是 ASDK? | Microsoft Docs
description: 了解 Azure Stack 开发工具包 (ASDK) 以及如何使用它来评估 Azure Stack。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 02/08/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 50a08480a098c9ca5a6d50e94dd6dc7dbfc78b09
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118624"
---
# <a name="what-is-the-asdk"></a>什么是 ASDK?
[Microsoft Azure Stack 集成系统](../operator/azure-stack-overview.md)的大小范围是4-16 个节点, 由硬件合作伙伴和 Microsoft 共同支持。 使用 Azure Stack集成系统为自己的生产工作负荷实现新方案。 如果你是 Azure Stack 操作员，想要管理集成系统基础结构并提供服务，请参阅[操作员文档](/azure-stack/operator)。

Azure Stack 开发工具包 (ASDK) 用于对 Azure Stack 进行单节点部署，可以**免费**下载和使用。 所有 ASDK 组件都安装在一台主机上运行的虚拟机 (Vm) 中, 该计算机必须满足或超过[最低硬件要求](asdk-deploy-considerations.md#hardware)。 ASDK 旨在提供一个环境, 在此环境中, 你可以在*非生产*环境中使用与 Azure 一致的 api 和工具来评估 Azure Stack 和开发新式应用。 

> [!IMPORTANT]
> ASDK 不应在生产环境中使用或支持。

因为所有 ASDK 组件都部署到单台主机计算机上, 所以有有限的可用物理资源。 进行 ASDK 部署时，Azure Stack 基础结构 VM 和租户 VM 共存于同一服务器计算机。 此配置不适用于缩放或性能评估。

ASDK 旨在为以下人员提供 Azure 一致性混合云体验：
- **管理员**(Azure Stack 运算符):ASDK 是适用于评估和了解可用 Azure Stack 服务的资源。
- **开发人员**:可以使用 ASDK 在本地 (开发/测试环境) 开发混合或新式应用。 这种灵活性在 Azure Stack 生产部署之前或一起提供了可重复性的开发体验。

观看此短视频，了解有关 ASDK 的详细信息：

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>ASDK 和多节点 Azure Stack 的差异
单节点 ASDK 部署与多节点 Azure Stack 部署的不同之处在于:

|描述|ASDK|多节点 Azure Stack|
|-----|-----|-----|
|**缩放**|所有组件都安装在一个单节点服务器计算机上。|大小范围为 4-16 节点。|
|**复原能力**|单节点配置不提供高可用性|支持[高可用性](../operator/azure-stack-overview.md#providing-high-availability)功能。|
|**网络**|ASDK 主机路由所有 ASDK 网络流量。 无其他切换要求。|需要在多节点部署中使用更复杂的[网络路由基础架构](../operator/azure-stack-network.md#network-infrastructure)，包括架顶 (TOR)、基板管理控制器 (BMC) 和边界（数据中心网络）交换机。|
|**修补和更新过程**|若要移动到 ASDK 的新版本, 必须在 ASDK 主机计算机上重新部署 ASDK。|[修补和更新](../operator/azure-stack-updates.md)过程用于更新已安装的 Azure Stack 版本。|
|**支持**|MSDN Azure Stack 论坛。 Microsoft 客户服务和支持 (CSS) 支持不适用于非生产环境。|[MSDN Azure Stack 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack)和完整的 CSS 支持。|
| | |

## <a name="learn-about-available-services"></a>了解可用的服务
作为 Azure Stack 操作员，你需要了解哪些服务可以提供给用户。 Azure Stack 支持部分 Azure 服务，而随着时间的推移，受支持服务的列表会不断变化。

### <a name="foundational-services"></a>基本服务
默认情况下，ASDK 在部署时包括以下“基本服务”：
- 计算
- 存储
- 网络
- Key Vault

有了这些基本服务，在向用户提供基础结构即服务 (IaaS) 时就可以尽量减少配置。

### <a name="additional-services"></a>其他服务
目前支持下述额外的平台即服务 (PaaS) 服务：
- 应用服务
- Azure Functions
- SQL 和 MySQL 数据库

> [!NOTE]
> 这些服务需要进行其他配置, 然后才能将它们提供给用户, 并且默认情况下在安装 ASDK 时不可用。

## <a name="service-roadmap"></a>服务路线图
Azure Stack 会持续增加对其他 Azure 服务的支持。 若要了解 Azure Stack 随后会推出哪项功能，请参阅 [Azure Stack 路线图](https://azure.microsoft.com/roadmap/?tag=azure-stack)。 


## <a name="next-steps"></a>后续步骤
若要开始评估 Azure Stack，需要先[下载最新 ASDK](asdk-download.md) 并准备 ASDK 主机。 然后, 可以安装 ASDK 并登录到管理员和用户门户, 开始使用 Azure Stack。