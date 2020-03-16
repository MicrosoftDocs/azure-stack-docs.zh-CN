---
title: Azure Stack 集线器管理基础知识
titleSuffix: Azure Stack Hub
description: 了解 Azure Stack 集线器管理的基础知识。
author: nicoalba
ms.topic: article
ms.date: 03/02/2020
ms.author: v-nialba
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 8f56dafbcc27e3ff4de9adcfbf5de27dea115bb3
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79293952"
---
# <a name="azure-stack-hub-administration-basics"></a>Azure Stack 集线器管理基础知识

如果你不熟悉 Azure Stack 集线器管理，则需要了解几个问题。 本文概述了你作为 Azure Stack 中心操作员的角色，以及你需要告诉用户如何帮助他们提高工作效率。

## <a name="understand-the-builds"></a>了解生成

如果使用的是 Azure Stack 集线器集成系统，则 Azure Stack 中心的更新版本通过更新包分发。 可以使用管理员门户中的 " **更新** " 磁贴来导入和应用这些包。

## <a name="learn-about-available-services"></a>了解可用的服务

请注意可以提供给用户的服务。 Azure Stack 中心支持部分 Azure 服务。 受支持服务的列表将继续发展。

### <a name="foundational-services"></a>基础服务

默认情况下，在部署 Azure Stack 中心时，Azure Stack 中心包含以下基础服务：

- 计算
- 存储
- 网络
- Key Vault

使用这些基础服务，可以使用最小配置向用户提供基础结构即服务（IaaS）。

### <a name="additional-services"></a>其他服务

我们支持以下附加平台即服务（PaaS）服务：

- 应用服务
- Azure Functions
- SQL 和 MySQL 数据库
- Kubernetes
- IoT 中心
- 事件中心

这些服务需要进行其他配置，然后才能将它们提供给用户。 有关详细信息，请参阅**教程**和操作**方法指南** > 在我们的[Azure Stack 中心运营商文档](https://docs.microsoft.com/azure-stack/operator/)中**提供服务**。

### <a name="service-roadmap"></a>服务路线图

Azure Stack 中心将继续添加对 Azure 服务的支持。 有关预计的路线图，请参阅 [Azure Stack 中心：Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409)的扩展  白皮书。 你还可以监视 [Azure Stack 集线器博客文章](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview)中的新公告。

## <a name="what-account-should-i-use"></a>我应该使用哪个帐户？

管理 Azure Stack 中心时，需要注意几个帐户注意事项。 在使用 Windows Server Active Directory 联合身份验证服务（AD FS）作为标识提供程序而不是 Azure Active Directory （Azure AD）的部署中尤其如此。

| **帐户** | **Azure** | **AD FS** |
|---|---|---|
| 本地管理员（.\Administrator） |   |
| Azure AD 全局管理员 | 在安装过程中使用。 <br> 默认提供程序的所有者 | 不适用。 |
| 用于扩展存储的帐户|   |   |
||

## <a name="what-tools-do-i-use-to-manage"></a>使用哪些工具来管理？

可以使用 [管理员门户](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-portals?view=azs-2002) 或 PowerShell 来管理 Azure Stack 中心。 了解基本概念的最简单方法是通过门户。 如果要使用 PowerShell，有一些准备步骤。 在开始之前，你可能需要熟悉如何在 Azure Stack 集线器上使用 PowerShell。 有关详细信息，请参阅 [Azure Stack 集线器上的 PowerShell 入门](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-powershell-overview?view=azs-2002)。

Azure Stack 中心使用 Azure 资源管理器作为其基础部署、管理和组织机制。 如果要管理 Azure Stack 中心和帮助支持用户，应了解资源管理器。 请参阅 [Azure 资源管理器](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) 白皮书中的入门。

## <a name="your-typical-responsibilities"></a>你的典型职责

你的用户希望使用服务。 从其角度来看，你的主要角色是使这些服务可供他们使用。 通过创建计划、产品/服务和配额，确定要提供哪些服务并使这些服务可用。 有关详细信息，请参阅 [在 Azure Stack Hub 中提供服务概述](https://review.docs.microsoft.com/en-us/azure-stack/operator/service-plan-offer-subscription-overview?view=azs-2002)。

还需要将项目添加到 [Azure Stack 中心市场](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-marketplace?view=azs-2002)。 最简单的方法是将 [marketplace 项从 Azure 下载到 Azure Stack 中心](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-2002)。

如果要测试计划、产品/服务和服务，可以使用 [用户门户](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-portals?view=azs-2002);不是管理员门户。

除了提供服务外，还必须执行操作员的常规职责，才能保持 Azure Stack 集线器正常运行。 这些职责包括以下任务：

- 添加 [Azure AD](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-add-new-user-aad?view=azs-2002) 部署的用户帐户。
- [使用基于角色的访问控制设置访问权限](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-permissions?view=azs-2002)。 （此任务并不限于管理员。）
- [监视基础结构运行状况](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-monitor-health?view=azs-2002)。
- 管理 [网络](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-viewing-public-ip-address-consumption?view=azs-2002) 和 [存储](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-manage-storage-accounts?view=azs-2002) 资源。
- [启动和停止 Azure Stack 集线器](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-start-and-stop?view=azs-2002&branch=release-tzl)。
- [操作扩展存储](https://review.docs.microsoft.com/en-us/azure-stack/tdc/extended-storage-operator-guide?view=azs-2002&branch=release-tzl)。
- [管理 IoT 中心](https://review.docs.microsoft.com/en-us/azure-stack/operator/iot-hub-rp-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&.bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl)。
- [管理事件中心](https://review.docs.microsoft.com/en-us/azure-stack/operator/event-hubs-rp-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl)。
- [管理应用服务](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-app-service-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl)。
- 更换损坏的硬件。 下面是可[替换部件](https://review.docs.microsoft.com/en-us/azure-stack/tdc/cru-replaceable-parts?view=azs-2002&branch=release-tzl)的列表。
- [获取支持](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-help-and-support-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl)。

## <a name="operator-tasks"></a>操作员任务

下面是一个操作员的每日、每周和每月任务的列表：

# <a name="daily"></a>[日历](#tab/daily)

1. 检查警报。
2. 检查备份状态。
3. 更新 Defender 签名（断开连接的系统）。
4. 在 OneFS 中检查 Isilon 系统运行状况和事件。
5. 检查 Isilon 容量。

# <a name="weekly"></a>[每周](#tab/weekly)

1. 检查容量。
2. 在 Avocent 连接中运行 `isi status –verbose`。

# <a name="monthly"></a>[次](#tab/monthly)

1. 应用每月更新包（Microsoft & OEM）。
2. 使用 ASDK 验证备份。
3. 管理 Azure Stack 中心市场（保持最新）。
4. & Avocent 更新交换机固件。
5. 回收存储容量。

# <a name="ondemand"></a>[OnDemand](#tab/ondemand)

1. 机密旋转。
2. 创建和更新产品/服务、计划和配额。
3. 应用修补程序包。
4. 应用修补程序包。
5. 展开容量（节点 & IPSpace）。
6. 在 Avocent 连接中运行 `isi status –verbose`。
7. 还原存储帐户。
8. 停止系统。
9. 诊断日志收集。

---

## <a name="what-to-tell-your-users"></a>告诉用户的内容

你将需要让用户了解如何在 Azure Stack Hub 中使用服务、如何连接到环境，以及如何订阅产品/服务。 除了任何可能需要提供用户的自定义文档外，还可以将用户定向到 [Azure Stack 集线器用户文档](https://review.docs.microsoft.com/en-us/azure-stack/user/)。

### <a name="understand-how-to-work-with-services-in-azure-stack-hub"></a>了解如何在 Azure Stack 中心使用服务

在 Azure Stack 集线器中使用服务和构建应用之前，用户必须先了解相关信息。 例如，有特定的 PowerShell 和 API 版本要求。 此外，Azure 中的服务与 Azure Stack 集线器中的等效服务之间存在一些功能差异。 确保你的用户查看以下文章：

- [使用服务和构建应用时 Azure Stack 中心与 Azure 之间的差异](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-considerations?view=azs-2002)
- [Azure Stack 中心 VM 功能](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-vm-considerations?view=azs-2002)
- Azure Stack 中心存储 [：差异和注意事项](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-acs-differences?view=azs-2002)

这些文章中的信息总结了 Azure 中服务与 Azure Stack 中心之间的差异。 它对 Azure 服务在全球 Azure 文档中提供的信息进行了补充。

### <a name="connect-to-azure-stack-hub-as-a-user"></a>以用户身份连接到 Azure Stack 中心

用户需要知道如何 [访问用户门户](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-use-portal?view=azs-2002) 或如何通过 PowerShell 进行连接。 在集成系统环境中，每个部署的用户门户地址各不相同。 需要为用户提供正确的 URL。

如果使用 PowerShell，用户可能需要先注册资源提供程序，然后才能使用服务。 资源提供程序管理服务。 例如，网络资源提供程序管理虚拟网络、网络接口和负载均衡器等资源。 它们必须 [安装](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-powershell-install?view=azs-2002) powershell， [下载](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-powershell-download?view=azs-2002) 其他模块，并[配置](https://review.docs.microsoft.com/en-us/azure-stack/user/azure-stack-powershell-configure-user?view=azs-2002) PowerShell （包括资源提供程序注册）。

### <a name="subscribe-to-an-offer"></a>订阅产品

用户必须订阅作为操作员创建的 [产品](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-2002)/服务 ，才能使用服务。

## <a name="where-to-get-support"></a>获取支持的位置

若要查找 Azure Stack 集线器早期版本的支持信息（1905之前），请参阅 [Azure Stack 中心服务策略](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-servicing-policy?view=azs-2002)。

对于集成系统，Microsoft 和我们的原始设备制造商（OEM）硬件合作伙伴之间存在协调的升级和解决过程。

如果有云服务问题，通过 Microsoft 客户支持服务（CSS）提供支持。 若要打开支持请求，请在管理员门户的右上角选择 "帮助和支持" 图标（问号）。 然后，在 " **支持** " 部分下选择 " **帮助 + 支持**"，然后选择 "**新建支持 请求**"。

如果部署、修补和更新、硬件（包括现场可更换部件）或任何硬件品牌软件（如硬件生命周期主机上运行的软件）存在问题，请首先联系你的 OEM 硬件供应商。

对于其他任何内容，请联系 Microsoft CSS。

## <a name="next-steps"></a>后续步骤

- [Azure Stack 中心的区域管理](https://review.docs.microsoft.com/en-us/azure-stack/operator/azure-stack-region-management?view=azs-2002)
