---
title: 为 Azure 和 Azure Stack 中心应用配置混合云标识
description: 了解如何配置 Azure 和 Azure Stack 中心应用的混合云标识。
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: dc3096888138400007c4091ec9f7f40e289340ed
ms.sourcegitcommit: a7db4594de43c31fe0c51e60e84fdaf4d41ef1bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2020
ms.locfileid: "77568412"
---
# <a name="configure-hybrid-cloud-identity-for-azure-and-azure-stack-hub-applications"></a>配置 Azure 的混合云标识和 Azure Stack 中心应用程序

了解如何为 Azure 和 Azure Stack 中心应用配置混合云标识。

你有两个选项可用于在全球 Azure 和 Azure Stack 中心授予对应用的访问权限。

 * 当 Azure Stack 集线器持续连接到 internet 时，可以使用 Azure Active Directory （Azure AD）。
 * 当 Azure Stack 集线器从 internet 断开连接时，可以使用 Azure 目录联合服务（AD FS）。

你可以使用服务主体，通过 Azure Stack 中心的 Azure 资源管理器授予对 Azure Stack 中心应用的访问权限，以便进行部署或配置。

在此解决方案中，你将构建一个示例环境，用于：

> [!div class="checklist"]
> - 在全球 Azure 和 Azure Stack 集线器中建立混合标识
> - 检索用于访问 Azure Stack 中心 API 的令牌。

对于此解决方案中的步骤，你必须具有 Azure Stack 中心操作员权限。

> [!Tip]  
> ![hybrid-pillars](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack 中心是 Azure 的扩展。 Azure Stack 中心为本地环境带来了云计算的灵活性和革新，使你能够在任何位置构建和部署混合应用，从而实现了唯一的混合云。  
> 
> [混合应用程序的设计注意事项](overview-app-design-considerations.md)查看软件质量的支柱（放置、可伸缩性、可用性、复原能力、可管理性和安全性），以便设计、部署和操作混合应用程序。 设计注意事项有助于优化混合应用设计，并最大程度减少生产环境中的挑战。


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>在门户中创建 Azure AD 的服务主体

如果你使用 Azure AD 作为标识存储来部署 Azure Stack 集线器，则可以创建服务主体，就像对 Azure 执行的操作一样。 [使用应用标识访问资源](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal)说明了如何通过门户执行这些步骤。 在开始之前，请确保具有[所需的 Azure AD 权限](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)。

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>使用 PowerShell 创建 AD FS 的服务主体

如果你使用 AD FS 部署 Azure Stack 集线器，则可以使用 PowerShell 来创建服务主体，为访问分配角色，并使用该标识从 PowerShell 登录。 [使用应用标识访问资源](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal)说明了如何使用 PowerShell 执行所需的步骤。

## <a name="using-the-azure-stack-hub-api"></a>使用 Azure Stack 集线器 API

[Azure Stack 中心 api](../user/azure-stack-rest-api-use.md)解决方案将引导你完成检索用于访问 AZURE STACK 中心 API 的令牌的过程。

## <a name="connect-to-azure-stack-hub-using-powershell"></a>使用 Powershell 连接到 Azure Stack 集线器

在[Azure Stack Hub 中启动并运行 PowerShell](../operator/azure-stack-powershell-install.md)的快速入门介绍了安装 Azure PowerShell 并连接到 Azure Stack 集线器安装所需的步骤。

### <a name="prerequisites"></a>先决条件

你需要与你可以访问的订阅连接到 Azure Active Directory Azure Stack 中心安装。 如果没有 Azure Stack 集线器安装，则可以使用这些说明来设置[Azure Stack 开发工具包](../asdk/asdk-install.md)。

#### <a name="connect-to-azure-stack-hub-using-code"></a>使用代码连接到 Azure Stack 集线器

若要使用代码连接到 Azure Stack 中心，请使用 Azure 资源管理器终结点 API 获取 Azure Stack 集线器安装的身份验证和图形终结点，然后使用 REST 请求进行身份验证。 可在[GitHub](https://github.com/shriramnat/HybridARMApplication)上找到一个示例客户端应用程序。

>[!Note]
>除非你选择的 Azure SDK 支持 Azure API 配置文件，否则，SDK 可能无法与 Azure Stack 中心配合使用。 若要了解有关 Azure API 配置文件的详细信息，请参阅[管理 API 版本配置文件](../user/azure-stack-version-profiles.md)一文。

## <a name="next-steps"></a>后续步骤

 - 若要详细了解如何在 Azure Stack 中心处理标识，请参阅[Azure Stack 中心的标识体系结构](../operator/azure-stack-identity-architecture.md)。
 - 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
