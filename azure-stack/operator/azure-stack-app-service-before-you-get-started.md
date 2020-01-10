---
title: 在 Azure Stack 集线器上部署应用服务的先决条件 |Microsoft Docs
description: 在 Azure Stack 集线器上部署应用服务之前，请先了解要完成的先决条件步骤。
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/08/2020
ms.openlocfilehash: 53256371be8903ac379cee088be2bd3b7208b2cb
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75812376"
---
# <a name="prerequisites-for-deploying-app-service-on-azure-stack-hub"></a>在 Azure Stack 集线器上部署应用服务的先决条件

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

在 Azure Stack 中心部署 Azure App Service 之前，必须完成本文中的先决条件步骤。

> [!IMPORTANT]
> 在部署 Azure App Service 1.6 之前，将1904更新应用到 Azure Stack 集线器集成系统或部署最新的 Azure Stack 开发工具包（ASDK）。

## <a name="download-the-installer-and-helper-scripts"></a>下载安装程序和帮助程序脚本

1. 下载[Azure Stack 中心部署帮助程序脚本上的应用服务](https://aka.ms/appsvconmashelpers)。
2. 下载[Azure Stack 中心安装程序上的应用服务](https://aka.ms/appsvconmasinstaller)。
3. 从帮助程序脚本 .zip 文件中提取文件。 提取以下文件和文件夹：

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - 模块文件夹
     - GraphAPI.psm1

## <a name="download-items-from-the-azure-marketplace"></a>从 Azure Marketplace 下载项

Azure Stack 集线器上的 Azure App Service 需要[从 Azure Marketplace 下载](azure-stack-download-azure-marketplace-item.md)项目，使其在 Azure Stack 中心市场中可用。 必须先下载这些项，然后才能在 Azure Stack 中心开始部署或升级 Azure App Service：

1. 最新版本的 Windows Server 2016 Datacenter 虚拟机映像。
2. 自定义脚本扩展1.9.1 或更高版本。 这是一个虚拟机扩展。

## <a name="get-certificates"></a>获取证书

### <a name="azure-resource-manager-root-certificate-for-azure-stack-hub"></a>Azure Stack 中心的 Azure 资源管理器根证书

在计算机上打开已提升权限的 PowerShell 会话，该计算机可以访问 Azure Stack 中心集成系统或 ASDK 主机上的特权终结点。

从提取了帮助程序脚本的文件夹中运行*get-azurestackrootcert.ps1*脚本。 此脚本在应用服务创建证书所需的脚本所在的同一文件夹中创建根证书。

运行以下 PowerShell 命令时，必须提供特权终结点和 AzureStack\CloudAdmin. 的凭据

```powershell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get-azurestackrootcert.ps1 脚本参数

| 参数 | 必需还是可选 | 默认值 | Description |
| --- | --- | --- | --- |
| PrivilegedEndpoint | 需要 | AzS-ERCS01 | 特权终结点 |
| CloudAdminCredential | 需要 | AzureStack\CloudAdmin | Azure Stack 中心云管理员的域帐户凭据 |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Azure App Service 的 ASDK 部署所需的证书

*Create-appservicecerts.ps1*脚本与 Azure Stack 中心证书颁发机构合作，以创建应用服务所需的四个证书。

| 文件名 | 使用 |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | 应用服务默认 SSL 证书 |
| api.appservice.local.azurestack.external.pfx | 应用服务 API SSL 证书 |
| ftp.appservice.local.azurestack.external.pfx | 应用服务发布者 SSL 证书 |
| sso.appservice.local.azurestack.external.pfx | 应用服务标识应用程序证书 |

若要创建证书，请执行以下步骤：

1. 使用 AzureStack\AzureStackAdmin 帐户登录到 ASDK 主机。
2. 打开一个提升的 PowerShell 会话。
3. 从提取了帮助程序脚本的文件夹中运行*create-appservicecerts.ps1*脚本。 此脚本在应用服务创建证书所需的脚本所在的同一文件夹中创建四个证书。
4. 输入用于保护 .pfx 文件的密码，并记下它。 你必须在 Azure Stack Hub 安装程序上的应用服务中输入它。

#### <a name="create-appservicecertsps1-script-parameters"></a>Create-appservicecerts.ps1 脚本参数

| 参数 | 必需还是可选 | 默认值 | Description |
| --- | --- | --- | --- |
| pfxPassword | 需要 | Null | 帮助保护证书私钥的密码 |
| DomainName | 需要 | local.azurestack.external | Azure Stack 中心区域和域后缀 |

### <a name="certificates-required-for-azure-stack-hub-production-deployment-of-azure-app-service"></a>Azure App Service Azure Stack 中心生产部署所需的证书

若要在生产环境中运行资源提供程序，必须提供以下证书：

- 默认域证书
- API 证书
- 发布证书
- 标识证书

#### <a name="default-domain-certificate"></a>默认域证书

默认域证书放置在前端角色上。 要 Azure App Service 使用此证书的通配符或默认域请求的用户应用。 证书还用于源代码管理操作（Kudu）。

证书必须采用 .pfx 格式，并且应该是三个使用者的通配符证书。 此要求允许一个证书同时涵盖默认域和 SCM 终结点以进行源代码管理操作。

| 格式 | 示例 |
| --- | --- |
| `*.appservice.<region>.<DomainName>.<extension>` | `*.appservice.redmond.azurestack.external` |
| `*.scm.appservice.<region>.<DomainName>.<extension>` | `*.scm.appservice.redmond.azurestack.external` |
| `*.sso.appservice.<region>.<DomainName>.<extension>` | `*.sso.appservice.redmond.azurestack.external` |

#### <a name="api-certificate"></a>API 证书

API 证书放置在管理角色上。 资源提供程序使用它来帮助保护 API 调用。 用于发布的证书必须包含与 API DNS 条目匹配的使用者。

| 格式 | 示例 |
| --- | --- |
| \>\<区域。\<DomainName\>。\<扩展\> | api.appservice.redmond.azurestack.external |

#### <a name="publishing-certificate"></a>发布证书

发布者角色的证书会在应用所有者上传内容时保护 FTPS 流量。 用于发布的证书必须包含与 FTPS DNS 条目匹配的使用者。

| 格式 | 示例 |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>标识证书

标识应用的证书启用：

- Azure Active Directory （Azure AD）或 Active Directory 联合身份验证服务（AD FS）目录、Azure Stack 中心和应用服务之间的集成，以支持与计算资源提供程序的集成。
- 单一登录方案适用于 Azure Stack 中心 Azure App Service 内的高级开发人员工具。

标识的证书必须包含与以下格式匹配的使用者。

| 格式 | 示例 |
| --- | --- |
| \<区域\>。\<DomainName\>。\<扩展\> | sso.appservice.redmond.azurestack.external |

### <a name="validate-certificates"></a>验证证书

在部署应用服务资源提供程序之前，应该使用[PowerShell 库](https://aka.ms/AzsReadinessChecker)中提供的 Azure Stack 集线器就绪检查程序工具[来验证要使用的证书](azure-stack-validate-pki-certs.md#using-validated-certificates)。 Azure Stack 集线器就绪检查程序工具验证生成的 PKI 证书是否适用于应用服务部署。

最佳做法是，在使用任何必需的[Azure Stack 中心 PKI 证书](azure-stack-pki-certs.md)时，应根据需要计划足够的时间来测试和重新颁发证书。

## <a name="virtual-network"></a>虚拟网络

> [!NOTE]
> 自定义虚拟网络的预创建是可选的，因为 Azure Stack 中心的 Azure App Service 可以创建所需的虚拟网络，但随后需要通过公共 IP 地址与 SQL 和文件服务器进行通信。

使用 Azure Stack 中心 Azure App Service 可以将资源提供程序部署到现有虚拟网络，也可以在部署过程中创建虚拟网络。 使用现有的虚拟网络，可以使用内部 Ip 连接到文件服务器，并 SQL Server Azure Stack 集线器上的 Azure App Service 所需的。 在 Azure Stack 集线器上安装 Azure App Service 之前，必须为虚拟网络配置以下地址范围和子网：

虚拟网络-/16

子网

- ControllersSubnet/24
- ManagementServersSubnet/24
- FrontEndsSubnet/24
- PublishersSubnet/24
- WorkersSubnet/21

## <a name="licensing-concerns-for-required-file-server-and-sql-server"></a>必需的文件服务器和 SQL Server 的许可问题

Azure Stack 中心的 Azure App Service 需要文件服务器和 SQL Server 才能运行。  你可以使用位于 Azure Stack 中心部署之外的预先存在的资源，或者在其 Azure Stack 中心默认提供程序订阅中部署资源。

如果选择在 Azure Stack 中心默认提供程序订阅中部署资源，则这些资源的许可证（Windows Server 许可证和 SQL Server 许可证）将包括 Azure Stack Azure App Service 在受以下约束：

- 基础结构部署到**默认提供程序订阅**中;
- Azure Stack 中心资源提供程序的 Azure App Service 专门使用基础结构。  对于其他工作负荷（例如： SQL-RP 的其他资源提供程序，例如 SQL-RP）或租户（例如：需要数据库的租户应用程序），允许使用此基础结构。

## <a name="prepare-the-file-server"></a>准备文件服务器

Azure App Service 要求使用文件服务器。 对于生产部署，必须将文件服务器配置为高度可用，并能够处理故障。

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>用于在 ASDK 上部署 Azure App Service 的文件服务器快速入门模板。

对于 ASDK 部署，你可以使用[示例 Azure 资源管理器部署模板](https://aka.ms/appsvconmasdkfstemplate)来部署已配置的单节点文件服务器。 单节点文件服务器将位于工作组中。

### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>高可用文件服务器和 SQL Server 的快速入门模板

[参考体系结构快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha)现已推出，将部署文件服务器和 SQL Server。 此模板在虚拟网络中支持 Active Directory 基础结构，配置为支持 Azure Stack Hub 上 Azure App Service 的高度可用部署。

### <a name="steps-to-deploy-a-custom-file-server"></a>部署自定义文件服务器的步骤

>[!IMPORTANT]
> 如果选择在现有虚拟网络中部署应用服务，应将文件服务器部署到应用服务的单独子网中。

>[!NOTE]
> 如果已选择使用上述任一快速入门模板部署文件服务器，则可以跳过此部分，因为文件服务器是作为模板部署的一部分进行配置的。

#### <a name="provision-groups-and-accounts-in-active-directory"></a>在 Active Directory 中预配组和帐户

1. 创建以下 Active Directory 全局安全组：

   - FileShareOwners
   - FileShareUsers

2. 创建以下 Active Directory 帐户作为服务帐户：

   - FileShareOwner
   - FileShareUser

   作为安全性最佳实践，这些帐户（和所有 web 角色）的用户应是唯一的，并具有强用户名和密码。 在以下条件下设置密码：

   - 启用**密码永不过期**。
   - 启用**用户不能更改密码**。
   - 禁用 "**用户在下次登录时必须更改密码**"。

3. 将这些帐户添加到组成员身份，如下所示：

   - 将**FileShareOwner**添加到**FileShareOwners**组。
   - 将**FileShareUser**添加到**FileShareUsers**组。

#### <a name="provision-groups-and-accounts-in-a-workgroup"></a>在工作组中预配组和帐户

>[!NOTE]
> 配置文件服务器时，请在**管理员命令提示符**下运行以下所有命令。 <br>***请勿使用 PowerShell。***

使用 Azure 资源管理器模板时，用户已创建。

1. 运行以下命令以创建 FileShareOwner 和 FileShareUser 帐户。 将 `<password>` 替换为自己的值。

   ``` DOS
   net user FileShareOwner <password> /add /expires:never /passwordchg:no
   net user FileShareUser <password> /add /expires:never /passwordchg:no
   ```

2. 通过运行以下 WMIC 命令，将帐户的密码设置为永不过期：

   ``` DOS
   WMIC USERACCOUNT WHERE "Name='FileShareOwner'" SET PasswordExpires=FALSE
   WMIC USERACCOUNT WHERE "Name='FileShareUser'" SET PasswordExpires=FALSE
   ```

3. 创建本地组 FileShareUsers 和 FileShareOwners，并将第一步中的帐户添加到它们：

   ``` DOS
   net localgroup FileShareUsers /add
   net localgroup FileShareUsers FileShareUser /add
   net localgroup FileShareOwners /add
   net localgroup FileShareOwners FileShareOwner /add
   ```

#### <a name="provision-the-content-share"></a>预配内容共享

内容共享包含租户网站内容。 在单个文件服务器上设置内容共享的过程与 Active Directory 和工作组环境相同。 但 Active Directory 中的故障转移群集是不同的。

#### <a name="provision-the-content-share-on-a-single-file-server-active-directory-or-workgroup"></a>在单个文件服务器（Active Directory 或工作组）上设置内容共享

在一个文件服务器上，在提升了权限的命令提示符下运行以下命令。 将 `C:\WebSites` 的值替换为你的环境中的相应路径。

```DOS
set WEBSITES_SHARE=WebSites
set WEBSITES_FOLDER=C:\WebSites
md %WEBSITES_FOLDER%
net share %WEBSITES_SHARE% /delete
net share %WEBSITES_SHARE%=%WEBSITES_FOLDER% /grant:Everyone,full
```

### <a name="configure-access-control-to-the-shares"></a>配置对共享的访问控制

在文件服务器上或在故障转移群集节点上提升了权限的命令提示符下运行以下命令，该节点是当前群集资源所有者。 将斜体中的值替换为特定于您的环境的值。

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

## <a name="prepare-the-sql-server-instance"></a>准备 SQL Server 实例

>[!NOTE]
> 如果已选择为高可用文件服务器和 SQL Server 部署快速入门模板，则可以跳过此部分，因为模板会在 HA 配置中部署和配置 SQL Server。

有关 Azure Stack 中心承载和计量数据库的 Azure App Service，必须准备一个 SQL Server 实例来容纳应用服务数据库。

对于 ASDK 部署，可以使用 SQL Server Express 2014 SP2 或更高版本。 SQL Server 必须配置为支持**混合模式**身份验证，因为 Azure Stack 中心的应用**服务不支持**Windows 身份验证。

出于生产和高可用性目的，应使用完整版本的 SQL Server 2014 SP2 或更高版本，启用混合模式身份验证，并在[高可用性配置](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server)中部署。

Azure Stack 中心的 Azure App Service 的 SQL Server 实例必须可从所有应用服务角色进行访问。 你可以在 Azure Stack 中心的默认提供程序订阅中部署 SQL Server。 或者，你可以使用组织中的现有基础结构（只要与 Azure Stack 集线器建立连接即可）。 如果使用的是 Azure Marketplace 映像，请记住相应地配置防火墙。

> [!NOTE]
> 许多 SQL IaaS 虚拟机映像可通过 Marketplace 管理功能获得。 在使用 Marketplace 项部署 VM 之前，请确保始终下载最新版本的 SQL IaaS 扩展。 SQL 映像与 Azure 中提供的 SQL Vm 相同。 对于基于这些映像创建的 SQL Vm，IaaS 扩展和对应的门户增强功能提供了一些功能，例如自动修补和备份功能。
>
> 对于任何 SQL Server 角色，均可使用默认实例或命名实例。 如果使用命名实例，请务必手动启动 SQL Server Browser 服务，然后打开端口1434。

应用服务安装程序将进行检查以确保 SQL Server 已启用数据库包含。 若要在将承载应用服务数据库的 SQL Server 上启用数据库包含，请运行以下 SQL 命令：

```sql
sp_configure 'contained database authentication', 1;
GO
RECONFIGURE;
GO
```

>[!IMPORTANT]
> 如果选择在现有虚拟网络中部署应用服务，则应该将 SQL Server 部署到应用服务和文件服务器的单独子网中。
>

## <a name="create-an-azure-active-directory-app"></a>创建 Azure Active Directory 应用

配置 Azure AD 服务主体，以支持以下操作：

- 辅助角色层上的虚拟机规模集集成。
- Azure Functions 门户和高级开发人员工具的 SSO。

这些步骤仅适用于 Azure AD 保护的 Azure Stack 中心环境。

管理员必须将 SSO 配置为：

- 在应用服务中启用高级开发人员工具（Kudu）。
- 允许使用 Azure Functions 门户体验。

按照以下步骤在 Azure AD 租户中创建服务主体：

1. 将 PowerShell 实例作为 Azurestack\azurestackadmin 身份打开
2. 在[必备步骤](azure-stack-app-service-before-you-get-started.md)中，请参阅下载和提取的脚本的位置。
3. [为 Azure Stack 集线器安装 PowerShell](azure-stack-powershell-install.md)。
4. 运行**create-aadidentityapp.ps1**脚本。 出现提示时，输入要用于 Azure Stack 中心部署的 Azure AD 租户 ID。 例如，输入**myazurestack.onmicrosoft.com**。
5. 在 "**凭据**" 窗口中，输入 Azure AD 服务管理员帐户和密码。 选择“确定”。
6. 输入[先前创建的证书](azure-stack-app-service-before-you-get-started.md)的证书文件路径和证书密码。 默认情况下，为此步骤创建的证书为**appservice. test-azurestack**。
7. 记下在 PowerShell 输出中返回的应用程序 ID。 使用以下步骤中的 ID 可为应用程序的权限以及在安装过程中提供许可。 
8. 打开一个新的浏览器窗口，并以 Azure Active Directory 服务管理员的身份登录到[Azure 门户](https://portal.azure.com)。
9. 打开 Azure Active Directory 服务。
10. 在左窗格中选择 "**应用注册**"。
11. 搜索在步骤7中记下的应用程序 ID。 
12. 从列表中选择 "应用服务应用程序注册"。
13. 在左窗格中选择 " **API 权限**"。
14. 选择 "**向 \<租户\>授予管理员许可**"，其中 \<租户\> 是 Azure AD 租户的名称。 选择 **"是"** ，确认同意授予。

```powershell
    Create-AADIdentityApp.ps1
```

| 参数 | 必需还是可选 | 默认值 | Description |
| --- | --- | --- | --- |
| DirectoryTenantName | 需要 | Null | Azure AD 租户 ID。 提供 GUID 或字符串。 例如，myazureaaddirectory.onmicrosoft.com。 |
| AdminArmEndpoint | 需要 | Null | 管理员 Azure 资源管理器终结点。 例如，adminmanagement. test-azurestack。 |
| TenantARMEndpoint | 需要 | Null | 租户 Azure 资源管理器终结点。 例如 "test-azurestack"。 |
| AzureStackAdminCredential | 需要 | Null | Azure AD 服务管理员凭据。 |
| CertificateFilePath | 需要 | Null | 之前生成的标识应用程序证书文件的**完整路径**。 |
| CertificatePassword | 需要 | Null | 帮助保护证书私钥的密码。 |
| 环境 | 可选 | AzureCloud | 可用的目标 Azure Active Directory 图形服务所支持的云环境的名称。  允许的值： "AzureCloud"、"AzureChinaCloud"、"AzureUSGovernment"、"AzureGermanCloud"。|

## <a name="create-an-active-directory-federation-services-app"></a>创建 Active Directory 联合身份验证服务应用

对于 AD FS 保护 Azure Stack 中心环境，必须配置 AD FS 服务主体以支持以下操作：

- 辅助角色层上的虚拟机规模集集成。
- Azure Functions 门户和高级开发人员工具的 SSO。

管理员必须将 SSO 配置为：

- 为虚拟机规模集集成配置辅助角色层上的服务主体。
- 在应用服务中启用高级开发人员工具（Kudu）。
- 允许使用 Azure Functions 门户体验。

执行以下步骤:

1. 将 PowerShell 实例作为 Azurestack\azurestackadmin 身份打开
2. 在[必备步骤](azure-stack-app-service-before-you-get-started.md)中，请参阅下载和提取的脚本的位置。
3. [为 Azure Stack 集线器安装 PowerShell](azure-stack-powershell-install.md)。
4. 运行**create-adfsidentityapp.ps1**脚本。
5. 在 "**凭据**" 窗口中，输入 AD FS 的云管理员帐户和密码。 选择“确定”。
6. 提供[前面创建的证书](azure-stack-app-service-before-you-get-started.md)的证书文件路径和证书密码。 默认情况下，为此步骤创建的证书为**appservice. test-azurestack**。

```powershell
    Create-ADFSIdentityApp.ps1
```

| 参数 | 必需还是可选 | 默认值 | Description |
| --- | --- | --- | --- |
| AdminArmEndpoint | 需要 | Null | 管理员 Azure 资源管理器终结点。 例如，adminmanagement. test-azurestack。 |
| PrivilegedEndpoint | 需要 | Null | 特权终结点。 例如 AzS-ERCS01。 |
| CloudAdminCredential | 需要 | Null | Azure Stack 中心云管理员的域帐户凭据。 例如 Azurestack\CloudAdmin。 |
| CertificateFilePath | 需要 | Null | 标识应用程序的证书 PFX 文件的**完整路径**。 |
| CertificatePassword | 需要 | Null | 帮助保护证书私钥的密码。 |

## <a name="next-steps"></a>后续步骤

[安装应用服务资源提供程序](azure-stack-app-service-deploy.md)
