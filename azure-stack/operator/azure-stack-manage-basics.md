---
title: Azure Stack 管理基础知识 | Microsoft Docs
description: 了解 Azure Stack 管理的基础知识。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2019
ms.author: justinha
ms.lastreviewed: 05/29/2019
ms.openlocfilehash: f9b9d6d1474c22c8e31d24ae08faf1aac6d5e9c1
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283483"
---
# <a name="azure-stack-administration-basics"></a>Azure Stack 管理基础知识

如果你不熟悉 Azure Stack 管理，则需要了解几项事情。 本文概述了你作为 Azure Stack 操作员的角色，以及你需要告诉用户如何帮助他们提高工作效率。

## <a name="understand-the-builds"></a>了解版本

### <a name="integrated-systems"></a>集成系统

如果你使用 Azure Stack 集成系统，请更新包会分发更新版的 Azure Stack。 可以通过管理员门户中的“更新”磁贴导入并应用这些包。
 
### <a name="development-kit"></a>开发工具包

如果你使用的是 Azure Stack 开发工具包（ASDK），请查看[什么是 Azure Stack？](../asdk/asdk-what-is.md) ，了解 ASDK 的用途和限制。 你可以使用 ASDK 作为*沙盒*，你可以在其中评估 Azure Stack，并在非生产环境中开发和测试你的应用程序。 有关部署信息，请参阅 [Azure Stack 开发工具包部署](../asdk/asdk-install.md)。

正如 Azure 一样，我们的创新速度很快。 我们会定期发布新版本。 如果你正在运行 ASDK 并想要更新到最新版本，则必须[重新部署 Azure Stack](../asdk/asdk-redeploy.md)。 不能应用更新包。 此过程需要一定的时间，但好处是可以尝试最新功能。 我们网站上的 ASDK 文档反映了最新的发行版。

## <a name="learn-about-available-services"></a>了解可用的服务

你需要了解可以向用户提供哪些服务。 Azure Stack 支持部分 Azure 服务。 受支持服务的列表将不断更新。

**基本服务**

默认情况下，Azure Stack 在部署时包括以下“基本服务”：

- 计算
- 存储
- 网络
- Key Vault

有了这些基本服务，在向用户提供基础结构即服务 (IaaS) 时就可以尽量减少配置。

**其他服务**

目前，我们支持下述额外的平台即服务 (PaaS) 服务：

- 应用服务
- Azure Functions
- SQL 和 MySQL 数据库
- Kubernetes （预览版）

这些服务在提供给用户之前，需要进行额外的配置。 有关详细信息，请参阅 Azure Stack 操作员文档的“教程”和“操作指南\产品服务”部分。

**服务路线图**

Azure Stack 会持续增加对 Azure 服务的支持。 有关计划的路线图，请参阅 [Azure Stack：Azure 的扩展](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409)白皮书。 也可留意 [Azure Stack 博客文章](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview)中的新公告。

## <a name="what-account-should-i-use"></a>我应使用什么帐户?
管理 Azure Stack 时，请注意帐户方面的几个事项。 尤其是在使用 Windows Server Active Directory 联合身份验证服务 (AD FS) 而不是 Azure Active Directory (Azure AD) 作为标识提供者的部署中。 以下帐户注意事项同时适用于 Azure Stack 集成系统和 ASDK 部署：


|帐户|Azure AD|AD FS|
|-----|-----|-----|
|本地管理员 (.\Administrator)|ASDK 主机管理员。|ASDK 主机管理员。|
|AzureStack\AzureStackAdmin|ASDK 主机管理员。<br><br>可用于登录到 Azure Stack 管理员门户。<br><br>查看和管理 Service Fabric 环的访问。|ASDK 主机管理员。<br><br>无权访问 Azure Stack 管理员门户。<br><br>查看和管理 Service Fabric 环的访问。<br><br>不再是默认提供程序订阅（DPS）的所有者。|
|AzureStack\CloudAdmin|可以访问和运行特权终结点中的允许命令。|可以访问和运行特权终结点中的允许命令。<br><br>无法登录到 ASDK 主机。<br><br>默认提供程序订阅（DPS）的所有者。|
|Azure AD 全局管理员|在安装过程中使用。<br><br>默认提供程序订阅（DPS）的所有者。|不适用。|
|

## <a name="what-tools-do-i-use-to-manage"></a>使用哪些工具进行管理？
 
可以使用[管理员门户](azure-stack-manage-portals.md)或 PowerShell 来管理 Azure Stack。 若要了解基本概念，最简单的方式是使用门户。 若要使用 PowerShell，则需完成准备步骤。 开始之前，请先熟悉如何在 Azure Stack 上使用 PowerShell。 有关详细信息，请参阅 [Azure Stack 上的 PowerShell 入门](../user/azure-stack-powershell-overview.md)。

Azure Stack 使用 Azure 资源管理器作为其基础的部署、管理和组织机制。 若要管理 Azure Stack 并帮助支持用户，可了解资源管理器。 请参阅 [Azure 资源管理器入门](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)白皮书。

## <a name="your-typical-responsibilities"></a>典型责任

用户需要使用服务， 从其角度来看，你的主要角色是向他们提供这些服务。 必须通过创建计划、套餐和配额来确定要提供的具体服务并将这些服务提供给用户。 有关详细信息，请参阅[概述：如何在 Azure Stack 中提供服务](service-plan-offer-subscription-overview.md)。 

还需要将项目添加到[Azure Stack Marketplace](azure-stack-marketplace.md)。 最简单的方式是[将市场项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md)。

> [!NOTE]
> 如需测试计划、套餐和服务，可使用[用户门户](azure-stack-manage-portals.md)而不是管理员门户。

除了提供服务，还必须执行操作员的常规任务，使 Azure Stack 始终能够启动并运行。 这些职责包括以下任务：

- 添加用户帐户（用于[Azure AD](azure-stack-add-new-user-aad.md)部署或[AD FS](azure-stack-add-users-adfs.md)部署）。
- [分配基于角色的访问控制（RBAC）角色](azure-stack-manage-permissions.md)（此任务并不限于管理员。）
- [监视基础结构运行状况](azure-stack-monitor-health.md)。
- 管理[网络](azure-stack-viewing-public-ip-address-consumption.md)和[存储](azure-stack-manage-storage-accounts.md)资源。
- 更换损坏的硬件。 例如，[替换出现故障的磁盘](azure-stack-replace-disk.md)。

## <a name="what-to-tell-your-users"></a>需要告知用户的内容

需要让用户知道如何使用 Azure Stack 中的服务、如何连接到环境，以及如何订阅套餐。 除了任何可能需要提供用户的自定义文档外，还可以将用户定向到[Azure Stack 的用户文档](https://docs.microsoft.com/en-us/azure-stack/user/)。

**了解如何使用 Azure Stack 中的服务**

在 Azure Stack 中使用服务和开发应用之前，用户必须了解某些信息。 例如，必须了解特定的 PowerShell 和 API 版本要求。 另外，Azure 中的服务与 Azure Stack 中的相应服务存在一些功能差异。 请确保用户参阅以下文章：

- [重要注意事项：使用 Azure Stack 的服务或构建适用于 Azure Stack 的应用](../user/azure-stack-considerations.md)
- [Azure Stack 中虚拟机的注意事项](../user/azure-stack-vm-considerations.md)
- [存储：差异和注意事项](../user/azure-stack-acs-differences.md)

这些文章汇总了 Azure 和 Azure Stack 中的服务差异， 是对全球 Azure 文档中 Azure 服务的可用信息的补充。

**以用户身份连接到 Azure Stack**

在 ASDK 环境中，如果用户未使用远程桌面连接到 ASDK 主机，他们可以配置虚拟专用网络 (VPN) 连接来连接 Azure Stack。 请参阅[连接到 Azure Stack](../asdk/asdk-connect.md)。

用户需要知道如何[访问用户门户](../user/azure-stack-use-portal.md)，或者如何通过 PowerShell 进行连接。 在集成系统环境中，用户门户地址随部署而变。 需向用户提供正确的 URL。

如果使用 PowerShell，用户可能需要先注册资源提供程序，然后才能使用服务。 资源提供程序管理服务。 例如，网络资源提供程序管理虚拟网络、网络接口和负载均衡器等资源。 用户必须[安装](azure-stack-powershell-install.md) PowerShell，[下载](azure-stack-powershell-download.md)更多的模块，然后[配置](../user/azure-stack-powershell-configure-user.md) PowerShell（包括资源提供程序注册）。

**订阅套餐**

在使用服务之前，用户必须[订阅套餐](azure-stack-subscribe-plan-provision-vm.md)，该产品/服务是你以操作员身份创建的。

## <a name="where-to-get-support"></a>从何处获取支持

> [!Note]  
> 若要查找旧版 Azure Stack（1905 之前）的支持信息，请参阅[旧版 Azure Stack（1905 之前）的帮助和支持](azure-stack-servicing-policy.md)。

### <a name="integrated-systems"></a>集成系统

对于集成系统，Microsoft 和我们的原始设备制造商（OEM）硬件合作伙伴之间存在协调的升级和解决过程。

如果有云服务问题，通过 Microsoft 客户支持服务（CSS）提供支持。 若要打开支持请求，请在管理员门户的右上角选择 "帮助和支持" 图标（问号），选择 "**帮助 + 支持**"，然后在 "**支持**" 部分下选择 "**新建支持请求**"。

如果部署、修补和更新、硬件（包括现场可更换部件）或任何硬件品牌软件（如硬件生命周期主机上运行的软件）存在问题，请首先联系你的 OEM 硬件供应商。

至于其他问题，请联系 Microsoft CSS。

### <a name="azure-stack-development-kit-asdk"></a>Azure Stack 开发工具包 (ASDK)

对于 ASDK，你可以在[Microsoft 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提问与支持相关的问题。 若要访问论坛，请在管理员门户的右上角选择 "帮助和支持" 图标（问号），然后选择 "**帮助 + 支持**"，然后在 "**支持**" 部分下选择 " **MSDN 论坛**"。 我们会定期关注这些论坛。 由于 ASDK 是一个评估环境，因此没有通过 Microsoft CSS 提供的官方支持。

## <a name="next-steps"></a>后续步骤

[Azure Stack 中的区域管理](azure-stack-region-management.md)