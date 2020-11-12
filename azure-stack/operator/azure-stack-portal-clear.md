---
title: 从 Azure Stack Hub 中按需清除门户用户数据数据
description: 了解如何按 Azure Stack Hub 用户的请求清除门户用户数据。
author: sethmanheim
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: sethm
ms.reviewer: troettinger
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 3821eced3f37902b20f13ee4d918b2ff2ea8aca9
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545085"
---
# <a name="clear-portal-user-data-from-azure-stack-hub"></a>从 Azure Stack Hub 中清除门户用户数据

Azure Stack Hub 操作员可以根据 Azure Stack Hub 用户的请求，按需清除门户用户数据。 Azure Stack Hub 用户可以通过固定磁贴和更改仪表板布局来自定义门户。 用户还可以根据个人喜好更改主题和调整默认语言。

门户用户数据包括 Azure Stack Hub 用户门户中的收藏夹和最近访问的资源。 本文介绍如何清除门户用户数据。

只有在删除用户订阅之后，才能删除门户用户设置。

> [!NOTE]
> 在遵循本文中的指导之后，某些用户数据可能仍会出现在事件日志的 system 节中。 在日志自动滚动更新之前，这些数据可能会保留数天。

## <a name="requirements"></a>要求

- [安装适用于 Azure Stack Hub 的 PowerShell](powershell-install-az-module.md)。
- 从 GitHub [下载最新的 Azure Stack Hub 工具](azure-stack-powershell-download.md)。
- 用户帐户必须仍存在于目录中。
- 用于访问管理员资源管理器终结点的 Azure Stack Hub 管理员凭据。

> [!NOTE]
> 如果你尝试从来宾目录（多租户）中删除受邀用户的门户用户信息，必须在该目录中拥有读取权限。 有关详细信息，请参阅[本文稍后的 CSP 方案](#clear-portal-user-data-in-guest-directory)。

## <a name="clear-portal-user-data-using-a-user-principal-name"></a>使用用户主体名称清除门户用户数据

此方案假设默认提供程序订阅和用户属于同一目录，或者你对用户所在的目录拥有读取访问权限。

在继续之前，请务必从 GitHub [下载最新版本的 Azure Stack Hub 工具](azure-stack-powershell-download.md)。

对于此过程，请使用能够与 Azure Stack Hub 的管理员资源管理器终结点通信的计算机。

1. 打开提升了权限的 Windows PowerShell 会话 (以管理员身份运行) ，中转到 Test-azurestack- **az** 目录中的根文件夹，然后导入所需的 PowerShell 模块：

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. 运行以下命令。 请务必将占位符替换为与环境匹配的值：

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.

   $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack Hub directory tenant ID.
   $azureStackDirectoryTenantId = "f5025bf2-547f-4b49-9693-6420c1d5e4ca"

   ## Replace the following value with the user directory tenant ID.
   $userDirectoryTenantId = " 7ddf3648-9671-47fd-b63d-eecd82ed040e"

   ## Replace the following value with name of the user principal whose portal user data is to be cleared.
   $userPrincipalName = "myaccount@contoso.onmicrosoft.com"

   Clear-AzsUserDataWithUserPrincipalName -AzsAdminArmEndpoint $adminARMEndpoint `
    -AzsAdminDirectoryTenantId $azureStackDirectoryTenantId `
    -UserPrincipalName $userPrincipalName `
    -DirectoryTenantId $userDirectoryTenantId
   ```

   > [!NOTE]
   > `azureStackDirectoryTenantId` 是可选项。 如果未指定此值，脚本将在 Azure Stack Hub 中已注册的所有租户目录中搜索用户主体名称，然后清除所有匹配用户的门户数据。

## <a name="clear-portal-user-data-in-guest-directory"></a>在来宾目录中清除门户用户数据

在此方案中，Azure Stack Hub 操作员对用户所在的来宾目录没有访问权限。 如果你是云解决方案提供商 (CSP)，这是一种常见方案。

要使 Azure Stack Hub 操作员能够删除门户用户数据，至少需要获取用户对象 ID。

用户必须查询该对象 ID，并将其提供给 Azure Stack Hub 操作员。 操作员对用户所在的目录没有访问权限。

### <a name="user-retrieves-the-user-object-id"></a>用户检索用户对象 ID

1. 打开提升了权限的 Windows PowerShell 会话 (以管理员身份运行) ，中转到 Test-azurestack- **az** 目录中的根文件夹，然后导入必要的 PowerShell 模块。

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. 运行以下命令。 请务必将占位符替换为与环境匹配的值。

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $userARMEndpoint = "https://management.local.azurestack.external"

   ## Replace the following value with the directory tenant ID, which contains the user account.
   $userDirectoryTenantId = "3160cbf5-c227-49dd-8654-86e924c0b72f"

   ## Replace the following value with the name of the user principal whose portal user data is to be cleared.
   $userPrincipleName = "myaccount@contoso.onmicrosoft.com"

   Get-UserObjectId -DirectoryTenantId $userDirectoryTenantId `
    -AzsArmEndpoint $userARMEndpoint `
    -UserPricinpalName $userPrincipleName
   ```

   > [!NOTE]
   > 用户必须将用户对象 ID（上一脚本的输出）提供给 Azure Stack Hub 操作员。

## <a name="azure-stack-hub-operator-removes-the-portal-user-data"></a>Azure Stack Hub 操作员删除门户用户数据

以 Azure Stack Hub 操作员的身份收到用户对象 ID 后，运行以下命令删除门户用户数据：

1. 打开提升了权限的 Windows PowerShell 会话 (以管理员身份运行) ，中转到 Test-azurestack- **az** 目录中的根文件夹，然后导入必要的 PowerShell 模块。

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. 运行以下命令（请务必根据环境调整参数）：

   ```powershell
   ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
   $AzsAdminARMEndpoint = "https://adminmanagement.local.azurestack.external"

   ## Replace the following value with the Azure Stack Hub directory tenant ID.
   $AzsAdminDirectoryTenantId = "f5025bf2-547f-4b49-9693-6420c1d5e4ca"

   ## Replace the following value with the directory tenant ID of the user to clear.
   $DirectoryTenantId = "3160cbf5-c227-49dd-8654-86e924c0b72f"

   ## Replace the following value with the name of the user principal whose portal user data is to be cleared.
   $userObjectID = "s-1-*******"
   Clear-AzsUserDataWithUserObject -AzsAdminArmEndpoint $AzsAdminARMEndpoint `
    -AzsAdminDirectoryTenantId $AzsAdminDirectoryTenantId `
    -DirectoryTenantID $DirectoryTenantId `
    -UserObjectID $userObjectID `
   ```

## <a name="next-steps"></a>后续步骤

- [将 Azure Stack Hub 注册到 Azure](azure-stack-registration.md)，并使用提供给用户的项填充 [Azure Stack Hub 市场](azure-stack-marketplace.md)。
