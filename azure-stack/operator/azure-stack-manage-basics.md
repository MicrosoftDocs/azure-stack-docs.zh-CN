---
title: Azure Stack 管理基础知识 | Microsoft Docs
description: 了解对 Azure Stack 进行管理需要知道哪些知识。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 83c4295c5f14cb89122aca096b7e90cfd44d8d4a
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618595"
---
# <a name="azure-stack-administration-basics"></a>Azure Stack 管理基础知识
如果不熟悉 Azure Stack 管理，则需要了解几项事情。 本文概述了 Azure Stack 操作员角色，以及需要告知用户哪些东西才能让他们快速提高工作效率。

## <a name="understand-the-builds"></a>了解版本

### <a name="integrated-systems"></a>集成系统

如果使用 Azure Stack 集成系统，则会通过更新包分发更新版的 Azure Stack。 可以通过管理员门户中的“更新”磁贴导入并应用这些包。 不支持更新 ASDK 安装。 
 
### <a name="development-kit"></a>开发工具包

如果使用 Azure Stack 开发工具包，请参阅[什么是 Azure Stack？](../asdk/asdk-what-is.md)一文，确保了解该开发工具包的用途和限制。 应该将开发工具包作为“沙盒”使用，在其中对 Azure Stack 进行评估，并在非生产环境中开发和测试应用。 （有关部署信息，请参阅 [Azure Stack 开发工具包部署](../asdk/asdk-install.md)一文。）

正如 Azure 一样，我们的创新速度很快。 我们会定期发布新版本。 如果开发包正在运行，但需要更新到最新版本，则必须[重新部署 Azure Stack](../asdk/asdk-redeploy.md)。 不能应用更新包。 此过程需要一定的时间，但好处是可以尝试最新功能。 我们网站上的开发工具包文档反映了最新的发行版。

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

这些服务在提供给用户之前，需要进行额外的配置。 有关详细信息，请参阅 Azure Stack 操作员文档的“教程”和“操作指南\产品服务”部分。

**服务路线图**

Azure Stack 会持续增加对 Azure 服务的支持。 有关计划的路线图，请参阅 [Azure Stack：Azure 的扩展](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409)白皮书。 也可留意 [Azure Stack 博客文章](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview)中的新公告。

## <a name="what-account-should-i-use"></a>我应使用什么帐户?
管理 Azure Stack 时，应该注意帐户方面的几个事项， 尤其是在使用 Windows Server Active Directory 联合身份验证服务 (AD FS) 而不是 Azure Active Directory (Azure AD) 作为标识提供者的部署中。 以下帐户注意事项同时适用于 Azure Stack 集成系统和 ASDK 部署：


|帐户|Azure AD|AD FS|
|-----|-----|-----|
|本地管理员 (.\Administrator)|ASDK 主机管理员|ASDK 主机管理员|
|AzureStack\AzureStackAdmin|ASDK 主机管理员<br><br>可用于登录到 Azure Stack 管理门户<br><br>拥有查看和管理 Service Fabric 环的访问权限|ASDK 主机管理员<br><br>没有 Azure Stack 管理门户的访问权限<br><br>拥有查看和管理 Service Fabric 环的访问权限<br><br>不再是默认提供程序订阅 (DPS) 的所有者|
|AzureStack\CloudAdmin|可在特权终结点中访问和运行允许的命令|可在特权终结点中访问和运行允许的命令<br><br>无法登录到 ASDK 主机<br><br>默认提供程序订阅 (DPS) 的所有者|
|Azure AD 全局管理员|安装期间使用<br><br>默认提供程序订阅 (DPS) 的所有者|不适用|
|

## <a name="what-tools-do-i-use-to-manage"></a>使用哪些工具进行管理？
 
可以使用[管理员门户](azure-stack-manage-portals.md)或 PowerShell 来管理 Azure Stack。 若要了解基本概念，最简单的方式是使用门户。 若要使用 PowerShell，则需完成准备步骤。 必须[安装](azure-stack-powershell-install.md) PowerShell，[下载](azure-stack-powershell-download.md)更多的模块，然后[配置](azure-stack-powershell-configure-admin.md) PowerShell。

Azure Stack 使用 Azure 资源管理器作为其基础的部署、管理和组织机制。 若要管理 Azure Stack 并帮助支持用户，应了解资源管理器。 请参阅 [Azure 资源管理器入门](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)白皮书。

## <a name="your-typical-responsibilities"></a>典型责任

用户需要使用服务， 从其角度来看，你的主要角色是向他们提供这些服务。 你必须通过创建计划、套餐和配额来确定要提供的具体服务并将这些服务提供给用户。 有关详细信息，请参阅[概述：如何在 Azure Stack 中提供服务](azure-stack-offer-services-overview.md)。 

此外还需向[市场](azure-stack-marketplace.md)添加项，例如虚拟机映像。 最简单的方式是[将市场项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md)。

> [!NOTE]
> 如需测试计划、套餐和服务，应使用[用户门户](azure-stack-manage-portals.md)而不是管理员门户。

除了提供服务，还必须执行操作员的所有常规任务，使 Azure Stack 始终能够启动并运行。 这些任务包括以下内容：

- 添加用户帐户（用于 [Azure Active Directory](azure-stack-add-new-user-aad.md) 部署或 [Active Directory 联合身份验证服务 (AD FS)](azure-stack-add-users-adfs.md) 部署）
- [分配基于角色的访问控制 (RBAC) 角色](azure-stack-manage-permissions.md)（不限管理员）
- [监视基础结构运行状况](azure-stack-monitor-health.md)
- 管理[网络](azure-stack-viewing-public-ip-address-consumption.md)和[存储](azure-stack-manage-storage-accounts.md)资源
- 更换损坏的硬件，例如[更换故障磁盘](azure-stack-replace-disk.md)。

## <a name="what-to-tell-your-users"></a>需要告知用户的内容

需要让用户知道如何使用 Azure Stack 中的服务、如何连接到环境，以及如何订阅套餐。 除了根据需要提供用户自定义文档，还可以引导用户访问 Azure Stack 用户文档站点。

**了解如何使用 Azure Stack 中的服务**

在 Azure Stack 中使用服务和开发应用之前，用户必须了解某些信息。 例如，必须了解特定的 PowerShell 和 API 版本要求。 另外，Azure 中的服务与 Azure Stack 中的相应服务存在一些功能差异。 请确保用户参阅以下文章：

- [重要注意事项：使用 Azure Stack 的服务或构建适用于 Azure Stack 的应用](../user/azure-stack-considerations.md)
- [Azure Stack 中虚拟机的注意事项](../user/azure-stack-vm-considerations.md)
- [存储：差异和注意事项](../user/azure-stack-acs-differences.md)

这些文章汇总了 Azure 和 Azure Stack 中的服务差异， 是对全球 Azure 文档中 Azure 服务的可用信息的补充。

**以用户身份连接到 Azure Stack**

在开发工具包环境中，如果不能通过远程桌面访问开发工具包主机，用户必须先配置虚拟专用网络 (VPN) 连接，然后才能访问 Azure Stack。 请参阅[连接到 Azure Stack](../asdk/asdk-connect.md)。 

你的用户会想要了解如何[访问用户门户](../user/azure-stack-use-portal.md)或如何通过 PowerShell 进行连接。 在集成系统环境中，用户门户地址随部署而变。 需向用户提供正确的 URL。

如果使用 PowerShell，用户可能需要先注册资源提供程序，然后才能使用服务。 （资源提供程序用于管理服务。 例如，网络资源提供程序用于管理虚拟网络、网络接口和负载均衡器之类的资源。）用户必须[安装](azure-stack-powershell-install.md) PowerShell，[下载](azure-stack-powershell-download.md)更多的模块，然后[配置](../user/azure-stack-powershell-configure-user.md) PowerShell（包括资源提供程序注册）。

**订阅套餐**

在访问服务之前，用户必须[订阅套餐](azure-stack-subscribe-plan-provision-vm.md)，该产品/服务是你以操作员身份创建的。

## <a name="where-to-get-support"></a>从何处获取支持

### <a name="integrated-systems"></a>集成系统

对于集成系统，Microsoft 和其原始设备制造商 (OEM) 硬件合作伙伴之间已经建立了协作的问题升级和解决流程。

如果存在云服务问题，请通过 Microsoft 客户支持服务 (CSS) 寻求支持。 单击管理员门户右上角的“帮助和支持”图标（问号），再单击“新建支持请求”，则会打开一个网站，然后就可以在其中直接提出支持请求。

如果存在部署问题、修补和更新问题、硬件（包括现场可更换部件）问题，以及任何硬件品牌软件（例如在硬件生命周期主机上运行的软件）问题，请首先联系 OEM 硬件供应商。

至于其他问题，请联系 Microsoft CSS。

### <a name="development-kit"></a>开发工具包

至于开发工具包，可以在 [Microsoft 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提出与支持相关的问题。 单击管理员门户右上角的“帮助和支持”图标（问号），然后单击“新建支持请求”，则会直接打开论坛网站。 我们会定期关注这些论坛。 由于开发工具包是一个评估环境，因此我们不会通过 Microsoft CSS 提供官方支持。

## <a name="next-steps"></a>后续步骤

[Azure Stack 中的区域管理](azure-stack-region-management.md)


