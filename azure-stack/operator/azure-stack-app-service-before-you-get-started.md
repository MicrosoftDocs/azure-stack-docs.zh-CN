---
title: 在 Azure Stack 上部署应用服务之前 | Microsoft Docs
description: 在 Azure Stack 上部署应用服务之前需要完成的步骤
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
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: f4b26701af32026ac2c83bf675fa29e3b6254cb2
ms.sourcegitcommit: 701685f0b59e5a3d1a8d39fe477b8df701a51cd2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70159557"
---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>在 Azure Stack 上开始使用应用服务之前

适用于：Azure Stack 集成系统和 Azure Stack 开发工具包

在 Azure Stack 上部署 Azure 应用服务之前，必须完成本文中的先决条件步骤。

> [!IMPORTANT]
> 请将 1904 更新应用于 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包 (ASDK)，然后部署 Azure 应用服务 1.6。

## <a name="download-the-installer-and-helper-scripts"></a>下载安装程序与帮助器脚本

1. 下载 [Azure Stack 上的应用服务部署帮助器脚本](https://aka.ms/appsvconmashelpers)。
2. 下载 [Azure Stack 上的应用服务安装程序](https://aka.ms/appsvconmasinstaller)。
3. 提取帮助器脚本 .zip 文件中的文件。 会提取以下文件和文件夹：

   - Common.ps1
   - Create-AADIdentityApp.ps1
   - Create-ADFSIdentityApp.ps1
   - Create-AppServiceCerts.ps1
   - Get-AzureStackRootCert.ps1
   - Remove-AppService.ps1
   - 模块文件夹
     - GraphAPI.psm1

## <a name="syndicate-the-custom-script-extension-from-the-marketplace"></a>从市场合成自定义脚本扩展版本

Azure Stack 上的 Azure 应用服务需要自定义脚本扩展 v1.9.1。  开始部署或升级 Azure Stack 上的 Azure 应用服务之前，必须先[从市场合成](azure-stack-download-azure-marketplace-item.md)该扩展

## <a name="get-certificates"></a>获取证书

### <a name="azure-resource-manager-root-certificate-for-azure-stack"></a>Azure Stack 的 Azure 资源管理器根证书

在计算机上打开一个提升的 PowerShell 会话，该计算机可以访问 Azure Stack 集成系统或 Azure Stack 开发工具包主机上的特权终结点。

从帮助器脚本提取到的文件夹运行 *Get-AzureStackRootCert.ps1* 脚本。 此脚本在应用服务所需的、用于创建证书的脚本所在的同一文件夹中创建一个根证书。

运行以下 PowerShell 命令时，必须为 AzureStack\CloudAdmin 提供特权终结点和凭据。

```powershell
    Get-AzureStackRootCert.ps1
```

#### <a name="get-azurestackrootcertps1-script-parameters"></a>Get-AzureStackRootCert.ps1 脚本参数

| 参数 | 必需还是可选 | 默认值 | 描述 |
| --- | --- | --- | --- |
| PrivilegedEndpoint | 必填 | AzS-ERCS01 | 特权终结点 |
| CloudAdminCredential | 必填 | AzureStack\CloudAdmin | Azure Stack 云管理的域帐户凭据 |

### <a name="certificates-required-for-asdk-deployment-of-azure-app-service"></a>Azure 应用服务的 ASDK 部署所需的证书

*Create-AppServiceCerts.ps1* 脚本配合 Azure Stack 证书颁发机构运行，创建应用服务所需的四个证书。

| 文件名 | 使用 |
| --- | --- |
| _.appservice.local.azurestack.external.pfx | 应用服务默认 SSL 证书 |
| api.appservice.local.azurestack.external.pfx | 应用服务 API SSL 证书 |
| ftp.appservice.local.azurestack.external.pfx | 应用服务发布者 SSL 证书 |
| sso.appservice.local.azurestack.external.pfx | 应用服务标识应用程序证书 |

若要创建证书，请执行以下步骤：

1. 使用 AzureStack\AzureStackAdmin 帐户登录到 Azure Stack 开发工具包主机。
2. 打开提升的 PowerShell 会话。
3. 从帮助器脚本提取到的文件夹运行 *Create-AppServiceCerts.ps1* 脚本。 此脚本在应用服务所需的、用于创建证书的脚本所在的同一文件夹中创建四个证书。
4. 输入密码来保护 .pfx 文件，并记下该密码。 必须在 Azure Stack 上的应用服务安装程序中输入此密码。

#### <a name="create-appservicecertsps1-script-parameters"></a>Create-AppServiceCerts.ps1 脚本参数

| 参数 | 必需还是可选 | 默认值 | 描述 |
| --- | --- | --- | --- |
| pfxPassword | 必填 | Null | 帮助保护证书私钥的密码 |
| DomainName | 必填 | local.azurestack.external | Azure Stack 区域和域后缀 |

### <a name="certificates-required-for-azure-stack-production-deployment-of-azure-app-service"></a>Azure 应用服务的 Azure Stack 生产部署所需的证书

若要在生产环境中运行资源提供程序，必须提供以下证书：

- 默认域证书
- API 证书
- 发布证书
- 标识证书

#### <a name="default-domain-certificate"></a>默认域证书

默认域证书放在“前端”角色上。 对 Azure 应用服务发出通配符或默认域请求的用户应用程序使用此证书。 该证书还用于源代码管理操作 (Kudu)。

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

“发布者”角色的证书在应用程序所有者上传内容时保护其 FTPS 流量。 用于发布的证书必须包含匹配 FTPS DNS 条目的使用者。

| 格式 | 示例 |
| --- | --- |
| ftp.appservice.\<region\>.\<DomainName\>.\<extension\> | ftp.appservice.redmond.azurestack.external |

#### <a name="identity-certificate"></a>标识证书

标识应用程序的证书可以实现：

- Azure Active Directory (Azure AD) 或 Active Directory 联合身份身份验证服务 (AD FS) 目录、Azure Stack 与应用服务 之间的集成，以支持与计算资源提供程序的集成。
- Azure Stack 上的 Azure 应用服务中的高级开发人员工具的单一登录方案。

用于标识的证书必须包含匹配以下格式的使用者。

| 格式 | 示例 |
| --- | --- |
| sso.appservice.\<region\>.\<DomainName\>.\<extension\> | sso.appservice.redmond.azurestack.external |

### <a name="validate-certificates"></a>验证证书

在部署应用服务资源提供程序之前，应当使用 [PowerShell 库](https://aka.ms/AzsReadinessChecker)中提供的 Azure Stack 准备情况检查器工具来[验证要使用的证书](azure-stack-validate-pki-certs.md#perform-platform-as-a-service-certificate-validation)。 Azure Stack 准备情况检查器工具验证生成的 PKI 证书是否适用于应用服务部署。

作为最佳做法，当使用任何所需的 [Azure Stack PKI 证书](azure-stack-pki-certs.md)时，如果需要，应当计划留出足够的时间来测试和重新颁发证书。

## <a name="virtual-network"></a>虚拟网络

> [!NOTE]
> 预先创建自定义虚拟网络是可选操作，因为 Azure Stack 上的 Azure 应用服务可以创建所需的虚拟网络，但之后需要通过公共 IP 地址来与 SQL 和文件服务器通信。

Azure Stack 上的 Azure 应用服务允许将资源提供程序部署到现有的虚拟网络，或者允许在部署时创建虚拟网络。 使用现有虚拟网络可以通过内部 IP 连接到 Azure Stack 上的 Azure 应用服务所需的文件服务器和 SQL Server。 在 Azure Stack 上安装 Azure 应用服务之前，必须为虚拟网络配置以下地址范围和子网：

虚拟网络 - /16

子网

- ControllersSubnet /24
- ManagementServersSubnet /24
- FrontEndsSubnet /24
- PublishersSubnet /24
- WorkersSubnet /21

## <a name="licensing-concerns-for-required-file-server-and-sql-server"></a>所需文件服务器和 SQL Server 的许可问题

Azure Stack 上的 Azure 应用服务需要文件服务器和 SQL Server 才能运行。  可以使用 Azure Stack 部署外部预先存在的资源，也可以将资源部署在其 Azure Stack 默认提供程序订阅的内部。

如果选择将资源部署在 Azure Stack 默认提供程序订阅内部，则这些资源的许可证（Windows Server 许可证和 SQL Server 许可证）会包括在 Azure Stack 上的 Azure 应用服务的成本中，但会受到以下约束：

- 基础结构部署到“默认提供程序订阅”中；
- 基础结构由 Azure Stack 资源提供程序上的 Azure 应用服务独占使用。  任何其他工作负荷、管理程序（其他资源提供程序，例如 SQL-RP）或租户（例如需要数据库的租户应用程序）都无权使用此基础结构。

## <a name="prepare-the-file-server"></a>准备文件服务器

Azure 应用服务需要使用文件服务器。 在生产部署中，必须将文件服务器配置为高度可用，且能够应对故障。

### <a name="quickstart-template-for-file-server-for-deployments-of-azure-app-service-on-asdk"></a>用于部署 ASDK 上的 Azure应用服务的文件服务器快速入门模板。

如果只部署 Azure Stack 开发工具包，则可以使用[示例 Azure 资源管理器部署模板](https://aka.ms/appsvconmasdkfstemplate)来部署已配置的单节点文件服务器。 单节点文件服务器位于工作组中。

### <a name="quickstart-template-for-highly-available-file-server-and-sql-server"></a>高可用性文件服务器和 SQL Server 的快速入门模板

现已推出一个[参考体系结构快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/appservice-fileserver-sqlserver-ha)，用于部署文件服务器、SQL Server，并在配置为支持 Azure Stack 上的 Azure 应用服务高可用性部署的虚拟网络中支持 Active Directory 基础结构。

### <a name="steps-to-deploy-a-custom-file-server"></a>部署自定义文件服务器的步骤

>[!IMPORTANT]
> 如果选择在现有虚拟网络中部署应用服务，应将文件服务器部署到独立于应用服务的子网中。

>[!NOTE]
> 如果已选择使用上述任一快速入门模板部署文件服务器，则可以跳过此部分，因为在部署模板的过程中已配置文件服务器。

#### <a name="provision-groups-and-accounts-in-active-directory"></a>在 Active Directory 中预配组和帐户

1. 创建以下 Active Directory 全局安全组：

   - FileShareOwners
   - FileShareUsers

2. 创建以下 Active Directory 帐户作为服务帐户：

   - FileShareOwner
   - FileShareUser

   根据安全最佳做法，这些帐户（以及所有 Web 角色）的用户应该各不相同，并采用强用户名和密码。 根据以下条件设置密码：

   - 启用“密码永不过期”。
   - 启用“用户不能更改密码”。
   - 禁用“用户在下次登录时必须更改密码”。

3. 如下所述将帐户添加到组成员身份：

   - 将 **FileShareOwner** 添加到 **FileShareOwners** 组。
   - 将 **FileShareUser** 添加到 **FileShareUsers** 组。

#### <a name="provision-groups-and-accounts-in-a-workgroup"></a>在工作组中预配组和帐户

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

## <a name="prepare-the-sql-server-instance"></a>准备 SQL Server 实例

>[!NOTE]
> 如果已选择为高度可用的文件服务器和 SQL Server 部署快速入门模板，则可以跳过本部分，因为模板会在 HA 配置中部署和配置 SQL Server。

对于 Azure Stack 上的 Azure 应用服务托管和计量数据库，必须准备 SQL Server 实例，用于存放应用服务数据库。

对于 Azure Stack 开发工具包部署，可以使用 SQL Server Express 2014 SP2 或更高版本。  SQL Server 必须配置为支持**混合模式**身份验证, Azure Stack 上的应用服务不支持 Windows 身份验证。

对于生产和高可用性目的，应使用完整版本的 SQL Server 2014 SP2 或更高版本，启用混合模式身份验证，并在[高可用性配置](https://docs.microsoft.com/sql/sql-server/failover-clusters/high-availability-solutions-sql-server)中部署。

必须能够从所有“应用服务”角色访问 Azure Stack 上的 Azure 应用服务的 SQL Server 实例。 可以在 Azure Stack 中的默认提供程序订阅中部署 SQL Server。 或者，可以使用组织中现有的基础结构（前提是与 Azure Stack 建立了连接）。 如果使用 Azure 市场映像，请记得相应地配置防火墙。

> [!NOTE]
> 可通过市场管理功能获取许多 SQL IaaS 虚拟机映像。 在使用市场项部署 VM 之前，请确保下载最新版本的 SQL IaaS 扩展。 SQL 映像与 Azure 中提供的 SQL VM 相同。 对于从这些映像创建的 SQL VM，IaaS 扩展和相应的门户增强功能可提供自动修补和备份等功能。
>
> 对于任何 SQL Server 角色，可以使用默认实例或命名实例。 如果使用命名实例，请务必手动启动 SQL Server Browser 服务并打开端口 1434。

应用服务安装程序将检查以确保 SQL Server 已启用数据库包含。 若要在将托管应用服务数据库的 SQL Server 上启用数据库包含，请运行以下 SQL 命令：

```sql
sp_configure 'contained database authentication', 1;
GO
RECONFIGURE;
GO
```

>[!IMPORTANT]
> 如果选择在现有虚拟网络中部署应用服务，应将 SQL Server 部署到独立于应用服务和文件服务器的子网中。
>

## <a name="create-an-azure-active-directory-application"></a>创建 Azure Active Directory 应用程序

配置 Azure AD 服务主体以支持以下操作：

- 辅助角色层上的虚拟机规模集集成。
- Azure Functions 门户和高级开发人员工具的 SSO。

这些步骤只适用于 Azure AD 保护的 Azure Stack 环境。

管理员必须配置 SSO 才能执行以下操作：

- 在 应用服务 (Kudu) 中启用高级开发人员工具。
- 启用 Azure Functions 门户体验。

请执行以下步骤：

1. 以 azurestack\AzureStackAdmin 身份打开 PowerShell 实例。
2. 转到在[先决条件步骤](azure-stack-app-service-before-you-get-started.md)中下载并提取的脚本所在的位置。
3. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。
4. 运行 **Create-AADIdentityApp.ps1** 脚本。 根据提示输入部署 Azure Stack 时使用的 Azure AD 租户 ID。 例如, 输入**myazurestack.onmicrosoft.com**。
5. 在“凭据”窗口中，输入 Azure AD 服务管理帐户和密码。 选择“确定”。
6. 输入[前面创建的证书](azure-stack-app-service-before-you-get-started.md)的证书文件路径和证书密码。 默认情况下值，为此步骤创建的证书是 **sso.appservice.local.azurestack.external.pfx**。
7. 此脚本在租户 Azure AD 实例中创建新的应用程序。 请记下 PowerShell 输出中返回的应用程序 ID。 安装期间需要此信息。
8. 打开新的浏览器窗口，以 Azure Active Directory 服务管理员的身份登录到 [Azure 门户](https://portal.azure.com)。
9. 打开 Azure AD 资源提供程序。
10. 选择“应用注册”。
11. 搜索步骤 7 返回的应用程序 ID。 随即会列出应用服务应用程序。
12. 在列表中选择“应用程序”。
13. 选择“设置”。
14. 选择“所需的权限” > “授予权限” > “是”。

```powershell
    Create-AADIdentityApp.ps1
```

| 参数 | 必需还是可选 | 默认值 | 描述 |
| --- | --- | --- | --- |
| DirectoryTenantName | 必填 | Null | Azure AD 租户 ID。 提供 GUID 或字符串。 例如, myazureaaddirectory.onmicrosoft.com。 |
| AdminArmEndpoint | 必填 | Null | Azure 资源管理器管理终结点。 例如 adminmanagement.local.azurestack.external。 |
| TenantARMEndpoint | 必填 | Null | Azure 资源管理器租户终结点。 例如 management.local.azurestack.external。 |
| AzureStackAdminCredential | 必填 | Null | Azure AD 服务管理员凭据。 |
| CertificateFilePath | 必填 | Null | 前面生成的标识应用程序证书文件的**完整路径**。 |
| CertificatePassword | 必填 | Null | 帮助保护证书私钥的密码。 |
| 环境 | 可选 | AzureCloud | 其中目标 Azure Active Directory Graph 服务可用的受支持云环境的名称。  允许的值：' AzureCloud '、' AzureChinaCloud '、' AzureUSGovernment '、' AzureGermanCloud '。|

## <a name="create-an-active-directory-federation-services-application"></a>创建 Active Directory 联合身份验证服务应用程序

对于受 AD FS 保护的 Azure Stack 环境，必须配置 AD FS 服务主体以支持以下操作：

- 辅助角色层上的虚拟机规模集集成。
- Azure Functions 门户和高级开发人员工具的 SSO。

管理员必须配置 SSO 才能执行以下操作：

- 针对辅助角色层上的虚拟机规模集集成配置服务主体。
- 在 应用服务 (Kudu) 中启用高级开发人员工具。
- 启用 Azure Functions 门户体验。

请执行以下步骤：

1. 以 azurestack\AzureStackAdmin 身份打开 PowerShell 实例。
2. 转到在[先决条件步骤](azure-stack-app-service-before-you-get-started.md)中下载并提取的脚本所在的位置。
3. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。
4. 运行 **Create-ADFSIdentityApp.ps1** 脚本。
5. 在“凭据”窗口中，输入 AD FS 云管理帐户和密码。 选择“确定”。
6. 提供[前面创建的证书](azure-stack-app-service-before-you-get-started.md)的证书文件路径和证书密码。 默认情况下值，为此步骤创建的证书是 **sso.appservice.local.azurestack.external.pfx**。

```powershell
    Create-ADFSIdentityApp.ps1
```

| 参数 | 必需还是可选 | 默认值 | 描述 |
| --- | --- | --- | --- |
| AdminArmEndpoint | 必填 | Null | Azure 资源管理器管理终结点。 例如 adminmanagement.local.azurestack.external。 |
| PrivilegedEndpoint | 必填 | Null | 特权终结点。 例如 AzS-ERCS01。 |
| CloudAdminCredential | 必填 | Null | Azure Stack 云管理的域帐户凭据。 例如 Azurestack\CloudAdmin。 |
| CertificateFilePath | 必填 | Null | 标识应用程序的证书 PFX 文件的**完整路径**。 |
| CertificatePassword | 必填 | Null | 帮助保护证书私钥的密码。 |

## <a name="next-steps"></a>后续步骤

[安装应用服务资源提供程序](azure-stack-app-service-deploy.md)
