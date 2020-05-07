---
title: 在 Azure Stack Hub 上部署 Azure 应用服务的先决条件
description: 了解在部署 Azure Stack Hub 上的 Azure 应用服务之前需要完成的前提步骤。
author: BryanLa
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/13/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: c3512a441116f57f74921cec38d5937b89a1978c
ms.sourcegitcommit: d04b8f2d431ec1e12bcf5aa976fb06ec109caa25
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82868477"
---
# <a name="prerequisites-for-deploying-app-service-on-azure-stack-hub"></a>在 Azure Stack Hub 上部署应用服务的先决条件

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

在 Azure Stack Hub 上部署 Azure 应用服务之前，必须完成本文中的先决条件步骤。

## <a name="before-you-get-started"></a>开始操作之前 

本部分列出了集成系统和 Azure Stack 开发工具包（ASDK）部署的先决条件。

### <a name="resource-provider-prerequisites"></a>资源提供程序先决条件

[!INCLUDE [Common RP prerequisites](../includes/marketplace-resource-provider-prerequisites.md)]

### <a name="installer-and-helper-scripts"></a>安装程序和帮助器脚本

1. 下载[Azure Stack 中心部署帮助程序脚本上的应用服务](https://aka.ms/appsvconmashelpers)。
2. 下载[Azure Stack 中心安装程序上的应用服务](https://aka.ms/appsvconmasinstaller)。
3. 提取帮助器脚本 .zip 文件中的文件。 会提取以下文件和文件夹：

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - 模块文件夹
     - GraphAPI.psm1

<!-- MultiNode Only --->
## <a name="certificates-and-server-configuration-integrated-systems"></a>证书和服务器配置（集成系统）

本部分列出了集成系统部署的先决条件。 

### <a name="certificate-requirements"></a>证书要求

若要在生产环境中运行资源提供程序，必须提供以下证书：

- 默认域证书
- API 证书
- 发布证书
- 标识证书

#### <a name="default-domain-certificate"></a>默认域证书

默认域证书放在前端角色上。 对 Azure 应用服务发出通配符或默认域请求的用户应用使用此证书。 该证书还用于源代码管理操作 (Kudu)。

该证书必须采用 .pfx 格式，并且应该是包含三个使用者的通配符证书。 此要求允许一个证书同时涵盖用于源代码管理操作的默认域和 SCM 终结点。

| 格式 | 示例 |
| --- | --- |
| `*.appservice.<region>.<DomainName>.<extension>` | `*.appservice.redmond.azurestack.external` |
| `*.scm.appservice.<region>.<DomainName>.<extension>` | `*.scm.appservice.redmond.azurestack.external` |
| `*.sso.appservice.<region>.<DomainName>.<extension>` | `*.sso.appservice.redmond.azurestack.external` |

#### <a name="api-certificate"></a>API 证书

API 证书放在“管理”角色上。 资源提供程序使用它来帮助保护 API 调用。 用于发布的证书必须包含匹配 API DNS 条目的使用者。

| 格式 | 示例 |
| --- | --- |
| api.appservice.\<region\>.\<DomainName\>.\<extension\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>发布证书

“发布者”角色的证书在应用所有者上传内容时保护其 FTPS 流量。 用于发布的证书必须包含匹配 FTPS DNS 条目的使用者。

| 格式 | 示例 |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>标识证书

标识应用的证书可以实现：

- Azure Active Directory （Azure AD）或 Active Directory 联合身份验证服务（AD FS）目录、Azure Stack 中心和应用服务之间的集成，以支持与计算资源提供程序的集成。
- 单一登录方案适用于 Azure Stack 中心 Azure App Service 内的高级开发人员工具。

用于标识的证书必须包含匹配以下格式的使用者。

| 格式 | 示例 |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

### <a name="validate-certificates"></a>验证证书

在部署应用服务资源提供程序之前，应该使用[PowerShell 库](https://aka.ms/AzsReadinessChecker)中提供的 Azure Stack 集线器就绪检查程序工具[来验证要使用的证书](azure-stack-validate-pki-certs.md)。 Azure Stack 集线器就绪检查程序工具验证生成的 PKI 证书是否适用于应用服务部署。

最佳做法是，在使用任何必需的[Azure Stack 中心 PKI 证书](azure-stack-pki-certs.md)时，应根据需要计划足够的时间来测试和重新颁发证书。

### <a name="prepare-the-file-server"></a>准备文件服务器

Azure 应用服务需要使用文件服务器。 在生产部署中，必须将文件服务器配置为高度可用，且能够应对故障。

#### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>高可用性文件服务器和 SQL Server 的快速入门模板

[参考体系结构快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha)现已推出，可用于部署文件服务器和 SQL Server。 此模板在虚拟网络中支持 Active Directory 基础结构，配置为支持 Azure Stack Hub 上 Azure App Service 的高度可用部署。

> [!NOTE]
> 集成的系统实例必须能够从 GitHub 下载资源，才能完成部署。

#### <a name="steps-to-deploy-a-custom-file-server"></a>部署自定义文件服务器的步骤

>[!IMPORTANT]
> 如果选择在现有虚拟网络中部署应用服务，应将文件服务器部署到独立于应用服务的子网中。

>[!NOTE]
> 如果已选择使用上述任一快速入门模板部署文件服务器，则可以跳过此部分，因为在部署模板的过程中已配置文件服务器。

##### <a name="provision-groups-and-accounts-in-active-directory"></a>在 Active Directory 中预配组和帐户

1. 创建以下 Active Directory 全局安全组：

   - FileShareOwners
   - FileShareUsers

2. 创建以下 Active Directory 帐户作为服务帐户：

   - FileShareOwner
   - FileShareUser

   根据安全最佳做法，这些帐户（以及所有 Web 角色）的用户应该各不相同，并采用强用户名和密码。 根据以下条件设置密码：

   - 启用“密码永不过期”。****
   - 启用“用户不能更改密码”。****
   - 禁用“用户在下次登录时必须更改密码”。****

3. 如下所述将帐户添加到组成员身份：

   - 将 **FileShareOwner** 添加到 **FileShareOwners** 组。
   - 将 **FileShareUser** 添加到 **FileShareUsers** 组。

##### <a name="provision-groups-and-accounts-in-a-workgroup"></a>在工作组中预配组和帐户

>[!NOTE]
> 配置文件服务器时，请通过**管理员命令提示符**运行以下所有命令。 <br>***请勿使用 PowerShell。***

使用 Azure 资源管理器模板时已创建用户。

1. 运行以下命令创建 FileShareOwner 和 FileShareUser 帐户。 将 `<password>` 替换为自己的值。

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. 运行以下 WMIC 命令，将帐户密码设为永不过期：

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. 创建本地组 FileShareUsers 和 FileShareOwners，并将第一个步骤中创建的帐户添加到其中：

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

#### <a name="provision-the-content-share"></a>预配内容共享

内容共享包含租户网站内容。 在单个文件服务器上预配内容共享的过程与在 Active Directory 和工作组环境中相同。 但是对于 Active Directory 中的故障转移群集则不同。

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>在单个文件服务器上（Active Directory 或工作组）预配内容共享

在单个文件服务器上，在权限提升的命令提示符下运行以下命令。 将 `C:\WebSites` 的值替换为环境中的相应路径。

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>配置共享访问控制

在文件服务器上或故障转移群集节点（当前的群集资源所有者）上，在权限提升的命令提示符下运行以下命令。 将斜体显示的值替换为环境特定的值。

#### <a name="active-directory"></a>Active Directory

```DOS
set DOMAIN=<DOMAIN>
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant %DOMAIN%\FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

#### <a name="workgroup"></a>工作组

```DOS
set WEBSITES_FOLDER=C:\WebSites
icacls %WEBSITES_FOLDER% /reset
icacls %WEBSITES_FOLDER% /grant Administrators:(OI)(CI)(F)
icacls %WEBSITES_FOLDER% /grant FileShareOwners:(OI)(CI)(M)
icacls %WEBSITES_FOLDER% /inheritance:r
icacls %WEBSITES_FOLDER% /grant FileShareUsers:(CI)(S,X,RA)
icacls %WEBSITES_FOLDER% /grant *S-1-1-0:(OI)(CI)(IO)(RA,REA,RD)
```

### <a name="prepare-the-sql-server-instance"></a>准备 SQL Server 实例

> [!NOTE]
> 如果已选择为高度可用的文件服务器和 SQL Server 部署快速入门模板，则可以跳过本部分，因为模板会在 HA 配置中部署和配置 SQL Server。

有关 Azure Stack 中心承载和计量数据库的 Azure App Service，必须准备一个 SQL Server 实例来容纳应用服务数据库。

对于生产和高可用性目的，应使用完整版本的 SQL Server 2014 SP2 或更高版本，启用混合模式身份验证，并在[高可用性配置](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server)中部署。

Azure Stack 中心的 Azure App Service 的 SQL Server 实例必须可从所有应用服务角色进行访问。 你可以在 Azure Stack 中心的默认提供程序订阅中部署 SQL Server。 或者，你可以使用组织中的现有基础结构（只要与 Azure Stack 集线器建立连接即可）。 如果使用 Azure 市场映像，请记得相应地配置防火墙。

> [!NOTE]
> 通过 Marketplace 管理功能可以使用许多 SQL IaaS VM 映像。 在使用市场项部署 VM 之前，请确保下载最新版本的 SQL IaaS 扩展。 SQL 映像与 Azure 中提供的 SQL VM 相同。 对于从这些映像创建的 SQL VM，IaaS 扩展和相应的门户增强功能可提供自动修补和备份等功能。
>
> 对于任何 SQL Server 角色，可以使用默认实例或命名实例。 如果使用命名实例，请务必手动启动 SQL Server Browser 服务并打开端口 1434。

应用服务安装程序将检查以确保 SQL Server 已启用数据库包含。 若要在将托管应用服务数据库的 SQL Server 上启用数据库包含，请运行以下 SQL 命令：

```sql
sp_configure 'contained database authentication', 1;
GO
RECONFIGURE;
GO
```

<!-- ASDK Only --->
## <a name="certificates-and-server-configuration-asdk"></a>证书和服务器配置（ASDK）

本部分列出了 ASDK 部署的先决条件。 

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Azure 应用服务的 ASDK 部署所需的证书

*Create-appservicecerts.ps1*脚本与 Azure Stack 中心证书颁发机构合作，以创建应用服务所需的四个证书。

| 文件名 | 使用 |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | 应用服务默认 SSL 证书 |
| api.appservice.local.azurestack.external.pfx | 应用服务 API SSL 证书 |
| ftp.appservice.local.azurestack.external.pfx | 应用服务发布者 SSL 证书 |
| sso.appservice.local.azurestack.external.pfx | 应用服务标识应用程序证书 |

若要创建证书，请执行以下步骤：

1. 使用 AzureStack\AzureStackAdmin 帐户登录到 ASDK 主机。
2. 打开提升的 PowerShell 会话。
3. 从帮助器脚本提取到的文件夹运行 *Create-AppServiceCerts.ps1* 脚本。 此脚本在应用服务所需的、用于创建证书的脚本所在的同一文件夹中创建四个证书。
4. 输入密码来保护 .pfx 文件，并记下该密码。 稍后必须在 Azure Stack 集线器安装程序上的应用服务中输入它。

#### <a name="create-appservicecertsps1-script-parameters"></a>Create-AppServiceCerts.ps1 脚本参数

| 参数 | 必需还是可选 | 默认值 | 说明 |
| --- | --- | --- | --- |
| pfxPassword | 必选 | Null | 帮助保护证书私钥的密码 |
| DomainName | 必选 | local.azurestack.external | Azure Stack 中心区域和域后缀 |

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>用于部署 ASDK 上的 Azure应用服务的文件服务器快速入门模板。

如果只部署 ASDK，则可以使用[示例 Azure 资源管理器部署模板](https://aka.ms/appsvconmasdkfstemplate)来部署已配置的单节点文件服务器。 单节点文件服务器位于工作组中。  

> [!NOTE]
> ASDK 实例必须能够从 GitHub 下载资源，才能完成部署。

### <a name="sql-server-instance"></a>SQL Server 实例

有关 Azure Stack 中心承载和计量数据库的 Azure App Service，必须准备一个 SQL Server 实例来容纳应用服务数据库。

对于 ASDK 部署，可以使用 SQL Server Express 2014 SP2 或更高版本。 SQL Server 必须配置为支持**混合模式**身份验证，因为 Azure Stack 中心的应用**服务不支持**Windows 身份验证。

Azure Stack 中心的 Azure App Service 的 SQL Server 实例必须可从所有应用服务角色进行访问。 你可以在 Azure Stack 中心的默认提供程序订阅中部署 SQL Server。 或者，你可以使用组织中的现有基础结构（只要与 Azure Stack 集线器建立连接即可）。 如果使用 Azure 市场映像，请记得相应地配置防火墙。

> [!NOTE]
> 通过 Marketplace 管理功能可以使用许多 SQL IaaS VM 映像。 在使用市场项部署 VM 之前，请确保下载最新版本的 SQL IaaS 扩展。 SQL 映像与 Azure 中提供的 SQL VM 相同。 对于从这些映像创建的 SQL VM，IaaS 扩展和相应的门户增强功能可提供自动修补和备份等功能。
>
> 对于任何 SQL Server 角色，可以使用默认实例或命名实例。 如果使用命名实例，请务必手动启动 SQL Server Browser 服务并打开端口 1434。

应用服务安装程序将检查以确保 SQL Server 已启用数据库包含。 若要在将托管应用服务数据库的 SQL Server 上启用数据库包含，请运行以下 SQL 命令：

```sql
sp_configure 'contained database authentication', 1;
GO
RECONFIGURE;
GO

```

## <a name="licensing-concerns-for-required-file-server-and-sql-server"></a>所需文件服务器和 SQL Server 的许可问题

Azure Stack 中心的 Azure App Service 需要文件服务器和 SQL Server 才能运行。 你可以使用位于 Azure Stack 中心部署之外的预先存在的资源，或者在其 Azure Stack 中心默认提供程序订阅中部署资源。

如果选择在 Azure Stack 中心默认提供程序订阅中部署资源，则这些资源的许可证（Windows Server 许可证和 SQL Server 许可证）将包括在 Azure Stack 中心的 Azure App Service 的成本中，并受以下约束的限制：

- 基础结构部署到“默认提供程序订阅”中；
- Azure Stack 中心资源提供程序的 Azure App Service 专门使用基础结构。 对于其他工作负荷（例如： SQL-RP 的其他资源提供程序，例如 SQL-RP）或租户（例如：需要数据库的租户应用程序），允许使用此基础结构。

## <a name="operational-responsibility-of-file-and-sql-servers"></a>文件和 sql server 的操作责任

云操作员负责维护和操作文件服务器，并 SQL Server。  资源提供程序不管理这些资源。  云操作员负责备份应用服务数据库和租户内容文件共享。

## <a name="retrieve-the-azure-resource-manager-root-certificate-for-azure-stack-hub"></a>检索 Azure Stack 中心的 Azure 资源管理器根证书

在计算机上打开已提升权限的 PowerShell 会话，该计算机可以访问 Azure Stack 中心集成系统或 ASDK 主机上的特权终结点。

从帮助器脚本提取到的文件夹运行 *Get-AzureStackRootCert.ps1* 脚本。 此脚本在应用服务所需的、用于创建证书的脚本所在的同一文件夹中创建一个根证书。

运行以下 PowerShell 命令时，必须为 AzureStack\CloudAdmin 提供特权终结点和凭据。

```powershell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get-AzureStackRootCert.ps1 脚本参数

| 参数 | 必需还是可选 | 默认值 | 说明 |
| --- | --- | --- | --- |
| PrivilegedEndpoint | 必选 | AzS-ERCS01 | 特权终结点 |
| CloudAdminCredential | 必选 | AzureStack\CloudAdmin | Azure Stack 中心云管理员的域帐户凭据 |

## <a name="network-and-identity-configuration"></a>网络和标识配置

### <a name="virtual-network"></a>虚拟网络

> [!NOTE]
> 自定义虚拟网络的预创建是可选的，因为 Azure Stack 中心的 Azure App Service 可以创建所需的虚拟网络，但随后需要通过公共 IP 地址与 SQL 和文件服务器进行通信。  如果你使用应用服务 HA 文件服务器和 SQL Server 快速入门模板来部署必备的 SQL 和文件服务器资源，则模板还将部署虚拟网络。

使用 Azure Stack 中心 Azure App Service 可以将资源提供程序部署到现有虚拟网络，也可以在部署过程中创建虚拟网络。 使用现有的虚拟网络，可以使用内部 Ip 连接到文件服务器，并 SQL Server Azure Stack 集线器上的 Azure App Service 所需的。 在 Azure Stack 集线器上安装 Azure App Service 之前，必须为虚拟网络配置以下地址范围和子网：

虚拟网络 - /16

子网

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

>[!IMPORTANT]
> 如果选择在现有虚拟网络中部署应用服务，应将 SQL Server 部署到独立于应用服务和文件服务器的子网中。
>

### <a name="create-an-identity-application-to-enable-sso-scenarios"></a>创建标识应用程序以启用 SSO 方案

Azure App Service 使用标识应用程序（服务主体）来支持以下操作：

- 辅助角色层上的虚拟机规模集集成。
- Azure Functions 门户和高级开发人员工具（Kudu）的 SSO。

根据 Azure Stack 集线器使用的标识提供者、Azure Active Directory （Azure AD）或 Active Directory 联合身份验证服务（ADFS），必须按照以下相应步骤来创建服务主体，以供 Azure App Service 中心资源提供程序上的 Azure Stack 使用。

::: zone pivot="state-connected"
#### <a name="create-an-azure-ad-app"></a>创建 Azure AD 应用

按照以下步骤在 Azure AD 租户中创建服务主体：

1. 以 azurestack\AzureStackAdmin 身份打开 PowerShell 实例。
1. 转到在[先决条件步骤](azure-stack-app-service-before-you-get-started.md)中下载并提取的脚本所在的位置。
1. [为 Azure Stack 集线器安装 PowerShell](azure-stack-powershell-install.md)。
1. 运行 **Create-AADIdentityApp.ps1** 脚本。 出现提示时，输入要用于 Azure Stack 中心部署的 Azure AD 租户 ID。 例如，输入**myazurestack.onmicrosoft.com**。
1. 在“凭据”窗口中，输入 Azure AD 服务管理帐户和密码。**** 选择“确定”  。
1. 输入[前面创建的证书](azure-stack-app-service-before-you-get-started.md)的证书文件路径和证书密码。 默认情况下值，为此步骤创建的证书是 **sso.appservice.local.azurestack.external.pfx**。
1. 请记下 PowerShell 输出中返回的应用程序 ID。 使用以下步骤中的 ID 可为应用程序的权限以及在安装过程中提供许可。 
1. 打开新的浏览器窗口，以 Azure Active Directory 服务管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
1. 打开 Azure Active Directory 服务。
1. 在左窗格中选择 "**应用注册**"。
1. 搜索在步骤7中记下的应用程序 ID。 
1. 从列表中选择 "应用服务应用程序注册"。
1. 在左窗格中选择 " **API 权限**"。
1. 选择 "**为\<租户\>授予管理员许可**"，其中\<租户\>是 Azure AD 租户的名称。 选择 **"是"**，确认同意授予。

```powershell
    Create-AADIdentityApp.ps1
```

| 参数 | 必需还是可选 | 默认值 | 说明 |
| --- | --- | --- | --- |
| DirectoryTenantName | 必选 | Null | Azure AD 租户 ID。 提供 GUID 或字符串。 例如，myazureaaddirectory.onmicrosoft.com。 |
| AdminArmEndpoint | 必须 | Null | Azure 资源管理器管理终结点。 例如 adminmanagement.local.azurestack.external。 |
| TenantARMEndpoint | 必选 | Null | Azure 资源管理器租户终结点。 例如 management.local.azurestack.external。 |
| AzureStackAdminCredential | 必选 | Null | Azure AD 服务管理员凭据。 |
| CertificateFilePath | 必选 | Null | 前面生成的标识应用程序证书文件的**完整路径**。 |
| CertificatePassword | 必选 | Null | 帮助保护证书私钥的密码。 |
| 环境 | 可选 | AzureCloud | 其中目标 Azure Active Directory Graph 服务可用的受支持云环境的名称。  允许的值： "AzureCloud"、"AzureChinaCloud"、"AzureUSGovernment"、"AzureGermanCloud"。|
::: zone-end

#### <a name="create-an-adfs-app"></a>创建 ADFS 应用

1. 以 azurestack\AzureStackAdmin 身份打开 PowerShell 实例。
1. 转到在[先决条件步骤](azure-stack-app-service-before-you-get-started.md)中下载并提取的脚本所在的位置。
1. [为 Azure Stack 集线器安装 PowerShell](azure-stack-powershell-install.md)。
1. 运行 **Create-ADFSIdentityApp.ps1** 脚本。
1. 在“凭据”窗口中，输入 AD FS 云管理帐户和密码。**** 选择“确定”  。
1. 提供[前面创建的证书](azure-stack-app-service-before-you-get-started.md)的证书文件路径和证书密码。 默认情况下值，为此步骤创建的证书是 **sso.appservice.local.azurestack.external.pfx**。

```powershell
    Create-ADFSIdentityApp.ps1
```

| 参数 | 必需还是可选 | 默认值 | 说明 |
| --- | --- | --- | --- |
| AdminArmEndpoint | 必须 | Null | Azure 资源管理器管理终结点。 例如 adminmanagement.local.azurestack.external。 |
| PrivilegedEndpoint | 必须 | Null | 特权终结点。 例如 AzS-ERCS01。 |
| CloudAdminCredential | 必选 | Null | Azure Stack 中心云管理员的域帐户凭据。 例如 Azurestack\CloudAdmin。 |
| CertificateFilePath | 必选 | Null | 标识应用程序的证书 PFX 文件的**完整路径**。 |
| CertificatePassword | 必选 | Null | 帮助保护证书私钥的密码。 |

<!--Connected/Disconnected-->

### <a name="download-items-from-the-azure-marketplace"></a>从 Azure 市场下载项

Azure Stack 集线器上的 Azure App Service 需要[从 Azure Marketplace 下载](azure-stack-download-azure-marketplace-item.md)项目，使其在 Azure Stack 中心市场中可用。 必须先下载这些项，然后才能在 Azure Stack 中心开始部署或升级 Azure App Service：

<!-- Connected --->
::: zone pivot="state-connected"
> [!IMPORTANT]
> 不支持将 Windows Server Core 用于 Azure Stack 中心的 Azure App Service。
>
> 请勿将评估映像用于生产部署。
>
1. **最新版本的 Windows Server 2016 DATACENTER VM 映像**。
::: zone-end

::: zone pivot="state-disconnected"
<!-- Disconnected --->
1. 已**激活 Microsoft.Net 3.5.1 SP1 的 Windows Server 2016 Datacenter FULL VM 映像**。  Azure Stack 中心的 Azure App Service 要求在用于部署的映像上激活 Microsoft .NET 3.5.1 SP1。 Marketplace 联合的 Windows Server 2016 映像未启用此功能，并且在断开连接的环境中，无法通过 DISM 访问 Microsoft 更新来下载要安装的程序包。 因此，你必须创建并使用 Windows Server 2016 映像，并将此功能预启用为断开连接的部署。

   有关创建自定义映像并将其添加到 Marketplace 的详细信息，请参阅[将自定义 VM 映像添加到 Azure Stack 集线器](azure-stack-add-vm-image.md)。 将映像添加到 Marketplace 时，请确保指定以下属性：

   - 发布服务器 = MicrosoftWindowsServer
   - 提议 = WindowsServer
   - SKU = 2016-Datacenter
   - 版本 = 指定 "最新" 版本

::: zone-end

<!-- For All --> 
2. **自定义脚本扩展1.9.1 或更高版本**。 此项为 VM 扩展。

## <a name="next-steps"></a>后续步骤

[安装应用服务资源提供程序](azure-stack-app-service-deploy.md)
