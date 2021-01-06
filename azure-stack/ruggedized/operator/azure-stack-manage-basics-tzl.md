---
title: Azure Stack 集线器管理基础知识-耐用
titleSuffix: Azure Stack Hub
description: 了解 Azure Stack Hub 管理基础知识。
author: PatAltimore
ms.topic: article
ms.date: 06/16/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 06/16/2020
ms.openlocfilehash: f94a8ba25c595ac231beda9e235245d25541f334
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909678"
---
# <a name="azure-stack-hub-administration-basics---ruggedized"></a>Azure Stack 集线器管理基础知识-耐用

如果你不熟悉 Azure Stack Hub 管理，则需要了解几项事情。 本文概述 Azure Stack Hub 操作员角色，以及需要告知用户哪些事情才能帮助他们提高工作效率。

## <a name="understand-the-builds"></a>了解版本

如果使用 Azure Stack Hub 集成系统，则通过更新包分发更新版 Azure Stack Hub。 可以通过管理员门户中的“更新”磁贴导入并应用这些包。

## <a name="learn-about-available-services"></a>了解可用的服务

请注意可以提供给用户的服务。 Azure Stack Hub 支持部分 Azure 服务。 受支持服务的列表将不断更新。

### <a name="foundational-services"></a>基本服务

默认情况下，Azure Stack Hub 在部署时包括以下基本服务：

- 计算
- 存储
- 网络
- 密钥保管库

有了这些基本服务，在向用户提供基础结构即服务 (IaaS) 时就可以尽量减少配置。

### <a name="additional-services"></a>其他服务

我们支持下述额外的平台即服务 (PaaS) 服务：

- 应用服务
- Azure Functions
- SQL 和 MySQL 数据库
- kubernetes
- IoT 中心
- 事件中心

这些服务在提供给用户之前，需要进行额外的配置。 有关详细信息，请参阅 [Azure Stack Hub 操作员文档](https://docs.microsoft.com/azure-stack/operator/)中的“教程”和“操作指南” > “套餐服务”。  

### <a name="service-roadmap"></a>服务路线图

Azure Stack Hub 会持续增加对 Azure 服务的支持。 有关计划的路线图，请参阅 [Azure Stack Hub：Azure 的扩展](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409)白皮书。 也可留意 [Azure Stack Hub 博客文章](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview)中的新公告。

## <a name="what-account-should-i-use"></a>我应使用什么帐户?

管理 Azure Stack Hub 时，请注意帐户方面的几个事项。 在使用 Windows Server Active Directory 联合身份验证服务 (AD FS) 而不是 Azure Active Directory (Azure AD) 作为标识提供者的部署中尤其如此。

| **帐户** | **Azure** | **AD FS** |
|---|---|---|
| 本地管理员 (.\Administrator) |   |
| Azure AD 全局管理员 | 安装期间使用。 <br> 默认提供程序的所有者 | 不适用。 |
| 用于已扩展存储的帐户|   |   |
||

## <a name="what-tools-do-i-use-to-manage"></a>使用哪些工具进行管理？

可以使用[管理员门户](../../operator/azure-stack-manage-portals.md)或 PowerShell 来管理 Azure Stack Hub。 若要了解基本概念，最简单的方式是使用门户。 若要使用 PowerShell，则需完成准备步骤。 开始之前，请先熟悉如何在 Azure Stack Hub 上使用 PowerShell。 有关详细信息，请参阅 [Azure Stack Hub 上的 PowerShell 入门](../../user/azure-stack-powershell-overview.md)。

Azure Stack Hub 使用 Azure 资源管理器作为其基础的部署、管理和组织机制。 若要管理 Azure Stack Hub 并帮助支持用户，应了解资源管理器。 请参阅 [Azure 资源管理器入门](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)白皮书。

## <a name="your-typical-responsibilities"></a>典型责任

用户需要使用服务， 从其角度来看，你的主要角色是向他们提供这些服务。 必须通过创建计划、套餐和配额来确定要提供的具体服务并将这些服务提供给用户。 有关详细信息，请参阅[概述：如何在 Azure Stack Hub 中提供服务](../../operator/service-plan-offer-subscription-overview.md)。

还需向 [Azure Stack Hub 市场](../../operator/azure-stack-marketplace.md)添加项。 最简单的方式是[将市场项从 Azure 下载到 Azure Stack Hub](../../operator/azure-stack-download-azure-marketplace-item.md)。

如需测试计划、套餐和服务，可使用[用户门户](../../operator/azure-stack-manage-portals.md)而不是管理员门户。

除了提供服务，还必须执行操作员的常规任务，使 Azure Stack Hub 始终能够启动并运行。 这些职责包括以下任务：

- 添加用于 [Azure AD](../../operator/azure-stack-add-new-user-aad.md) 部署的用户帐户。
- [使用基于角色的访问控制设置访问权限](../../operator/azure-stack-manage-permissions.md)。 （此任务并不限于管理员。）
- [监视基础结构运行状况](../../operator/azure-stack-monitor-health.md)。
- 管理[网络](../../operator/azure-stack-viewing-public-ip-address-consumption.md)和[存储](../../operator/azure-stack-manage-storage-accounts.md)资源。
- [启动和停止 Azure Stack Hub](../../operator/azure-stack-start-and-stop.md)。
- [运行扩展的存储](../../user/azure-stack-network-howto-extend-datacenter.md)。
- [管理 IoT 中心](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)。
- [管理事件中心](https://docs.microsoft.com/azure-stack/operator/event-hubs-rp-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl)。
- [管理应用服务](https://docs.microsoft.com/azure-stack/operator/azure-stack-app-service-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl)。
- 更换损坏的硬件。 下面是[可替换部件](../../operator/azure-stack-replace-component.md)的列表。
- [获取支持](https://docs.microsoft.com/azure-stack/operator/azure-stack-help-and-support-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl)。

## <a name="operator-tasks"></a>操作员任务

下面是一个操作员每日、每周和每月任务的列表：

# <a name="daily"></a>[每日](#tab/daily)

1. 检查警报。
2. 检查备份状态。
3. 更新 Defender 签名（断开连接的系统）。
4. 在 OneFS 中检查 Isilon 系统运行状况和事件。
5. 检查 Isilon 容量。

# <a name="weekly"></a>[每周](#tab/weekly)

1. 检查容量。
2. 在 Avocent 连接中运行 `isi status –verbose`。

# <a name="monthly"></a>[每月](#tab/monthly)

1. 应用每月更新包（Microsoft 和 OEM）。
2. 使用 ASDK 验证备份。
3. 管理 Azure Stack Hub 市场（保持最新）。
4. 更新交换机固件和 Avocent。
5. 回收存储容量。

# <a name="ondemand"></a>[按需](#tab/ondemand)

1. 机密轮换。
2. 创建和更新套餐、计划和配额。
3. 应用修补程序包。
4. 应用修补程序包。
5. 扩展容量（节点和 IPSpace）。
6. 在 Avocent 连接中运行 `isi status –verbose`。
7. 还原存储帐户。
8. 停止系统。
9. 诊断日志收集。

---

## <a name="what-to-tell-your-users"></a>需要告知用户的内容

需要让用户知道如何使用 Azure Stack Hub 中的服务、如何连接到环境，以及如何订阅套餐。 除了根据需要提供用户自定义文档，还可以引导用户访问 [Azure Stack Hub 用户文档](https://docs.microsoft.com/azure-stack/user/)。

### <a name="understand-how-to-work-with-services-in-azure-stack-hub"></a>了解如何使用 Azure Stack Hub 中的服务

在 Azure Stack Hub 中使用服务和开发应用之前，用户必须了解某些信息。 例如，必须了解特定的 PowerShell 和 API 版本要求。 另外，Azure 中的服务与 Azure Stack Hub 中的相应服务存在一些功能差异。 请确保用户参阅以下文章：

- [使用服务和生成应用时 Azure Stack Hub 与 Azure 之间的差异](../../user/azure-stack-considerations.md)
- [Azure Stack Hub VM 功能](../../user/azure-stack-vm-considerations.md)
- [Azure Stack Hub 存储：差异和注意事项](../../user/azure-stack-acs-differences.md)

这些文章汇总了 Azure 和 Azure Stack Hub 中的服务差异， 是对全球 Azure 文档中 Azure 服务的可用信息的补充。

### <a name="connect-to-azure-stack-hub-as-a-user"></a>以用户身份连接到 Azure Stack Hub

用户需要知道如何[访问用户门户](../../user/azure-stack-use-portal.md)，或者如何通过 PowerShell 进行连接。 在集成系统环境中，用户门户地址随部署而变。 需向用户提供正确的 URL。

如果使用 PowerShell，用户可能需要先注册资源提供程序，然后才能使用服务。 资源提供程序用于管理服务。 例如，网络资源提供程序用于管理虚拟网络、网络接口和负载均衡器之类的资源。 用户必须[安装](../../operator/azure-stack-powershell-install.md) PowerShell，[下载](../../operator/azure-stack-powershell-download.md)更多的模块，然后[配置](../../user/azure-stack-powershell-configure-user.md) PowerShell（包括资源提供程序注册）。

### <a name="subscribe-to-an-offer"></a>订阅产品

在使用服务之前，用户必须[订阅套餐](../../operator/azure-stack-subscribe-plan-provision-vm.md)，该产品/服务是你以操作员身份创建的。

## <a name="where-to-get-support"></a>从何处获取支持

若要查找旧版 Azure Stack Hub（1905 之前）的支持信息，请参阅 [Azure Stack Hub 服务策略](../../operator/azure-stack-servicing-policy.md)。

对于集成系统，Microsoft 和原始设备制造商之间存在协调的升级和解决过程， (OEM) 硬件合作伙伴。

如果存在云服务问题，请通过 Microsoft 客户支持服务 (CSS) 寻求支持。 若要创建支持请求，请选择管理员门户右上角的帮助和支持图标（问号）。 接着选择“帮助 + 支持”，然后选择“支持”部分下面的“新建支持请求”。  

如果部署、修补和更新存在问题，硬件 (包括现场可更换单元) 或硬件品牌软件（如硬件生命周期主机上运行的软件），请首先联系 OEM 硬件供应商。

至于其他问题，请联系 Microsoft CSS。

## <a name="next-steps"></a>后续步骤

- [Azure Stack Hub 中的区域管理](../../operator/azure-stack-region-management.md)
