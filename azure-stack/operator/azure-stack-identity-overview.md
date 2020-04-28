---
title: Azure Stack Hub 的标识提供者概述
description: 了解可与 Azure Stack Hub 配合使用的标识提供者。
author: BryanLa
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: b078158bebf83835e4a0a0eb6d92ba90b4679ca9
ms.sourcegitcommit: d930d52e27073829b8bf8ac2d581ec2accfa37e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "82173958"
---
# <a name="overview-of-identity-providers-for-azure-stack-hub"></a>Azure Stack Hub 的标识提供者概述

Azure Stack Hub 要求使用 Active Directory 所支持的 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 作为标识提供者。 提供者的选择是首次部署 Azure Stack Hub 时做出的一次性决定。 本文中的概念和授权详细信息可帮助你选择适当的标识提供者。

选择 Azure AD 还是 AD FS 取决于 Azure Stack Hub 的部署模式：

- 在连接模式下部署时，可以使用 Azure AD 或 AD FS。
- 在未建立 Internet 连接的离线模式下部署时，仅支持 AD FS。

若要详细了解依赖于 Azure Stack Hub 环境的选项，请参阅以下文章：

- Azure Stack Hub 部署工具包：[标识注意事项](azure-stack-datacenter-integration.md#identity-considerations)。
- Azure Stack Hub 集成系统：[Azure Stack Hub 集成系统的部署规划决策](azure-stack-connection-models.md)。

## <a name="common-concepts-for-identity-providers"></a>标识提供者的一般概念

后续部分介绍标识提供者的一般概念及其在 Azure Stack Hub 中的用法。

![标识提供者的术语](media/azure-stack-identity-overview/terminology.svg)

### <a name="directory-tenants-and-organizations"></a>目录租户和组织

目录是保留用户、应用程序、组和服务主体相关信息的容器。    

目录租户是一个*组织*，例如 Microsoft 或你自己的公司。

- Azure AD 支持多个租户并可支持多个组织（各自位于自身的目录中）。 如果使用 Azure AD 并且有多个租户，则可以授权应用和用户从一个租户访问同一目录中的其他租户。
- AD FS 仅支持单个租户，因此仅支持单个组织。

### <a name="users-and-groups"></a>用户和组

用户帐户（标识）是标准帐户，可使用用户 ID 和密码对个人进行身份验证。 组可以包含用户或其他组。

创建和管理用户与组的方式取决于所用的标识解决方案。

在 Azure Stack Hub 中，用户帐户：

- 以 *username\@domain* 格式创建。 尽管 AD FS 可将用户帐户映射到 Active Directory 实例，但 AD FS 不支持使用 *\\\<域>\\\<别名>* 格式。
- 可以设置为使用多重身份验证。
- 限制为它们首先注册到的目录，即其组织的目录。
- 可从本地目录导入。 如需详细信息，请参阅[将本地目录与 Azure Active Directory 集成](/azure/active-directory/connect/active-directory-aadconnect)。

登录到组织的用户门户时，请使用 *https:\//portal.local.azurestack.external* URL。 从用于注册 Azure Stack 中心的域以外的其他域登录到 Azure Stack 中心门户时，必须将用于注册 Azure Stack 中心的域名追加到门户 url。 例如，如果已向 fabrikam.onmicrosoft.com 注册 Azure Stack 集线器并且登录的用户帐户为admin@contoso.com，则用于登录用户门户的 URL 应为： https：/portal.local.azurestack.external/fabrikam.onmicrosoft.com.\/

### <a name="guest-users"></a>来宾用户

来宾用户是其他目录租户中的用户帐户，这些用户已获得目录中资源的访问权限。 若要支持来宾用户，请使用 Azure AD 并启用多租户支持。 若已启用支持，可以邀请来宾用户访问目录租户中的资源，方便他们与外部组织协作。

为了邀请来宾用户，云操作员和用户可以使用[AZURE AD B2B 协作](/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)。 受邀的用户可从你的目录访问文档、资源和应用，并保持对你自己的资源和数据的控制。

可以作为来宾用户登录到其他组织的目录租户。 为此，请将该组织的目录名称追加到门户 URL。 例如，如果你属于 Contoso 组织并且要登录到 Fabrikam 目录，则使用 https：\//portal.local.azurestack.external/fabrikam.onmicrosoft.com。

### <a name="apps"></a>应用

可以向 Azure AD 或 AD FS 注册应用，然后将其提供给组织中的用户。

应用包括：

- **Web 应用**：示例包括 Azure 门户和 Azure 资源管理器。 它们支持 Web API 调用。
- **本机客户端**：示例包括 Azure PowerShell、Visual Studio 和 Azure CLI。

应用可支持两种租户：

- **单租户**：仅支持注册了应用的同一目录中的用户和服务。

  > [!NOTE]
  > 由于 AD FS 仅支持单个目录，因此在 AD FS 拓扑中创建的应用设计为单租户应用。

- **多租户**：支持通过注册应用程序的目录和其他租户目录使用用户和服务。 另一个租户目录（另一个 Azure AD 租户）的用户可以使用多租户应用登录到应用。

  有关多租户的详细信息，请参阅[启用多租户](azure-stack-enable-multitenancy.md)。

  有关如何开发多租户应用的详细信息，请参阅[多租户应用](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview)。

注册应用时，请创建两个对象：

- **应用程序对象**：应用程序在所有租户中的全局表示形式。 此关系是与软件应用的一对一关系，且只存在于应用首先注册到的目录中。

- **服务主体对象**：为应用程序在首次注册时所在的目录中创建的凭据。 服务主体也是在使用该应用的其他每个租户的目录中创建的。 此关系可以是与软件应用的一对多关系。

有关应用和服务主体的详细信息，请参阅 [Azure Active Directory 中的应用程序和服务主体对象](/azure/active-directory/develop/active-directory-application-objects)。

### <a name="service-principals"></a>服务主体

服务主体是应用或服务的一组*凭据*，用于授予对 Azure Stack 中心资源的访问权限。 使用服务主体可将应用权限与应用用户权限区分开来。

服务主体是在使用应用的每个租户中创建的。 服务主体建立的标识用于登录和访问受该租户保护的资源（例如用户）。

- 单租户应用在其首次创建所在的目录中只有一个服务主体。 此服务主体在应用注册期间创建并允许用户使用。
- 多租户 Web 应用或 API 将在每个租户中创建一个服务主体，前提是该租户的用户同意使用此应用。

服务主体的凭据可以是通过 Azure 门户生成的密钥，也可以是证书。 证书的使用很适合自动化，因为证书被视为比密钥更安全。

> [!NOTE]
> 将 AD FS 与 Azure Stack 中心一起使用时，只有管理员才能创建服务主体。 使用 AD FS 时，服务主体需要证书，并需要通过特权终结点 (PEP) 来创建。 有关详细信息，请参阅[使用应用标识访问资源](azure-stack-create-service-principals.md)。

若要了解 Azure Stack 中心的服务主体，请参阅[创建服务主体](azure-stack-create-service-principals.md)。

### <a name="services"></a>服务

与标识提供者交互的 Azure Stack 中心中的服务将注册为具有标识提供者的应用。 如同应用，注册可让服务在标识系统中进行身份验证。

所有 Azure 服务都使用 [OpenID Connect](/azure/active-directory/develop/active-directory-protocols-openid-connect-code) 协议和 [JSON Web 令牌](/azure/active-directory/develop/active-directory-token-and-claims)来建立其标识。 由于 Azure AD 和 AD FS 使用的协议一致，因此可以使用 [Azure Active Directory 身份验证库](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) 在本地环境或 Azure 中进行身份验证（在联网场景中）。 还可以通过 ADAL 使用 Azure PowerShell 和 Azure CLI 等工具进行跨云的和本地的资源管理。

### <a name="identities-and-your-identity-system"></a>标识和标识系统

Azure Stack 中心的标识包括用户帐户、组和服务主体。

安装 Azure Stack 集线器时，多个内置应用和服务会自动在目录租户中向标识提供者注册。 有些注册的服务用于管理。 还有些服务供用户使用。 默认注册提供核心服务标识，此类标识可彼此交互，以及与今后添加的标识交互。

如果设置采用多租户的 Azure AD，则有些应用会传播到新的目录。

## <a name="authentication-and-authorization"></a>身份验证和授权

### <a name="authentication-by-apps-and-users"></a>按应用和用户进行身份验证

![Azure Stack 中心层之间的标识](media/azure-stack-identity-overview/identity-layers.svg)

对于应用程序和用户，由四个层描述 Azure Stack 中心的体系结构。 各层之间的交互可以使用不同类型的身份验证。

|层    |各层之间的身份验证  |
|---------|---------|
|工具与客户端，例如管理员门户     | 若要访问或修改 Azure Stack 集线器中的资源，工具和客户端将使用[JSON Web 令牌](/azure/active-directory/develop/active-directory-token-and-claims)来调用 Azure 资源管理器。 <br>Azure 资源管理器验证 JSON Web 令牌，并查看已颁发令牌中的*声明*，以估计用户或服务主体在 Azure Stack 中心的授权级别。 |
|Azure 资源管理器及其核心服务     |Azure 资源管理器与资源提供程序通信，以传输用户的通信。 <br> 传输通过 [Azure 资源管理器模板](/azure-stack/user/azure-stack-arm-templates)使用直接命令式调用或声明式调用。****|
|资源提供程序     |传递给资源提供程序的调用通过基于证书的身份验证进行保护。 <br>随后，Azure 资源管理器和资源提供程序持续通过 API 通信。 对于从 Azure 资源管理器 收到的每个调用，资源提供程序使用该证书来验证调用。|
|基础结构和业务逻辑     |资源提供程序使用所选的身份验证模式与业务逻辑和基础结构通信。 Azure Stack 集线器随附的默认资源提供程序使用 Windows 身份验证来保护此通信。|

![身份验证所需的信息](media/azure-stack-identity-overview/authentication.svg)

### <a name="authenticate-to-azure-resource-manager"></a>对 Azure 资源管理器进行身份验证

若要在标识提供者中进行身份验证并接收 JSON Web 令牌，必须提供以下信息：

1. **标识系统（颁发机构）的 URL**：可用于访问标识提供者的 URL。 例如， *https：\//login.windows.net*。
2. **Azure 资源管理器的应用 ID URI**：向标识提供者注册的 azure 资源管理器的唯一标识符。 这对于每个 Azure Stack 中心安装也是唯一的。
3. **凭据**：用于在标识提供者中进行身份验证的凭据。
4. **Azure 资源管理器 的 URL**：该 URL 是 Azure 资源管理器服务的位置。 例如， *https\/：/management.azure.com*或*https：\//management.local.azurestack.external*。

当主体（客户端、应用或用户）发出访问资源所需的身份验证请求时，该请求必须包含：

- 主体的凭据。
- 主体想要访问的资源的应用 ID URI。

凭据已由标识提供者验证。 标识提供者还会验证应用 ID URI 是否用于已注册的应用，以及主体是否拥有适当的权限，可获取该资源的令牌。 如果请求有效，则授予 JSON Web 令牌。

然后，必须将此令牌传入发往 Azure 资源管理器的请求的标头中。 Azure 资源管理器执行以下操作（不遵循特定的顺序）：

- 验证 *issuer* (iss) 声明，确认令牌来自正确的标识提供者。
- 验证 *audience* (aud) 声明，确认令牌已颁发给 Azure 资源管理器。
- 验证 JSON Web 令牌是否已使用通过 OpenID 配置的证书进行签名，并且为 Azure 资源管理器所知。
- 检查 *issued at* (iat) 和 *expiration* (exp) 声明，确认令牌处于活动状态并且可以接受。

完成所有验证后，Azure 资源管理器使用对象 ID (oid) 和 *groups* 声明来生成主体可以访问的资源列表。**

![令牌交换协议示意图](media/azure-stack-identity-overview/token-exchange.svg)

> [!NOTE]
> 部署后，不需要 Azure Active Directory 全局管理员权限。 但是，某些操作可能需要全局管理员凭据（例如，资源提供程序安装程序脚本或需要授予权限的新功能）。 可以临时复原帐户的全局管理员权限，也可以使用单独的全局管理员帐户（该帐户应是默认提供程序订阅的所有者）。**

### <a name="use-role-based-access-control"></a>使用基于角色的访问控制

Azure Stack 中心的基于角色的访问控制（RBAC）与 Microsoft Azure 中的实现一致。 可以通过将相应的 RBAC 角色分配给用户、组和应用，来管理资源访问权限。 有关如何将 RBAC 与 Azure Stack 中心一起使用的信息，请参阅以下文章：

- [Azure 门户中基于角色的访问控制入门](/azure/role-based-access-control/overview)。
- [使用基于角色的访问控制来管理对 Azure 订阅资源的访问权限](/azure/role-based-access-control/role-assignments-portal)。
- [为 Azure 基于角色的访问控制创建自定义角色](/azure/role-based-access-control/custom-roles)。
- 在 Azure Stack Hub 中[管理基于角色的访问控制](azure-stack-manage-permissions.md)。

### <a name="authenticate-with-azure-powershell"></a>使用 Azure PowerShell 进行身份验证

有关使用 Azure PowerShell 通过 Azure Stack 中心进行身份验证的详细信息，请参阅[配置 Azure Stack 集线器用户的 PowerShell 环境](../user/azure-stack-powershell-configure-user.md)。

### <a name="authenticate-with-azure-cli"></a>使用 Azure CLI 进行身份验证

有关使用 Azure PowerShell 向 Azure Stack 中心进行身份验证的信息，请参阅[安装和配置与 Azure Stack 集线器一起使用的 Azure CLI](/azure-stack/user/azure-stack-version-profiles-azurecli2)。

## <a name="next-steps"></a>后续步骤

- [标识体系结构](azure-stack-identity-architecture.md)
- [数据中心集成 - 标识](azure-stack-integrate-identity.md)
