---
title: Azure Stack Hub 管理基础知识
description: 了解 Azure Stack Hub 管理基础知识。
author: justinha
ms.topic: article
ms.date: 06/16/2020
ms.author: justinha
ms.lastreviewed: 06/16/2020
ms.openlocfilehash: b277eb9dcb1ac66af870db68c919e711aadc1ac7
ms.sourcegitcommit: b50dd116d6d1f89d42bd35ad0f85bb25c5192921
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "94543504"
---
# <a name="azure-stack-hub-administration-basics"></a>Azure Stack Hub 管理基础知识

如果你不熟悉 Azure Stack Hub 管理，则需要了解几项事情。 本文概述 Azure Stack Hub 操作员角色，以及需要告知用户哪些事情才能帮助他们提高工作效率。

## <a name="understand-the-builds"></a>了解版本

### <a name="integrated-systems"></a>集成系统

如果你使用 Azure Stack Hub 集成系统，请更新包会分发更新版的 Azure Stack。 可以通过管理员门户中的“更新”磁贴导入并应用这些包。
 
### <a name="development-kit"></a>开发工具包

如果你使用 Azure Stack 开发工具包 (ASDK)，请查看[什么是 Azure Stack Hub？](../asdk/asdk-what-is.md)来了解 ASDK 的用途和其限制。 可将 ASDK 用作沙盒，在其中对 Azure Stack Hub 进行评估，并在非生产环境中开发和测试应用。 有关部署信息，请参阅 [Azure Stack 开发工具包部署](../asdk/asdk-install.md)。

正如 Azure 一样，我们的创新速度很快。 我们会定期发布新版本。 如果你正在运行 ASDK 并想要更新到最新版本，则必须[重新部署 Azure Stack Hub](../asdk/asdk-redeploy.md)。 不能应用更新包。 此过程需要一定的时间，但好处是可以尝试最新功能。 我们网站上的 ASDK 文档反映了最新的发行版。

## <a name="learn-about-available-services"></a>了解可用的服务

你需要了解可以向用户提供哪些服务。 Azure Stack Hub 支持部分 Azure 服务。 受支持服务的列表将不断更新。

**基本服务**

默认情况下，Azure Stack Hub 在部署时包括以下“基本服务”：

- 计算
- 存储
- 网络
- 密钥保管库

有了这些基本服务，在向用户提供基础结构即服务 (IaaS) 时就可以尽量减少配置。

**其他服务**

目前，我们支持下述额外的平台即服务 (PaaS) 服务：

- 应用服务
- Azure Functions
- SQL 和 MySQL 数据库
- Kubernetes（预览版）

这些服务在提供给用户之前，需要进行额外的配置。 有关详细信息，请参阅 Azure Stack Hub 操作员文档的“教程”和“操作指南\套餐服务”部分。

**服务路线图**

Azure Stack Hub 会持续增加对 Azure 服务的支持。 有关计划的路线图，请参阅 [Azure Stack Hub：Azure 的扩展](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409)白皮书。 也可留意 [Azure Stack Hub 博客文章](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview)中的新公告。

## <a name="what-account-should-i-use"></a>我应使用什么帐户?

管理 Azure Stack Hub 时，请注意帐户方面的几个事项。 尤其是在使用 Windows Server Active Directory 联合身份验证服务 (AD FS) 而不是 Azure Active Directory (Azure AD) 作为标识提供者的部署中。 以下帐户注意事项同时适用于 Azure Stack Hub 集成系统和 ASDK 部署：

|帐户|Azure AD|AD FS|
|-----|-----|-----|
|本地管理员 (.\Administrator)|ASDK 主机管理员。|ASDK 主机管理员。|
|AzureStack\AzureStackAdmin|ASDK 主机管理员。<br><br>可用于登录到 Azure Stack Hub 管理员门户。<br><br>拥有查看和管理 Service Fabric 环的访问权限。|ASDK 主机管理员。<br><br>无权访问 Azure Stack Hub 管理员门户。<br><br>拥有查看和管理 Service Fabric 环的访问权限。<br><br>不再是默认提供程序订阅 (DPS) 的所有者。|
|AzureStack\CloudAdmin|可在特权终结点中访问和运行允许的命令。|可在特权终结点中访问和运行允许的命令。<br><br>无法登录到 ASDK 主机。<br><br>默认提供程序订阅 (DPS) 的所有者。|
|Azure AD 全局管理员|安装期间使用。<br><br>默认提供程序订阅 (DPS) 的所有者。|不适用。|
|

## <a name="what-tools-do-i-use-to-manage"></a>使用哪些工具进行管理？
 
可以使用[管理员门户](azure-stack-manage-portals.md)或 PowerShell 来管理 Azure Stack Hub。 若要了解基本概念，最简单的方式是使用门户。 若要使用 PowerShell，则需完成准备步骤。 开始之前，请先熟悉如何在 Azure Stack Hub 上使用 PowerShell。 有关详细信息，请参阅 [Azure Stack Hub 上的 PowerShell 入门](../user/azure-stack-powershell-overview.md)。

Azure Stack Hub 使用 Azure 资源管理器作为其基础的部署、管理和组织机制。 若要管理 Azure Stack Hub 并帮助支持用户，可了解资源管理器。 请参阅 [Azure 资源管理器入门](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)白皮书。

## <a name="your-typical-responsibilities"></a>典型责任

用户需要使用服务， 从其角度来看，你的主要角色是向他们提供这些服务。 必须通过创建计划、套餐和配额来确定要提供的具体服务并将这些服务提供给用户。 有关详细信息，请参阅[概述：如何在 Azure Stack Hub 中提供服务](service-plan-offer-subscription-overview.md)。 

还需向 [Azure Stack Hub 市场](azure-stack-marketplace.md)添加项。 最简单的方式是[将市场项从 Azure 下载到 Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)。

> [!NOTE]
> 如需测试计划、套餐和服务，可使用[用户门户](azure-stack-manage-portals.md)而不是管理员门户。

除了提供服务，还必须执行操作员的常规任务，使 Azure Stack Hub 始终能够启动并运行。 这些职责包括以下任务：

- 添加用户帐户（针对 [Azure AD](azure-stack-add-new-user-aad.md) 部署或 [AD FS](azure-stack-add-users-adfs.md) 部署）。
- [分配基于角色的访问控制 (RBAC) 角色](azure-stack-manage-permissions.md)（此任务不仅限于管理员。）
- [监视基础结构运行状况](azure-stack-monitor-health.md)。
- 管理[网络](azure-stack-viewing-public-ip-address-consumption.md)和[存储](azure-stack-manage-storage-accounts.md)资源。
- 更换损坏的硬件。 例如，[更换有故障的磁盘](azure-stack-replace-disk.md)。

## <a name="what-to-tell-your-users"></a>需要告知用户的内容

需要让用户知道如何使用 Azure Stack Hub 中的服务、如何连接到环境，以及如何订阅套餐。 除了根据需要提供用户自定义文档，还可以引导用户访问 [Azure Stack Hub 用户文档](../user/index.yml)。

**了解如何使用 Azure Stack Hub 中的服务**

在 Azure Stack Hub 中使用服务和开发应用之前，用户必须了解某些信息。 例如，必须了解特定的 PowerShell 和 API 版本要求。 另外，Azure 中的服务与 Azure Stack Hub 中的相应服务存在一些功能差异。 请确保用户参阅以下文章：

- [重要注意事项：使用 Azure Stack Hub 的服务或构建适用于 Azure Stack Hub 的应用](../user/azure-stack-considerations.md)
- [Azure Stack Hub 中虚拟机的注意事项](../user/azure-stack-vm-considerations.md)
- [存储：差异和注意事项](../user/azure-stack-acs-differences.md)

这些文章汇总了 Azure 和 Azure Stack Hub 中的服务差异， 是对全球 Azure 文档中 Azure 服务的可用信息的补充。

**以用户身份连接到 Azure Stack Hub**

在 ASDK 环境中，如果用户未使用远程桌面连接到 ASDK 主机，他们可以配置虚拟专用网络 (VPN) 连接来连接 Azure Stack Hub。 请参阅[连接到 Azure Stack Hub](../asdk/asdk-connect.md)。

用户需要知道如何[访问用户门户](../user/azure-stack-use-portal.md)，或者如何通过 PowerShell 进行连接。 在集成系统环境中，用户门户地址随部署而变。 需向用户提供正确的 URL。

如果使用 PowerShell，用户可能需要先注册资源提供程序，然后才能使用服务。 资源提供程序用于管理服务。 例如，网络资源提供程序用于管理虚拟网络、网络接口和负载均衡器之类的资源。 用户必须[安装](powershell-install-az-module.md) PowerShell，[下载](azure-stack-powershell-download.md)更多的模块，然后[配置](../user/azure-stack-powershell-configure-user.md) PowerShell（包括资源提供程序注册）。

**订阅套餐**

在使用服务之前，用户必须[订阅套餐](azure-stack-subscribe-plan-provision-vm.md)，该产品/服务是你以操作员身份创建的。

## <a name="where-to-get-support"></a>从何处获取支持

> [!Note]  
> 若要查找旧版 Azure Stack Hub（1905 之前）的支持信息，请参阅[旧版 Azure Stack Hub（1905 之前）的帮助和支持](azure-stack-servicing-policy.md)。

### <a name="integrated-systems"></a>集成系统

对于集成系统，Microsoft 和原始设备制造商之间存在协调的升级和解决过程， (OEM) 硬件合作伙伴。

如果有云服务问题，则通过 Microsoft 支持部门提供支持。 若要创建支持请求，请选择管理员门户右上角的帮助和支持图标（问号）。 接着选择“帮助 + 支持”，然后选择“支持”部分下面的“新建支持请求”。  

如果存在部署问题、修补和更新问题、硬件（包括现场可更换部件）问题，或任何硬件品牌软件（例如在硬件生命周期主机上运行的软件）问题，请首先联系 OEM 硬件供应商。

对于其他任何内容，请联系 Microsoft 支持部门。

### <a name="azure-stack-development-kit-asdk"></a>Azure Stack 开发工具包 (ASDK)

对于 ASDK，可以在 [Microsoft 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提出与支持相关的问题。 若要访问论坛，请选择管理员门户右上角的“帮助和支持”图标（问号），然后选择“帮助 + 支持”，再选择“支持”部分下面的“MSDN 论坛”。   我们会定期关注这些论坛。 由于 ASDK 是一个评估环境，因此没有通过 Microsoft 支持部门提供的官方支持。

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 中的区域管理](azure-stack-region-management.md)
