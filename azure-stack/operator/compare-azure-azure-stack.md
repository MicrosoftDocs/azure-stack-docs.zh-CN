---
title: 比较 Azure Stack 与全球 Azure |Microsoft Docs
description: 了解 Microsoft 如何提供 Azure 和 Azure 生态系统中的服务的 Azure Stack 系列
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/03/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 20199e927498d0590ae32f68eb02b0a47c8c2b3e
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782286"
---
# <a name="differences-between-global-azure-azure-stack-and-azure-stack-hci"></a>全局 Azure，Azure Stack 和 Azure Stack HCI 之间的差异

Microsoft 提供了 Azure 和 Azure 生态系统中的服务的 Azure Stack 系列。 使用相同的应用程序模块、 自助服务门户和 Api 使用 Azure 资源管理器来提供基于云的功能，无论你的业务使用全球 Azure 还是本地资源。

本文介绍了全局 Azure、 Azure Stack 和 Azure Stack HCI 功能并提供常见方案建议，您可以将为你的组织的 Microsoft 基于云的服务交付的最佳选择。

![Azure 生态系统概述](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>全局 Azure

Microsoft Azure 是一个不断扩展的云服务集合，它可以帮助组织应对各种商业挑战。 它是可以自由地构建、 管理和使用你最喜爱的工具和框架的全球性的大规模网络上部署应用程序。

全球 Azure 提供了在全球的 54 区域中可用的 100 多个服务。 全球 Azure 服务的最新列表，请参阅[*区域的可用产品*](https://azure.microsoft.com/regions/services)。 在 Azure 中可用的服务是由类别，还列出按它们是否已公开发布或通过预览可用。

有关全球 Azure 服务的详细信息，请参阅[开始使用 Azure](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1)。

## <a name="azure-stack"></a>Azure Stack

Azure Stack 是云的 Azure 提供了灵活的扩展和创新计算到您的本地环境。 在本地部署，Azure Stack 可以用于提供未连接到 internet 连接到 internet （和 Azure） 或断开连接的环境中的 Azure 一致的服务。 Azure Stack 使用相同的基础技术作为全局 Azure，其中包含基础结构作为-服务 (IaaS)、 软件作为-服务 (SaaS) 的核心组件和可选平台-作为-服务 (PaaS) 功能，包括：

- 适用于 Windows 和 Linux 的 azure Vm
- Azure Web 应用和函数
- Azure 密钥保管库
- Azure 资源管理器
- Azure Marketplace
- 容器
- Azure IoT 中心与事件中心
- 管理工具 (计划、 产品/服务，RBAC，等等。)

Azure Stack 的 PaaS 功能是可选的因为 Azure Stack 不由 Microsoft 运行 — 它由我们的客户。 这意味着你可以提供所需向最终用户如果已准备好抽象底层基础结构和进程最终用户从任何 PaaS 服务。 但是，Azure Stack 包括几个可选 PaaS 服务提供程序包括应用服务、 SQL 数据库和 MySQL 数据库。 这些是传递作为资源提供程序，因此它们是多租户已准备好，通过使用标准 Azure Stack 更新的时间已更新，在 Azure Stack 门户中可见，并与 Azure Stack 良好集成。

除了上面所述的资源提供程序，还有其他 PaaS 服务可用并经过测试作为[Azure 资源管理器基于模板的解决方案](https://github.com/Azure/AzureStack-QuickStart-Templates)的 IaaS，在运行，但你作为 Azure Stack 操作员可以将其作为提供PaaS 服务添加到你的用户包括：

- Service Fabric
- Kubernetes 容器服务
- 以太坊区块链
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack"></a>Azure Stack 的示例用例：

- 金融建模
- 临床和声明的数据
- IoT 设备分析
- 零售 assortment 优化
- 供应链优化
- 工业 IoT
- 预防性维护
- 智能城市
- 公民参与度

详细了解在 Azure Stack[什么是 Azure Stack](azure-stack-overview.md)。

## <a name="azure-stack-hci"></a>Azure Stack HCI 

Azure Stack HCI 解决方案，可运行本地虚拟机，并使用超聚合基础结构 (HCI) 解决方案轻松地连接到 Azure。 使用一致的 Azure 本地服务生成和运行云应用程序，以满足法规或技术要求。 除了虚拟化应用程序本地运行，Azure Stack HCI，可替换和合并老化服务器基础结构和使用 Windows Admin Center 为云服务连接到 Azure。

Azure Stack HCI 提供经验证的 HCI 解决方案由 HYPER-V 和存储空间直通与 Windows Server 2019 软件定义数据中心 (SDDC) 提供支持。 Windows Admin Center 被用来管理和集成访问 Azure 服务，如：

- Azure 备份
- Azure Site Recovery
- Azure 监视和更新

有关更新的列表的 Azure 服务可以连接 Azure Stack HCI 以，请参阅[连接到 Azure 的混合服务的 Windows Server](https://docs.microsoft.com/windows-server/azure-hybrid-services/index)。

### <a name="example-use-cases-for-azure-stack-hci"></a>Azure Stack HCI 的示例用例
- 远程或分支办公系统
- 数据中心合并
- 虚拟桌面基础结构
- 业务关键基础结构
- 低成本存储
- 在云中的高可用性和灾难恢复
- SQL Server 等企业应用

请访问[Azure Stack HCI 网站](https://azure.microsoft.com/overview/azure-stack/hci/)若要查看 70 多个 Azure Stack HCI 解决方案当前可从 Microsoft 合作伙伴。

## <a name="next-steps"></a>后续步骤

[Azure Stack 管理基础知识](azure-stack-manage-basics.md)

[快速入门： 使用 Azure Stack 管理门户](azure-stack-manage-portals.md)
