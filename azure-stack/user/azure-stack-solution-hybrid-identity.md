---
title: 配置混合云标识与 Azure 和 Azure Stack 的应用程序 |Microsoft Docs
description: 了解如何使用 Azure 和 Azure Stack 的应用程序中配置混合云标识。
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/26/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 06/26/2019
ms.openlocfilehash: 074d971c1f951797b5dc2d53a62eef56d0b7249f
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492331"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>教程：为 Azure 和 Azure Stack 应用程序配置混合云标识

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

了解如何配置你的 Azure 和 Azure Stack 应用的混合云标识。

有两个选项用于授予对在全球 Azure 和 Azure Stack 中应用的访问。

 * 当 Azure Stack 具有持续连接到 internet 时，可以使用 Azure Active Directory (Azure AD)。
 * 当 Azure Stack 从 internet 断开连接时，可以使用 Azure Directory 联合身份验证服务 (AD FS)。

使用服务主体向 Azure Stack 应用程序以部署或配置 Azure Stack 中使用 Azure 资源管理器授予访问权限。

在本教程中，你将生成到一个示例环境：

> [!div class="checklist"]
> - 在全球 Azure 和 Azure Stack 中建立一个混合标识
> - 检索用于访问 Azure Stack API 的令牌。

必须拥有 Azure Stack 操作员权限才能完成本教程中的步骤。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack 是 Azure 的扩展。 Azure Stack 提供的敏捷性和创新的云计算到您的本地环境中，启用唯一的混合云，可用于生成和部署混合应用任意位置。  
> 
> 本白皮书[混合应用程序的设计注意事项](https://aka.ms/hybrid-cloud-applications-pillars)回顾的软件质量 （放置、 可伸缩性、 可用性、 复原能力、 可管理性和安全性） 进行设计、 部署和操作的支柱混合应用程序。 这些设计注意事项有助于优化混合应用程序设计，从而最大限度地减少生产环境中的难题。


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>在门户中创建适用于 Azure AD 的服务主体

如果在部署 Azure Stack 使用 Azure AD 作为标识存储，可以创建服务主体就像 Azure。 [使用应用程序标识来访问资源](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal)演示如何通过门户执行步骤。 请确保您拥有[所需的 Azure AD 权限](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions)然后再开始。

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>使用 PowerShell 创建适用于 AD FS 的服务主体

如果部署使用 AD FS 的 Azure Stack 时，可以使用 PowerShell 创建服务主体、 分配角色的访问权限，并从 PowerShell 中使用该标识登录。 [使用应用程序标识来访问资源](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal)演示如何执行所需的步骤使用 PowerShell。

## <a name="using-the-azure-stack-api"></a>使用 Azure Stack API

[Azure Stack API](azure-stack-rest-api-use.md) 教程演练了检索令牌来访问 Azure Stack API 的过程。

## <a name="connect-to-azure-stack-using-powershell"></a>使用 Powershell 连接到 Azure Stack

[在 Azure Stack 中使用 PowerShell 启动并运行](../operator/azure-stack-powershell-install.md)快速入门演练了安装 Azure PowerShell 并连接到 Azure Stack 安装所要执行的步骤。

### <a name="prerequisites"></a>必备组件

需要连接到 Azure Active Directory 与订阅可以访问 Azure Stack 安装。 如果没有 Azure Stack 安装，可以使用这些说明来设置[Azure Stack 开发工具包](../asdk/asdk-install.md)。

#### <a name="connect-to-azure-stack-using-code"></a>使用代码连接到 Azure Stack

若要使用代码连接到 Azure Stack，请使用 Azure 资源管理器终结点 API 来获取 Azure Stack 安装的身份验证和 Graph 终结点，然后使用 REST 请求进行身份验证。 可在 [GitHub](https://github.com/shriramnat/HybridARMApplication) 中找到示例客户端应用程序。

>[!Note]
>除非所选语言的 Azure SDK 支持 Azure API 配置文件，否则 SDK 可能无法与 Azure Stack 一起使用。 有关 Azure API 配置文件的详细信息，请参阅[管理 API 版本配置文件](azure-stack-version-profiles.md)一文。

## <a name="next-steps"></a>后续步骤

 - 若要详细了解 Azure Stack 如何处理标识，请参阅 [Azure Stack 的标识体系结构](../operator/azure-stack-identity-architecture.md)。
 - 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
