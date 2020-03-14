---
title: ASDK 管理基础知识
description: 了解如何执行 Azure Stack 开发工具包的基本管理任务（ASDK）。
author: justinha
ms.topic: article
ms.date: 02/19/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: ab0f54d94b96a7bdd75d13f04fd9146146d42740
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79294271"
---
# <a name="asdk-admin-basics"></a>ASDK 管理基础知识
如果你不熟悉 Azure Stack 开发工具包（ASDK）管理，则需要了解几个问题。 本指南概述了你作为评估环境中的 Azure Stack 操作员的角色。 熟悉此信息可确保你的测试用户能够尽快提高工作效率。

首先，应查看[什么是 Azure Stack 开发工具包？](asdk-what-is.md)一文，确保了解 ASDK 及其限制的用途。 你应使用开发工具包作为 "沙盒"，你可以在其中评估 Azure Stack 以在非生产环境中开发和测试应用。 

与 Azure 一样，Azure Stack xerox，因此我们会定期发布新的 ASDK 版本。 但是，你无法升级 ASDK，这与 Azure Stack 集成系统部署相同。 因此，如果要迁移到最新版本，则必须完全重新[部署 ASDK](asdk-redeploy.md)。 不能应用更新包。 此过程需要一定的时间，但好处是您可以在最新功能可用时立即试用它们。 

## <a name="what-account-should-i-use"></a>我应该使用哪个帐户？
管理 Azure Stack 时应注意一些帐户注意事项。 在使用 Windows Server Active Directory 联合身份验证服务（AD FS）作为标识提供程序而不是 Azure Active Directory （Azure AD）的部署中尤其如此。 以下帐户注意事项适用于 Azure Stack 集成系统和 ASDK 部署：

|帐户|Azure AD|AD FS|
|-----|-----|-----|
|本地管理员（.\Administrator）|ASDK 主机管理员|ASDK 主机管理员|
|AzureStack\AzureStackAdmin|ASDK 主机管理员<br><br>可用于登录到 Azure Stack 管理员门户<br><br>查看和管理 Service Fabric 环的访问权限|ASDK 主机管理员<br><br>无权访问 Azure Stack 管理员门户<br><br>查看和管理 Service Fabric 环的访问权限<br><br>不再拥有默认提供程序订阅（DPS）的所有者|
|AzureStack\CloudAdmin|可以访问和运行特权终结点中的允许命令|可以访问和运行特权终结点中的允许命令<br><br>无法登录到 ASDK 主机<br><br>默认提供程序订阅（DPS）的所有者|
|Azure AD 全局管理员|在安装过程中使用<br><br>默认提供程序订阅（DPS）的所有者|不适用|
|

## <a name="what-tools-do-i-use-to-manage"></a>使用哪些工具来管理？
你可以使用[Azure Stack 管理员门户](https://adminportal.local.azurestack.external)或 PowerShell 来管理 Azure Stack。 了解基本概念的最简单方法是通过门户。 如果要使用 PowerShell，则需要安装[适用于 Azure Stack 的 powershell](asdk-post-deploy.md#install-azure-stack-powershell)并[从 GitHub 下载 Azure Stack 工具](asdk-post-deploy.md#download-the-azure-stack-tools)。

Azure Stack 使用 Azure 资源管理器作为其基础的部署、管理和组织机制。 如果要管理 Azure Stack 和帮助支持用户，应了解 Azure 资源管理器。 若要了解详细信息，请阅读[Azure 资源管理器白皮书](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf)中的入门。

## <a name="your-typical-responsibilities"></a>你的典型职责
你的用户希望使用服务。 从其角度来看，你的主要角色是使这些服务可供他们使用。 使用 ASDK，可以了解要提供哪些服务，以及如何通过[创建计划、产品/服务和配额](../operator/azure-stack-tutorial-tenant-vm.md)来使这些服务可用。 还需要将项目添加到 marketplace，如虚拟机（VM）映像。 最简单的方法是将[marketplace 项](../operator/azure-stack-create-and-publish-marketplace-item.md)从 Azure 下载到 Azure Stack。

> [!NOTE]
> 如果要测试计划、产品/服务和服务，应使用[用户门户](https://portal.local.azurestack.external);不是[管理员门户](https://adminportal.local.azurestack.external)。

除了提供服务之外，您还必须负责 Azure Stack 操作员的所有常规任务，使 ASDK 保持正常运行。 这些责任包括：
- 添加 Azure AD 或 AD FS 部署的用户帐户。
- 分配基于角色的访问控制（RBAC）角色（这并不局限于仅限管理员）。
- 监视基础结构运行状况。
- 管理网络和存储资源。
- 替换失败的开发工具包主机硬件。

## <a name="where-to-get-support"></a>获取支持的位置
对于 ASDK，你可以在[AZURE STACK MSDN 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提问与支持相关的问题。

还可以通过单击管理员门户右上角的 "**帮助**" （问号）来访问论坛。 然后单击 "**帮助 + 支持**"，打开 "帮助 + 支持**概述**"，其中包含指向论坛的链接。 将定期监视 MSDN 论坛。  

> [!IMPORTANT]
> 由于 ASDK 是一个评估环境，因此不会通过 Microsoft 客户支持服务（CSS）提供官方支持。

## <a name="next-steps"></a>后续步骤
[部署 ASDK](asdk-install.md)

