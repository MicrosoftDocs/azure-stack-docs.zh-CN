---
title: 在 Azure Stack Hub 中配置多租户
description: 了解如何在 Azure Stack 中心为来宾 Azure Active Directory 租户配置多租户。
author: BryanLa
ms.topic: how-to
ms.date: 01/26/2021
ms.author: bryanla
ms.reviewer: bryanr
ms.lastreviewed: 01/26/2021
ms.openlocfilehash: 3de6c5db42285f90e1d4ce6c1ebf6736d7ce4863
ms.sourcegitcommit: d542b68b299b73e045f30916afb6018e365e9db6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975939"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>在 Azure Stack Hub 中配置多租户

你可以配置 Azure Stack 集线器以支持来自驻留在其他 Azure Active Directory 中的用户的登录 (Azure AD) 目录，从而允许他们使用 Azure Stack 中心的服务。 这些目录与 Azure Stack 中心之间存在 "来宾" 关系，因此，被视为来宾 Azure AD 租户。 例如，考虑以下方案：

- 你是 contoso.onmicrosoft.com 的服务管理员，home Azure AD 租户向你的 Azure Stack 中心提供标识和访问管理服务。
- Mary 是 fabrikam.onmicrosoft.com 的目录管理员，来宾用户所在的来宾 Azure AD 租户。
- Mary 的 company (Fabrikam) 使用公司的 IaaS 和 PaaS 服务。 Fabrikam 希望允许来自来宾目录 (fabrikam.onmicrosoft.com) 的用户登录并使用 contoso.onmicrosoft.com 保护的 Azure Stack 中心资源。

本指南提供在此方案的上下文中为来宾目录租户启用或禁用多 Azure Stack 租户所需的步骤。 你和 Mary 通过注册/注销来宾目录租户来完成此过程，这将通过 Fabrikam 用户启用/禁用 Azure Stack 中心登录和服务使用。 

如果你是云解决方案提供商 (CSP)，则可以通过其他方式[配置和管理多租户 Azure Stack Hub](azure-stack-add-manage-billing-as-a-csp.md)。 

## <a name="prerequisites"></a>先决条件

在注册或取消注册来宾目录之前，你和 Mary 必须为各自的 Azure AD 租户完成管理步骤： Azure Stack 中心主目录 (Contoso) ，来宾目录 (Fabrikam) ：

 - 为 Azure Stack 集线器[安装](powershell-install-az-module.md)和[配置](azure-stack-powershell-configure-admin.md)PowerShell。
 - [下载 Azure Stack Hub 工具](azure-stack-powershell-download.md)，并导入“连接和标识”模块：

    ```powershell
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

## <a name="register-a-guest-directory"></a>注册来宾目录

若要为多租户注册来宾目录，需要配置 "home Azure Stack 中心目录" 和 "来宾" 目录。

#### <a name="configure-azure-stack-hub-directory"></a>配置 Azure Stack Hub 目录

作为 contoso.onmicrosoft.com 的服务管理员，必须首先将 Fabrikam 的来宾目录租户登记到 Azure Stack 中心。 以下脚本会将 Azure 资源管理器配置为接受 fabrikam.onmicrosoft.com 租户中的用户和服务主体登录：

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as adminmanagement.<region>.<FQDN>.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack Hub directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest directory tenant. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group.
$location = "local"

# Subscription Name
$SubscriptionName = "Default Provider Subscription"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName `
 -SubscriptionName $SubscriptionName
```

#### <a name="configure-guest-directory"></a>配置来宾目录

接下来，Azure Stack Fabrikam) 的 Mary (directory 管理员必须通过运行以下脚本向 fabrikam.onmicrosoft.com guest 目录注册 Hub：

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as management.<region>.<FQDN>.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest directory tenant.
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose
```

> [!IMPORTANT]
> 如果你的 Azure Stack Hub 管理员将来安装新服务或更新，则你可能需要再次运行此脚本。
>
> 随时可以再次运行此脚本来检查目录中的 Azure Stack Hub 应用的状态。
>
> 如果已注意到在托管磁盘中创建 VM 时存在的问题（在 1808 更新中引入），则已添加新的 **磁盘资源提供程序**，从而需要再次运行此脚本。

### <a name="direct-users-to-sign-in"></a>指导用户登录

最后，Mary 可以 @fabrikam.onmicrosoft.com 通过访问 [Azure Stack 集线器用户门户](../user/azure-stack-use-portal.md)，将具有帐户的 Fabrikam 用户定向到登录帐户。 对于多节点系统，用户门户 URL 的格式为 `https://management.<region>.<FQDN>` 。 对于 ASDK 部署，URL 为 `https://portal.local.azurestack.external` 。

Mary 还必须直接在 Fabrikam 目录中 (用户的任何外部主体，而无需使用 fabrikam.onmicrosoft.com) 的后缀，才能使用登录 `https://<user-portal-url>/fabrikam.onmicrosoft.com` 。 如果未 `/fabrikam.onmicrosoft.com` 在 URL 中指定目录租户，则会将其发送到默认目录，并收到一条错误消息，指出管理员尚未同意。

## <a name="unregister-a-guest-directory"></a>取消注册来宾目录

如果你不再希望登录从来宾目录租户 Azure Stack 中心服务，则可以取消注册该目录。 同样，将需要配置 home Azure Stack 中心目录和来宾目录：

1. 在此方案中，作为来宾目录 (Mary 的管理员) ，请运行 `Unregister-AzsWithMyDirectoryTenant` 。 该 cmdlet 从新目录中卸载所有 Azure Stack Hub 应用。

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as management.<region>.<FQDN>.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest directory tenant.
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. 作为 Azure Stack 中心的服务管理员 (在此方案中) ，请运行 `Unregister-AzSGuestDirectoryTenant` 以下 cmdlet：

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as adminmanagement.<region>.<FQDN>.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack Hub directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest directory tenant. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant resource was created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > 禁用多租户步骤必须按顺序执行。 如果首先完成步骤 1，则步骤 2 将失败。

## <a name="retrieve-azure-stack-hub-identity-health-report"></a>检索 Azure Stack Hub 标识运行状况报告 

替换 `<region>`、`<domain>` 和 `<homeDirectoryTenant>` 占位符，然后以 Azure Stack Hub 管理员的身份执行以下 cmdlet。

```powershell

$AdminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
$DirectoryName = "<homeDirectoryTenant>.onmicrosoft.com"
$healthReport = Get-AzsHealthReport -AdminResourceManagerEndpoint $AdminResourceManagerEndpoint -DirectoryTenantName $DirectoryName
Write-Host "Healthy directories: "
$healthReport.directoryTenants | Where status -EQ 'Healthy' | Select -Property tenantName,tenantId,status | ft


Write-Host "Unhealthy directories: "
$healthReport.directoryTenants | Where status -NE 'Healthy' | Select -Property tenantName,tenantId,status | ft
```

## <a name="update-azure-ad-tenant-permissions"></a>更新 Azure AD 租户权限

此操作将在 Azure Stack Hub 中清除警报，指出目录需要更新。 从 Azurestack-tools-master/identity 文件夹运行以下命令：

```powershell
Import-Module ..\Identity\AzureStack.Identity.psm1

$adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"

# This is the primary tenant Azure Stack Hub is registered to:
$homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com"

Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
   -DirectoryTenantName $homeDirectoryTenantName -Verbose
```

该脚本会提示你提供 Azure AD 租户的管理凭据，并且需要几分钟才能运行。 运行 cmdlet 后，应该会清除警报。

## <a name="next-steps"></a>后续步骤

- [管理委派提供程序](azure-stack-delegated-provider.md)
- [Azure Stack Hub 的重要概念](azure-stack-overview.md)
- [管理充当云解决方案提供商的 Azure Stack Hub 的用量和计费](azure-stack-add-manage-billing-as-a-csp.md)
- [将租户添加到 Azure Stack Hub 以获取用量和计费信息](azure-stack-csp-howto-register-tenants.md)
