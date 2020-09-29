---
title: 在 Azure Stack Hub 中配置多租户
description: 了解如何在 Azure Stack Hub 中启用和禁用多个 Azure Active Directory 租户。
author: BryanLa
ms.topic: how-to
ms.date: 06/18/2020
ms.author: bryanla
ms.reviewer: bryanr
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: e99c1cc09f3dc6b0a04ff22f5b5dc96004ba305e
ms.sourcegitcommit: d73637146daaba0ef0ab1729683bb52c65466099
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2020
ms.locfileid: "88857493"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>在 Azure Stack Hub 中配置多租户

可以配置 Azure Stack Hub，以支持多个 Azure Active Directory (Azure AD) 租户中的用户，允许他们使用 Azure Stack Hub 中的服务。 例如，考虑以下方案：

- 你是安装 Azure Stack 集线器的 contoso.onmicrosoft.com 的服务管理员。
- Mary 是来宾用户所在的 fabrikam.onmicrosoft.com 的目录管理员。
- Mary 的公司从公司接收 IaaS 和 PaaS 服务，并需要允许来自来宾目录 (fabrikam.onmicrosoft.com) 的用户登录并使用 contoso.onmicrosoft.com 中的 Azure Stack 集线器资源。

本指南提供了此方案上下文中所需的步骤，用于在 Azure Stack Hub 中配置多租户。 在此方案中，你和 Mary 必须完成相关步骤以使 Fabrikam 中的用户能够登录并使用 Contoso 中部署的 Azure Stack Hub 提供的服务。

如果你是云解决方案提供商 (CSP) ，你可以通过其他方式 [配置和管理多租户 Azure Stack 集线器](azure-stack-add-manage-billing-as-a-csp.md)。 

## <a name="enable-multi-tenancy"></a>启用多租户

在 Azure Stack Hub 中配置多租户之前，需要考虑几个先决条件：
  
 - 你和 Mary 必须在安装 Azure Stack Hub 的目录 (Contoso) 和来宾目录 (Fabrikam) 之间协调管理步骤。
 - 确保已[安装](azure-stack-powershell-install.md)并[配置](azure-stack-powershell-configure-admin.md)适用于 Azure Stack Hub 的 PowerShell。
 - [下载 Azure Stack 中心工具](azure-stack-powershell-download.md)，并导入标识模块：

    ```powershell
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-hub-directory"></a>配置 Azure Stack Hub 目录

在本部分，你将配置 Azure Stack Hub 以允许从 Fabrikam Azure AD 目录租户登录。

通过将 Azure 资源管理器配置为接受来自来宾目录租户的用户和服务主体，将来宾目录租户 (Fabrikam) 加入到 Azure Stack Hub。

Contoso.onmicrosoft.com 的服务管理员运行以下命令：

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack Hub directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
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

### <a name="configure-guest-directory"></a>配置来宾目录

在 Azure Stack Hub 操作员使得 Fabrikam 目录能够与 Azure Stack Hub 一起使用后，Mary 必须向 Fabrikam 的目录租户注册 Azure Stack Hub。

#### <a name="register-azure-stack-hub-with-the-guest-directory"></a>将 Azure Stack Hub 注册到来宾目录

张薇 (directory 管理员) 在来宾目录 fabrikam.onmicrosoft.com 中运行以下命令：

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory.
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
> 如果已注意到在托管磁盘中创建 VM 时存在的问题（在 1808 更新中引入），则已添加新的**磁盘资源提供程序**，从而需要再次运行此脚本。

### <a name="direct-users-to-sign-in"></a>指导用户登录

现在，你和 Mary 已完成到加入 Mary 目录的步骤，Mary 可以指导 Fabrikam 用户登录。 Fabrikam 用户 (具有 fabrikam.onmicrosoft.com 后缀的用户) 通过访问 https//portal.local.azurestack.external. 登录 \:

Mary 会将 Fabrikam 目录中的所有 [外部主体](/azure/role-based-access-control/rbac-and-directory-admin-roles) 定向 (fabrikam 目录中没有 fabrikam.onmicrosoft.com 的后缀的用户) 使用 https \: //portal.local.azurestack.external/fabrikam.onmicrosoft.com. 登录 如果他们不使用此 URL，则会将其发送到其默认目录 (Fabrikam) 并收到一条错误消息，指出管理员尚未同意。

## <a name="disable-multi-tenancy"></a>禁用多租户

如果 Azure Stack Hub 中不再需要有多个租户，可以按顺序执行以下步骤来禁用多租户：

1. 以来宾目录的管理员身份（在此场景中为 Mary）运行 *Unregister-AzsWithMyDirectoryTenant*。 该 cmdlet 从新目录中卸载所有 Azure Stack Hub 应用。

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory.
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. 以 Azure Stack Hub 的服务管理员身份（在此场景中是你）运行 Unregister-AzSGuestDirectoryTenant。

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack Hub directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
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

### <a name="update-azure-ad-tenant-permissions"></a>更新 Azure AD 租户权限

此操作会清除 Azure Stack Hub 中的警报，表明目录需要更新。 从 **test-azurestack/identity** 文件夹中运行以下命令：

```powershell
Import-Module ..\Identity\AzureStack.Identity.psm1

$adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"

# This is the primary tenant Azure Stack is registered to:
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
