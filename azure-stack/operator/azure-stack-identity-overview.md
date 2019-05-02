---
title: Azure Stack 的标识概述 | Microsoft Docs
description: 了解可与 Azure Stack 配合使用的标识系统。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: bd58611e08bf0b0de6808c9719311f904ea682ba
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985261"
---
# <a name="overview-of-identity-for-azure-stack"></a>Azure Stack 的标识概述

Azure Stack 要求使用 Active Directory 所支持的 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 作为标识提供者。 提供者的选择是首次部署 Azure Stack 时做出的一次性决定。 本文中的概念和授权详细信息可帮助你选择适当的标识提供者。

选择 Azure AD 还是 AD FS 取决于 Azure Stack 的部署模式：

- 在连接模式下部署时，可以使用 Azure AD 或 AD FS。
- 在未建立 Internet 连接的离线模式下部署时，仅支持 AD FS。

若要详细了解依赖于 Azure Stack 环境的选项，请参阅以下文章：

- Azure Stack 部署工具包：[标识注意事项](azure-stack-datacenter-integration.md#identity-considerations)。
- Azure Stack 集成系统：[Azure Stack 集成系统的部署规划决策](azure-stack-connection-models.md)。

## <a name="common-concepts-for-identity"></a>标识的一般概念

后续部分介绍标识提供者的一般概念及其在 Azure Stack 中的用法。

![标识提供者的术语](media/azure-stack-identity-overview/terminology.png)

### <a name="directory-tenants-and-organizations"></a>目录租户和组织

目录是保留用户、应用程序、组和服务主体相关信息的容器。

目录租户是一个组织，例如 Microsoft 或你自己的公司。

- Azure AD 支持多个租户，并且它可以支持多个组织，每个都在其自己的目录中。 如果使用 Azure AD 并且有多个租户，则可以授权应用程序和用户从一个租户访问同一目录中的其他租户。
- AD FS 仅支持单个租户，因此仅支持单个组织。

### <a name="users-and-groups"></a>用户和组

用户帐户（标识）是标准帐户，可使用用户 ID 和密码对个人进行身份验证。 组可以包含用户或其他组。

创建和管理用户与组的方式取决于所用的标识解决方案。

在 Azure Stack 中，用户帐户：

- 在中创建*用户名\@域*格式。 尽管 AD FS 可将用户帐户映射到 Active Directory 实例，但 AD FS 不支持使用 *\\\<域>\\\<别名>* 格式。
- 可以设置为使用多重身份验证。
- 限制为它们首先注册到的目录，即其组织的目录。
- 可从本地目录导入。 如需详细信息，请参阅[将本地目录与 Azure Active Directory 集成](/azure/active-directory/connect/active-directory-aadconnect)。

当登录到你组织的租户门户时，使用*https:\//portal.local.azurestack.external* URL。 从用于注册 Azure Stack 的域以外的域登录 Azure Stack 门户时，用于注册 Azure Stack 的域名必须追加到门户 URL 后面。 例如，如果 Azure Stack 注册 fabrikam.onmicrosoft.com 和中的日志记录的用户帐户是admin@contoso.com，用于使用登录到用户门户的 url 将为： https:\//portal.local.azurestack.external/fabrikam.onmicrosoft.com。

### <a name="guest-users"></a>来宾用户

来宾用户是其他目录租户中的用户帐户，这些用户已获得目录中资源的访问权限。 若要支持来宾用户，请使用 Azure AD 并启用多租户支持。 若已启用支持，可以邀请来宾用户访问目录租户中的资源，方便他们与外部组织协作。

若要邀请的来宾用户，云操作员和用户可以使用[Azure AD B2B 协作](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)。 受邀的用户有权访问文档、 资源和应用程序从你的目录，并保持对你自己的资源和数据的控制。 

可以作为来宾用户登录到其他组织的目录租户。 为此，请将该组织的目录名称追加到门户 URL。 例如，如果你属于 Contoso 组织，并且想要登录 Fabrikam 目录，则使用 https:\//portal.local.azurestack.external/fabrikam.onmicrosoft.com。

### <a name="applications"></a>应用程序

可以向 Azure AD 或 AD FS 注册应用程序，然后将应用程序提供给组织中的用户。

应用程序包括：

- **Web 应用程序**：示例包括 Azure 门户和 Azure 资源管理器。 它们支持 Web API 调用。
- **本机客户端**：示例包括 Azure PowerShell、Visual Studio 和 Azure CLI。

应用程序可支持两种租户：

- **单租户**：仅支持应用程序注册时所在的目录中的用户和服务。

  > [!NOTE]
  > 由于 AD FS 仅支持单个目录，因此在 AD FS 拓扑中创建的应用程序设计为单租户应用程序。

- **多租户**：支持应用程序注册时所在目录以及其他租户目录中的用户和服务使用。 另一个租户目录（另一个 Azure AD 租户）的用户可以使用多租户应用程序登录到应用程序。 

  有关多租户的详细信息，请参阅[启用多租户](azure-stack-enable-multitenancy.md)。

  有关如何开发多租户应用的详细信息，请参阅[多租户应用](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)。

注册应用程序时，请创建两个对象：

- **应用程序对象**：应用程序在所有租户中的全局表示形式。 此关系是与软件应用程序的一对一关系，且只存在于应用程序首先注册到的目录中。

- **服务主体对象**：在首先注册应用程序的目录中针对应用程序创建的凭据。 服务主体也是在使用该应用程序的其他每个租户的目录中创建的。 此关系可以是与软件应用程序的一对多关系。

有关应用程序和服务主体的详细信息，请参阅 [Azure Active Directory 中的应用程序和服务主体对象](/azure/active-directory/develop/active-directory-application-objects)。

### <a name="service-principals"></a>服务主体

服务主体是应用程序或服务的一组凭据，可授予 Azure Stack 中资源的访问权限。 使用服务主体可将应用程序权限与应用程序用户权限区分开来。

服务主体是在使用应用程序的每个租户中创建的。 服务主体建立的标识用于登录和访问受该租户保护的资源（例如用户）。

- 单租户应用程序在其首次创建所在的目录中只有一个服务主体。 此服务主体在应用程序注册期间创建并允许用户使用。
- 多租户 Web 应用程序或 API 将在每个租户中创建一个服务主体，前提是该租户的用户同意使用此应用程序。

服务主体的凭据可以是通过 Azure 门户生成的密钥，也可以是证书。 证书的使用很适合自动化，因为证书被视为比密钥更安全。 

> [!NOTE]
> 在 Azure Stack 中使用 AD FS 时，只有管理员可以创建服务主体。 使用 AD FS 时，服务主体需要证书，并需要通过特权终结点 (PEP) 来创建。 有关详细信息，请参阅[提供对 Azure Stack 的应用程序访问权限](azure-stack-create-service-principals.md)。

若要了解 Azure Stack 的服务主体，请参阅[创建服务主体](azure-stack-create-service-principals.md)。

### <a name="services"></a>服务

Azure Stack 中与标识提供者交互的服务，标识提供者将其注册为应用程序。 如同应用程序，注册可让服务在标识系统中进行身份验证。

所有 Azure 服务都使用 [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 协议和 [JSON Web 令牌](/azure/active-directory/develop/active-directory-token-and-claims)来建立其标识。 由于 Azure AD 和 AD FS 使用的协议一致，因此可以使用 [Azure Active Directory 身份验证库](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) 在本地环境或 Azure 中进行身份验证（在联网场景中）。 还可以通过 ADAL 使用 Azure PowerShell 和 Azure CLI 等工具进行跨云的和本地的资源管理。

### <a name="identities-and-your-identity-system"></a>标识和标识系统

Azure Stack 的标识包括用户帐户、组和服务主体。

当安装 Azure Stack 时，有多个内置应用程序和服务在目录租户中自动向标识提供者注册。 有些注册的服务用于管理。 还有些服务供用户使用。 默认注册提供核心服务标识，此类标识可彼此交互，以及与今后添加的标识交互。

如果设置采用多租户的 Azure AD，则有些应用程序会传播到新的目录。

## <a name="authentication-and-authorization"></a>身份验证和授权

### <a name="authentication-by-applications-and-users"></a>应用程序和用户的身份验证

![Azure Stack 层之间的标识](media/azure-stack-identity-overview/identity-layers.png)

对应用程序和用户而言，Azure Stack 的体系结构可以用四个层来描述。 各层之间的交互可以使用不同类型的身份验证。

|层    |各层之间的身份验证  |
|---------|---------|
|工具与客户端，例如管理门户     | 为了访问或修改 Azure Stack 中的资源，工具和客户端将使用 [JSON Web 令牌](/azure/active-directory/develop/active-directory-token-and-claims)来调用 Azure 资源管理器。 <br>Azure 资源管理器验证 JSON Web 令牌并扫视所颁发令牌中的声明，以评估用户或服务主体在 Azure Stack 中的授权级别。 |
|Azure 资源管理器及其核心服务     |Azure 资源管理器与资源提供程序通信，以传输用户的通信。 <br> 传输通过 [Azure 资源管理器模板](/azure-stack/user/azure-stack-arm-templates)使用直接命令式调用或声明式调用。|
|资源提供程序     |传递给资源提供程序的调用通过基于证书的身份验证进行保护。 <br>随后，Azure 资源管理器和资源提供程序持续通过 API 通信。 对于从 Azure 资源管理器 收到的每个调用，资源提供程序使用该证书来验证调用。|
|基础结构和业务逻辑     |资源提供程序使用所选的身份验证模式与业务逻辑和基础结构通信。 Azure Stack 随附的默认资源提供程序使用 Windows 身份验证来保护此通信。|

![身份验证所需的信息](media/azure-stack-identity-overview/authentication.png)

### <a name="authenticate-to-azure-resource-manager"></a>对 Azure 资源管理器进行身份验证

若要在标识提供者中进行身份验证并接收 JSON Web 令牌，必须提供以下信息：

1. **标识系统（颁发机构）的 URL**：可用于访问标识提供者的 URL。 例如， *https:\//login.windows.net*。
2. **Azure 资源管理器的应用 ID URI**：已向标识提供者注册的 Azure 资源管理器的唯一标识符， 也是每个 Azure Stack 安装中的唯一标识符。
3. **凭据**：用于在标识提供者中进行身份验证的凭据。
4. **Azure 资源管理器 的 URL**：该 URL 是 Azure 资源管理器服务的位置。 例如， *https:\//management.azure.com*或*https:\//management.local.azurestack.external*。

当主体（客户端、应用程序或用户）发出访问资源所需的身份验证请求时，该请求必须包含：

- 主体的凭据。
- 主体想要访问的资源的应用 ID URI。

凭据已由标识提供者验证。 标识提供者还会验证应用 ID URI 是否用于已注册的应用程序，以及主体是否拥有适当的权限，可获取该资源的令牌。 如果请求有效，则授予 JSON Web 令牌。

然后，必须将此令牌传入发往 Azure 资源管理器的请求的标头中。 Azure 资源管理器执行以下操作（不遵循特定的顺序）：

- 验证 *issuer* (iss) 声明，确认令牌来自正确的标识提供者。
- 验证 *audience* (aud) 声明，确认令牌已颁发给 Azure 资源管理器。
- 验证 JSON Web 令牌是否已使用通过 OpenID 配置的证书进行签名，并且为 Azure 资源管理器所知。
- 检查 *issued at* (iat) 和 *expiration* (exp) 声明，确认令牌处于活动状态并且可以接受。

完成所有验证后，Azure 资源管理器使用 *objected* (oid) 和 *groups* 声明来生成主体可以访问的资源列表。

![令牌交换协议示意图](media/azure-stack-identity-overview/token-exchange.png)

> [!NOTE]
> 部署后，不需要 Azure Active Directory 全局管理员权限。 但是，某些操作可能需要全局管理员凭据。 例如，资源提供程序安装程序脚本或需要授予权限的新功能。 你可以暂时重新使用该帐户的全局管理员权限或使用单独的全局管理员帐户的所有者*默认提供商订阅*。

### <a name="use-role-based-access-control"></a>使用基于角色的访问控制

Azure Stack 中基于角色的访问控制 (RBAC) 与 Microsoft Azure 中的实现是一致的。 可以通过将相应的 RBAC 角色分配给用户、组和应用程序来管理对资源的访问权限。
有关如何在 Azure Stack 中使用 RBAC 的信息，请参阅以下文章：

- [Azure 门户中基于角色的访问控制入门](/azure/role-based-access-control/overview)。
- [使用基于角色的访问控制管理 Azure 订阅资源的访问权限](/azure/role-based-access-control/role-assignments-portal)
- [创建用于 Azure 基于角色的访问控制的自定义角色](/azure/role-based-access-control/custom-roles)
- 在 Azure Stack 中[管理基于角色的访问控制](azure-stack-manage-permissions.md)。

### <a name="authenticate-with-azure-powershell"></a>使用 Azure PowerShell 进行身份验证

有关使用 Azure PowerShell 在 Azure Stack 中进行身份验证的详细信息，请参阅[配置 Azure Stack 用户的 PowerShell 环境](../user/azure-stack-powershell-configure-user.md)。

### <a name="authenticate-with-azure-cli"></a>使用 Azure CLI 进行身份验证

有关使用 Azure PowerShell 在 Azure Stack 中进行身份验证的信息，请参阅[安装和配置与 Azure Stack 配合使用的 Azure CLI](/azure-stack/user/azure-stack-version-profiles-azurecli2)。

## <a name="next-steps"></a>后续步骤

- [标识体系结构](azure-stack-identity-architecture.md)
- [数据中心集成 - 标识](azure-stack-integrate-identity.md)
