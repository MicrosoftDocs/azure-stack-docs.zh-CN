---
title: Azure Stack 中心的标识体系结构
description: 了解 Azure Stack 集线器的标识体系结构，以及 Azure AD 和 AD FS 之间的差异。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: inhenkel
ms.reviewer: fiseraci
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 528e008c5c3c166979ba4c3d4fa15d6a94cc0f87
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509919"
---
# <a name="identity-architecture-for-azure-stack-hub"></a>Azure Stack 中心的标识体系结构

选择要与 Azure Stack 中心一起使用的标识提供者时，应了解 Azure Active Directory （Azure AD）和 Active Directory 联合身份验证服务（AD FS）的选项之间的重要区别。

## <a name="capabilities-and-limitations"></a>功能和限制

你选择的标识提供者可以限制你的选项，包括对多租户的支持。

|功能或方案        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|已连接到 internet     |是       |可选|
|支持多租户     |是       |否      |
|在 Marketplace 中提供项目 |是       |是（需要使用[脱机 Marketplace 联合](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario)工具）|
|支持 Active Directory 身份验证库（ADAL） |是 |是|
|支持 Azure CLI、Visual Studio 和 PowerShell 等工具  |是 |是|
|通过 Azure 门户创建服务主体     |是 |否|
|创建具有证书的服务主体      |是 |是|
|用机密（密钥）创建服务主体    |是 |是|
|应用程序可以使用图形服务           |是 |否|
|应用程序可以使用标识提供程序登录 |是 |是（要求应用与本地 AD FS 实例联合） |

## <a name="topologies"></a>拓扑

以下部分介绍了可以使用的不同标识拓扑。

### <a name="azure-ad-single-tenant-topology"></a>Azure AD：单租户拓扑

默认情况下，当你安装 Azure Stack 集线器并使用 Azure AD 时，Azure Stack 集线器使用单租户拓扑。

单租户拓扑在以下情况中非常有用：
- 所有用户都属于同一租户。
- 服务提供商为组织托管 Azure Stack 的中心实例。

![Azure AD Azure Stack 中心单租户拓扑](media/azure-stack-identity-architecture/single-tenant.png)

此拓扑具有以下特征：

- Azure Stack 中心将所有应用和服务注册到同一个 Azure AD 租户目录。
- Azure Stack 中心仅对该目录中的用户和应用进行身份验证，包括令牌。
- 管理员（云操作员）和租户用户的标识位于同一个目录租户中。
- 若要使其他目录中的用户能够访问此 Azure Stack 中心环境，必须将该[用户邀请](azure-stack-identity-overview.md#guest-users)为租户目录的来宾。

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD：多租户拓扑

云操作员可以将 Azure Stack 中心配置为允许来自一个或多个组织的租户访问应用。 用户通过 Azure Stack 集线器用户门户访问应用。 在此配置中，管理员门户（由云操作员使用）仅限于单个目录中的用户。

多租户拓扑在以下情况中非常有用：

- 服务提供商希望允许多个组织的用户访问 Azure Stack 中心。

![Azure AD Azure Stack 中心多租户拓扑](media/azure-stack-identity-architecture/multi-tenant.png)

此拓扑具有以下特征：

- 资源的访问权限应为每个组织。
- 一个组织中的用户应该无法向其组织外部的用户授予对资源的访问权限。
- 管理员（云操作员）标识可以位于与用户标识不同的目录租户中。 此隔离在标识提供程序级别提供帐户隔离。
 
### <a name="ad-fs"></a>AD FS

如果满足以下任一条件，则需要 AD FS 拓扑：

- Azure Stack 中心未连接到 internet。
- Azure Stack 集线器可以连接到 internet，但你选择为标识提供者使用 AD FS。
  
![使用 AD FS Azure Stack 集线器拓扑](media/azure-stack-identity-architecture/adfs.png)

此拓扑具有以下特征：

- 若要支持在生产环境中使用此拓扑，必须通过联合身份验证信任将内置 Azure Stack 中心 AD FS 实例与 Active Directory 支持的现有 AD FS 实例集成。
- 可以将 Azure Stack 中心中的图形服务与现有 Active Directory 实例集成。 你还可以使用基于 OData 的图形 API 服务，该服务支持与 Azure AD 图形 API 一致的 Api。

  若要与 Active Directory 实例进行交互，图形 API 需要具有只读权限的 Active Directory 实例中的用户凭据。
  - 内置 AD FS 实例基于 Windows Server 2016。
  - AD FS 和 Active Directory 实例必须基于 Windows Server 2012 或更高版本。
  
  在 Active Directory 实例与内置 AD FS 实例之间，交互不限于 OpenID Connect，它们可以使用任何相互支持的协议。
  - 用户帐户是在本地 Active Directory 实例中创建和管理的。
  - 应用的服务主体和注册在内置 Active Directory 实例中进行管理。

## <a name="next-steps"></a>后续步骤

- [标识概述](azure-stack-identity-overview.md)
- [数据中心集成-标识](azure-stack-integrate-identity.md)
