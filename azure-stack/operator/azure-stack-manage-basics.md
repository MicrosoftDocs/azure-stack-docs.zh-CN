---
title: Azure Stack 集线器管理基础知识
description: 了解 Azure Stack 集线器管理的基础知识。
author: justinha
ms.topic: article
ms.date: 05/29/2019
ms.author: justinha
ms.lastreviewed: 05/29/2019
ms.openlocfilehash: 53515145d4cbc1ad49ca25261ab65ac8bafdfbb5
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882047"
---
# <a name="azure-stack-hub-administration-basics"></a>Azure Stack 集线器管理基础知识

如果你不熟悉 Azure Stack 集线器管理，则需要了解几个问题。 本文概述了你作为 Azure Stack 中心运营商的角色，以及你需要告诉用户如何帮助他们提高工作效率。

## <a name="understand-the-builds"></a>了解生成

### <a name="integrated-systems"></a>集成系统

如果使用 Azure Stack 集线器集成系统，更新包将分发 Azure Stack 中心的更新版本。 可以导入这些包，并使用管理员门户中的 "**更新**" 磁贴来应用它们。
 
### <a name="development-kit"></a>开发工具包

如果你使用的是 Azure Stack 开发工具包（ASDK），请查看[什么是 Azure Stack 中心？](../asdk/asdk-what-is.md)了解 ASDK 的用途和限制。 你可以使用 ASDK 作为*沙盒*，你可以在其中评估 Azure Stack 中心，并在非生产环境中开发和测试应用。 有关部署信息，请参阅[Azure Stack 开发工具包部署](../asdk/asdk-install.md)。

与 Azure 一样，我们能够快速创新。 我们会定期发布新版本。 如果运行的是 ASDK，并且想要移动到最新版本，则必须重新[部署 Azure Stack 中心](../asdk/asdk-redeploy.md)。 不能应用更新包。 此过程需要一定的时间，但好处是您可以尝试使用最新功能。 我们网站上的 ASDK 文档反映了最新的发布版本。

## <a name="learn-about-available-services"></a>了解可用的服务

你需要了解哪些服务可供用户使用。 Azure Stack 中心支持部分 Azure 服务。 受支持服务的列表将继续发展。

**基础服务**

默认情况下，在部署 Azure Stack 中心时，Azure Stack 中心包含以下 "基础服务"：

- 计算
- 存储空间
- 联网
- Key Vault

使用这些基础服务，可以使用最小配置向用户提供基础结构即服务（IaaS）。

**其他服务**

目前，我们支持以下附加平台即服务（PaaS）服务：

- 应用服务
- Azure Functions
- SQL 和 MySQL 数据库
- Kubernetes （预览版）

这些服务需要进行其他配置，然后才能将它们提供给用户。 有关详细信息，请参阅本 Azure Stack 中心操作员文档的 "教程" 和 "操作方法 guides\Offer 服务" 部分。

**服务路线图**

Azure Stack 中心将继续添加对 Azure 服务的支持。 有关预计的路线图，请参阅[Azure Stack 中心： Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409)白皮书的扩展。 你还可以监视[Azure Stack 集线器博客文章](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview)中的新公告。

## <a name="what-account-should-i-use"></a>我应该使用哪个帐户？

管理 Azure Stack 中心时，需要注意几个帐户注意事项。 尤其是在使用 Windows Server Active Directory 联合身份验证服务（AD FS）作为标识提供程序而不是 Azure Active Directory （Azure AD）的部署中。 以下帐户注意事项适用于 Azure Stack 集线器集成系统和 ASDK 部署：

|帐户|Azure AD|AD FS|
|-----|-----|-----|
|本地管理员（.\Administrator）|ASDK 主机管理员。|ASDK 主机管理员。|
|AzureStack\AzureStackAdmin|ASDK 主机管理员。<br><br>可用于登录到 Azure Stack 中心管理员门户。<br><br>查看和管理 Service Fabric 环的访问。|ASDK 主机管理员。<br><br>无权访问 Azure Stack 中心管理员门户。<br><br>查看和管理 Service Fabric 环的访问。<br><br>不再是默认提供程序订阅（DPS）的所有者。|
|AzureStack\CloudAdmin|可以访问和运行特权终结点中的允许命令。|可以访问和运行特权终结点中的允许命令。<br><br>无法登录到 ASDK 主机。<br><br>默认提供程序订阅（DPS）的所有者。|
|Azure AD 全局管理员|在安装过程中使用。<br><br>默认提供程序订阅（DPS）的所有者。|不适用。|
|

## <a name="what-tools-do-i-use-to-manage"></a>使用哪些工具来管理？
 
可以使用[管理员门户](azure-stack-manage-portals.md)或 PowerShell 来管理 Azure Stack 中心。 了解基本概念的最简单方法是通过门户。 如果要使用 PowerShell，有一些准备步骤。 在开始之前，你可能需要熟悉如何在 Azure Stack 集线器上使用 PowerShell。 有关详细信息，请参阅[Azure Stack 集线器上的 PowerShell 入门](../user/azure-stack-powershell-overview.md)。

Azure Stack 中心使用 Azure 资源管理器作为其基础部署、管理和组织机制。 如果要管理 Azure Stack 中心和帮助支持用户，可以了解资源管理器。 请参阅[Azure 资源管理器](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)白皮书中的入门。

## <a name="your-typical-responsibilities"></a>你的典型职责

你的用户希望使用服务。 从其角度来看，你的主要角色是使这些服务可供他们使用。 通过创建计划、产品/服务和配额来确定要提供的服务，并使这些服务可用。 有关详细信息，请参阅[在 Azure Stack Hub 中提供服务概述](service-plan-offer-subscription-overview.md)。 

还需要将项目添加到[Azure Stack 中心市场](azure-stack-marketplace.md)。 最简单的方法是将[marketplace 项从 Azure 下载到 Azure Stack 中心](azure-stack-download-azure-marketplace-item.md)。

> [!NOTE]
> 如果要测试计划、产品/服务和服务，可以使用[用户门户](azure-stack-manage-portals.md);不是管理员门户。

除了提供服务外，还必须执行操作员的常规职责，才能保持 Azure Stack 集线器正常运行。 这些职责包括以下任务：

- 添加用户帐户（用于[Azure AD](azure-stack-add-new-user-aad.md)部署或[AD FS](azure-stack-add-users-adfs.md)部署）。
- [分配基于角色的访问控制（RBAC）角色](azure-stack-manage-permissions.md)（此任务并不限于管理员。）
- [监视基础结构运行状况](azure-stack-monitor-health.md)。
- 管理[网络](azure-stack-viewing-public-ip-address-consumption.md)和[存储](azure-stack-manage-storage-accounts.md)资源。
- 更换损坏的硬件。 例如，[替换出现故障的磁盘](azure-stack-replace-disk.md)。

## <a name="what-to-tell-your-users"></a>告诉用户的内容

你将需要让用户了解如何在 Azure Stack Hub 中使用服务、如何连接到环境，以及如何订阅产品/服务。 除了任何可能需要提供用户的自定义文档外，还可以将用户定向到[Azure Stack 集线器用户文档](/azure-stack/user/)。

**了解如何在 Azure Stack 中心使用服务**

在 Azure Stack 集线器中使用服务和构建应用之前，用户必须先了解相关信息。 例如，有特定的 PowerShell 和 API 版本要求。 此外，Azure 中的服务与 Azure Stack 集线器中的等效服务之间存在一些功能差异。 确保你的用户查看以下文章：

- [关键注意事项：使用服务或为 Azure Stack 中心构建应用](../user/azure-stack-considerations.md)
- [Azure Stack 中心中虚拟机的注意事项](../user/azure-stack-vm-considerations.md)
- [存储：差异和注意事项](../user/azure-stack-acs-differences.md)

这些文章中的信息总结了 Azure 中服务与 Azure Stack 中心之间的差异。 它对 Azure 服务在全球 Azure 文档中提供的信息进行了补充。

**以用户身份连接到 Azure Stack 中心**

在 ASDK 环境中，如果用户不使用远程桌面连接到 ASDK 主机，则他们可以配置虚拟专用网络（VPN）连接以连接到 Azure Stack 集线器。 请参阅[连接到 Azure Stack 集线器](../asdk/asdk-connect.md)。

用户需要知道如何[访问用户门户](../user/azure-stack-use-portal.md)或如何通过 PowerShell 进行连接。 在集成系统环境中，每个部署的用户门户地址各不相同。 需要为用户提供正确的 URL。

如果使用 PowerShell，用户可能需要先注册资源提供程序，然后才能使用服务。 资源提供程序管理服务。 例如，网络资源提供程序管理虚拟网络、网络接口和负载均衡器等资源。 它们必须[安装](azure-stack-powershell-install.md)powershell，[下载](azure-stack-powershell-download.md)更多的模块，然后[配置](../user/azure-stack-powershell-configure-user.md)powershell （包括资源提供程序注册）。

**订阅套餐**

用户必须订阅作为操作员创建的[产品](azure-stack-subscribe-plan-provision-vm.md)/服务，然后用户才能使用服务。

## <a name="where-to-get-support"></a>获取支持的位置

> [!Note]  
> 若要查找更早版本的 Azure Stack 中心（1905版）的支持信息，请参阅[早期版本 Azure Stack 中心的帮助和支持（1905之前）](azure-stack-servicing-policy.md)。

### <a name="integrated-systems"></a>集成系统

对于集成系统，Microsoft 和我们的原始设备制造商（OEM）硬件合作伙伴之间存在协调的升级和解决过程。

如果有云服务问题，通过 Microsoft 客户支持服务（CSS）提供支持。 若要打开支持请求，请在管理员门户的右上角选择 "帮助和支持" 图标（问号），选择 "**帮助 + 支持**"，然后在 "**支持**" 部分下选择 "**新建支持请求**"。

如果部署、修补和更新、硬件（包括现场可更换部件）或任何硬件品牌软件（如硬件生命周期主机上运行的软件）存在问题，请首先联系你的 OEM 硬件供应商。

对于其他任何内容，请联系 Microsoft CSS。

### <a name="azure-stack-development-kit-asdk"></a>Azure Stack 开发工具包 (ASDK)

对于 ASDK，你可以在[Microsoft 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提问与支持相关的问题。 若要访问论坛，请在管理员门户的右上角选择 "帮助和支持" 图标（问号），然后选择 "**帮助 + 支持**"，然后在 "**支持**" 部分下选择 " **MSDN 论坛**"。 这些论坛会定期受到监视。 由于 ASDK 是一个评估环境，因此没有通过 Microsoft CSS 提供的官方支持。

## <a name="next-steps"></a>后续步骤

[Azure Stack 中心的区域管理](azure-stack-region-management.md)