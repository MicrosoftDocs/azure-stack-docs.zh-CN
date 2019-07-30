---
title: 配置 Azure 和 Azure Stack apps 的混合云标识 |Microsoft Docs
description: 了解如何配置 Azure 的混合云标识和 Azure Stack 应用。
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 06/26/2019
ms.openlocfilehash: bc614cb514b9d35523749944486aa375bb8fce9c
ms.sourcegitcommit: 35b13ea6dc0221a15cd0840be796f4af5370ddaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68603010"
---
# <a name="configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>为 Azure 和 Azure Stack 应用程序配置混合云标识

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

了解如何为 Azure 和 Azure Stack 应用配置混合云标识。

你有两个选项可用于在全球 Azure 和 Azure Stack 中授予对应用的访问权限。

 * 如果 Azure Stack 持续连接到 internet, 则可以使用 Azure Active Directory (Azure AD)。
 * 当 Azure Stack 从 internet 断开连接时, 可以使用 Azure 目录联合服务 (AD FS)。

你可以使用服务主体, 通过 Azure Stack 中的 Azure 资源管理器, 为部署或配置授予对 Azure Stack 应用的访问权限。

在此解决方案中, 你将构建一个示例环境, 用于:

> [!div class="checklist"]
> - 在全球 Azure 和 Azure Stack 中建立一个混合标识
> - 检索用于访问 Azure Stack API 的令牌。

对于此解决方案中的步骤, 你必须具有 Azure Stack 操作员权限。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack 是 Azure 的扩展。 Azure Stack 将云计算的灵活性和创新带入本地环境, 从而实现了唯一的混合云, 使你能够在任何位置构建和部署混合应用。  
> 
> [混合应用程序的设计注意事项](azure-stack-edge-pattern-overview.md)查看软件质量的支柱 (放置、可伸缩性、可用性、复原能力、可管理性和安全性), 以便设计、部署和操作混合应用程序。 设计注意事项有助于优化混合应用设计, 并最大程度减少生产环境中的挑战。


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>在门户中创建适用于 Azure AD 的服务主体

如果你使用 Azure AD 作为标识存储来部署 Azure Stack, 则可以创建服务主体, 就像在 Azure 中一样。 [使用应用标识访问资源](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal)说明了如何通过门户执行这些步骤。 在开始之前, 请确保具有[所需的 Azure AD 权限](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)。

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>使用 PowerShell 创建适用于 AD FS 的服务主体

如果你使用 AD FS 部署 Azure Stack, 则可以使用 PowerShell 来创建服务主体, 为访问分配角色, 并使用该标识从 PowerShell 登录。 [使用应用标识访问资源](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal)说明了如何使用 PowerShell 执行所需的步骤。

## <a name="using-the-azure-stack-api"></a>使用 Azure Stack API

[AZURE STACK API](azure-stack-rest-api-use.md)解决方案将引导你完成检索用于访问 Azure Stack API 的令牌的过程。

## <a name="connect-to-azure-stack-using-powershell"></a>使用 Powershell 连接到 Azure Stack

[在 Azure Stack 中使用 PowerShell 启动并运行](../operator/azure-stack-powershell-install.md)快速入门演练了安装 Azure PowerShell 并连接到 Azure Stack 安装所要执行的步骤。

### <a name="prerequisites"></a>先决条件

需要 Azure Stack 安装连接到可以访问的订阅 Azure Active Directory。 如果未安装 Azure Stack, 则可以使用这些说明来设置[Azure Stack 开发工具包](../asdk/asdk-install.md)。

#### <a name="connect-to-azure-stack-using-code"></a>使用代码连接到 Azure Stack

若要使用代码连接到 Azure Stack，请使用 Azure 资源管理器终结点 API 来获取 Azure Stack 安装的身份验证和 Graph 终结点，然后使用 REST 请求进行身份验证。 可在 [GitHub](https://github.com/shriramnat/HybridARMApplication) 中找到示例客户端应用程序。

>[!Note]
>除非所选语言的 Azure SDK 支持 Azure API 配置文件，否则 SDK 可能无法与 Azure Stack 一起使用。 有关 Azure API 配置文件的详细信息，请参阅[管理 API 版本配置文件](azure-stack-version-profiles.md)一文。

## <a name="next-steps"></a>后续步骤

 - 若要详细了解 Azure Stack 如何处理标识，请参阅 [Azure Stack 的标识体系结构](../operator/azure-stack-identity-architecture.md)。
 - 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
