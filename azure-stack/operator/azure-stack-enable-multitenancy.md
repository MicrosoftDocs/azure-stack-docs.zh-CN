---
title: Azure Stack 中的多租户
description: 了解如何在 Azure Stack 中支持多个 Azure Active Directory 目录
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
ms.date: 03/04/2019
ms.author: patricka
ms.reviewer: bryanr
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: 0932c1eb5f064d40841d9b056d869927e1144527
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691650"
---
# <a name="multi-tenancy-in-azure-stack"></a>Azure Stack 中的多租户

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以配置 Azure Stack，以支持多个 Azure Active Directory (Azure AD) 租户中的用户使用 Azure Stack 中的服务。 例如，考虑以下方案：

- 你是 contoso.onmicrosoft.com，服务管理员 Azure Stack 的安装位置。
- Mary 是 fabrikam.onmicrosoft.com，目录管理员来宾用户的位置。
- Mary 的公司收到你的公司，IaaS 和 PaaS 服务，并需要允许来宾目录 (fabrikam.onmicrosoft.com) 中的用户登录并在 contoso.onmicrosoft.com 中使用 Azure Stack 资源。

本指南提供了此方案上下文中所需的步骤，用于在 Azure Stack 中配置多租户。 在此方案中，你和 Mary 必须完成相关步骤以使 Fabrikam 中的用户能够登录并使用 Contoso 中部署的 Azure Stack 提供的服务。  

## <a name="enable-multi-tenancy"></a>启用多租户

在 Azure Stack 中配置多租户之前，需要考虑几个先决条件：
  
 - 你和 Mary 必须在安装 Azure Stack 的目录 (Contoso) 和来宾目录 (Fabrikam) 之间协调管理步骤。  
 - 确保已[安装](azure-stack-powershell-install.md)并[配置](azure-stack-powershell-configure-admin.md)适用于 Azure Stack 的 PowerShell。
 - [下载 Azure Stack Tools](azure-stack-powershell-download.md)，并导入“连接和标识”模块：

    ```powershell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-directory"></a>配置 Azure Stack 目录

在本部分中，将配置 Azure Stack 以允许从 Fabrikam Azure AD 目录租户登录。

通过将 Azure 资源管理器配置为接受来自来宾目录租户的用户和服务主体，将来宾目录租户 (Fabrikam) 加入到 Azure Stack。

服务管理员的 contoso.onmicrosoft.com 运行以下命令。

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName
```

### <a name="configure-guest-directory"></a>配置来宾目录

在 Azure Stack 管理员 / 操作员使得 Fabrikam 目录能够与 Azure Stack 一起使用后，Mary 必须向 Fabrikam 的目录租户注册 Azure Stack。

#### <a name="registering-azure-stack-with-the-guest-directory"></a>将 Azure Stack 注册到来宾目录

Mary Fabrikam 目录管理员在来宾目录 fabrikam.onmicrosoft.com 中运行以下命令。

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
```

> [!IMPORTANT]
> 如果你的 Azure Stack 管理员将来安装新服务或更新，则你可能需要再次运行此脚本。
>
> 随时可以再次运行此脚本来检查目录中的 Azure Stack 应用程序的状态。
>
> 如果已注意到在托管磁盘中创建 VM 时存在的问题（在 1808 更新中引入），则已添加新的**磁盘资源提供程序**，从而需要再次运行此脚本。

### <a name="direct-users-to-sign-in"></a>指导用户登录

现在，你和 Mary 已完成到加入 Mary 目录的步骤，Mary 可以指导 Fabrikam 用户登录。  Fabrikam 用户 （即，具有 fabrikam.onmicrosoft.com 后缀的用户） 登录，请访问 https://portal.local.azurestack.external。  

Mary 将指导任何[外部主体](/azure/role-based-access-control/rbac-and-directory-admin-roles)Fabrikam 目录 （即，如果没有 fabrikam.onmicrosoft.com 后缀，Fabrikam 目录中的用户） 中使用登录 https://portal.local.azurestack.external/fabrikam.onmicrosoft.com。  如果他们未使用此 URL，则将被发送到其默认目录 (Fabrikam)，并收到一个错误，指出其管理员未许可。

## <a name="disable-multi-tenancy"></a>禁用多租户

如果 Azure Stack 中不再需要有多个租户，可以按顺序执行以下步骤来禁用多租户：

1. 以来宾目录的管理员身份（在此场景中为 Mary）运行 *Unregister-AzsWithMyDirectoryTenant*。 该 cmdlet 从新目录中卸载所有 Azure Stack 应用程序。

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. 以 Azure Stack 的服务管理员身份（在此场景中是你）运行 *Unregister-AzSGuestDirectoryTenant*。 

    ``` PowerShell  
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
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

## <a name="next-steps"></a>后续步骤

- [管理委派提供程序](azure-stack-delegated-provider.md)
- [Azure Stack 关键概念](azure-stack-overview.md)
- [作为云服务提供商管理 Azure Stack 的使用情况和计费](azure-stack-add-manage-billing-as-a-csp.md)
- [将用于统计使用情况和计费的租户添加到 Azure Stack](azure-stack-csp-howto-register-tenants.md)
