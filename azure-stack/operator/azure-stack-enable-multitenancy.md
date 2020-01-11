---
title: 在 Azure Stack Hub 中配置多租户 |Microsoft Docs
description: 了解如何在 Azure Stack 中心内启用和禁用多个 Azure Active Directory 租户。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: bryanr
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 09265b0b84a18d6df5f03127489a4d38f46a216e
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882447"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>在 Azure Stack Hub 中配置多租户

可以将 Azure Stack 中心配置为支持多个 Azure Active Directory （Azure AD）租户中的用户，使他们能够使用 Azure Stack 中心内的服务。 例如，请考虑以下情况：

- 你是安装 Azure Stack 集线器的 contoso.onmicrosoft.com 的服务管理员。
- Mary 是来宾用户所在的 fabrikam.onmicrosoft.com 的目录管理员。
- Mary 的公司从公司接收 IaaS 和 PaaS 服务，并需要允许来宾目录（fabrikam.onmicrosoft.com）的用户登录并使用 contoso.onmicrosoft.com 中的 Azure Stack 集线器资源。

本指南提供了在这种情况下，在 Azure Stack Hub 中配置多租户所需的步骤。 在这种情况下，你和 Mary 必须完成一些步骤，使来自 Fabrikam 的用户能够登录并使用 Contoso 中 Azure Stack 中心部署提供的服务。

## <a name="enable-multi-tenancy"></a>启用多租户

在 Azure Stack Hub 中配置多租户之前，需要考虑以下几个先决条件：
  
 - 你和 Mary 必须协调每个 Azure Stack 目录中安装的管理步骤（Contoso）和来宾目录（Fabrikam）。
 - 请确保已为 Azure Stack 集线器[安装](azure-stack-powershell-install.md)并[配置](azure-stack-powershell-configure-admin.md)了 PowerShell。
 - [下载 Azure Stack 中心工具](azure-stack-powershell-download.md)，并导入 "连接" 和 "标识" 模块：

    ```powershell
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-hub-directory"></a>配置 Azure Stack 中心目录

在本部分中，会将 Azure Stack 集线器配置为允许来自 Fabrikam Azure AD directory 租户的登录。

通过将 Azure 资源管理器配置为接受来自来宾目录租户的用户和服务主体，使来宾目录租户（Fabrikam）加入到 Azure Stack 中心。

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

Azure Stack 中心操作员启用了要与 Azure Stack 中心一起使用的 Fabrikam 目录后，Mary 必须向 Fabrikam 的目录租户注册 Azure Stack Hub。

#### <a name="registering-azure-stack-hub-with-the-guest-directory"></a>向来宾目录注册 Azure Stack 集线器

Mary （Fabrikam 的目录管理员）在 guest directory fabrikam.onmicrosoft.com 中运行以下命令：

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
> 如果 Azure Stack 中心管理员将来安装新服务或更新，则可能需要再次运行此脚本。
>
> 请随时再次运行此脚本，检查目录中 Azure Stack 中心应用的状态。
>
> 如果已注意到在托管磁盘（1808更新中引入）中创建 Vm 的问题，则添加了一个新的**磁盘资源提供程序**，要求再次运行此脚本。

### <a name="direct-users-to-sign-in"></a>指示用户登录

现在，你和 Mary 已经完成了加入 Mary 目录的步骤，Mary 可以指示 Fabrikam 用户登录。 Fabrikam 用户（具有 fabrikam.onmicrosoft.com 后缀的用户）通过访问 https\://portal.local.azurestack.external. 登录

Mary 将引导 Fabrikam 目录（Fabrikam 目录中不含后缀为 fabrikam.onmicrosoft.com 的用户）中的所有[外部主体](/azure/role-based-access-control/rbac-and-directory-admin-roles)使用 https\://portal.local.azurestack.external/fabrikam.onmicrosoft.com. 登录。 如果他们不使用此 URL，则会将其发送到其默认目录（Fabrikam），并收到一条错误消息，指出管理员尚未同意。

## <a name="disable-multi-tenancy"></a>禁用多租户

如果不再需要 Azure Stack 中心中的多个租户，可以按顺序执行以下步骤来禁用多租户：

1. 作为来宾目录（此方案中 Mary）的管理员，请运行*AzsWithMyDirectoryTenant*。 Cmdlet 从新目录中卸载所有 Azure Stack 中心应用。

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

2. 作为 Azure Stack 中心的服务管理员（在此方案中为），请运行*AzSGuestDirectoryTenant*。

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
    > 必须按顺序执行 "禁用多租户" 步骤。 如果步骤 #2 首先完成，步骤 #1 会失败。

## <a name="next-steps"></a>后续步骤

- [管理委派的提供商](azure-stack-delegated-provider.md)
- [Azure Stack 集线器关键概念](azure-stack-overview.md)
- [作为云解决方案提供商管理 Azure Stack 集线器的使用情况和计费](azure-stack-add-manage-billing-as-a-csp.md)
- [将使用情况和计费的租户添加到 Azure Stack 中心](azure-stack-csp-howto-register-tenants.md)
