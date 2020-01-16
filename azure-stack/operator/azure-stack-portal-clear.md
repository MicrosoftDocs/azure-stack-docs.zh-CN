---
title: 从 Azure Stack 中心按需清除 "门户用户数据"。 | Microsoft Docs
description: 作为 Azure Stack 中心操作员，请了解当 Azure Stack 中心用户请求时如何清除门户用户数据。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.date: 09/10/2019
ms.author: sethm
ms.reviewer: troettinger
ms.lastreviewed: 09/10/2019
monikerRange: azs-1802
ms.openlocfilehash: ac28a67f7b1409ebc5a786a88e8b9702df94c2ff
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76022768"
---
# <a name="clear-portal-user-data-from-azure-stack-hub"></a>从 Azure Stack 中心清除门户用户数据

Azure Stack 中心操作员可以在 Azure Stack 集线器用户请求时按需清除门户用户数据。 作为 Azure Stack 中心用户，可以通过固定磁贴和更改仪表板布局来自定义门户。 用户还可以更改主题，并调整默认语言以匹配个人首选项。 

门户用户数据包括 Azure Stack 集线器用户门户中的收藏夹和最近访问的资源。 本文介绍如何清除门户用户数据。

只应在删除用户订阅后删除门户用户设置。

> [!NOTE]
> 遵循本文中的指南后，某些用户数据仍可存在于事件日志的系统部分中。 此数据可以保留几天，直到日志自动滚动更新。

## <a name="requirements"></a>要求

- [为 Azure Stack 集线器安装 PowerShell](azure-stack-powershell-install.md)。
- 从 GitHub[下载最新的 Azure Stack 中心工具](azure-stack-powershell-download.md)。
- 用户帐户必须仍然存在于目录中。
- Azure Stack 中心管理员凭据以访问管理员资源管理器终结点。

> [!NOTE]
> 如果尝试从已从来宾目录（多租户）邀请的用户中删除门户用户信息，则必须对该目录具有 "读取" 权限。 有关详细信息，请参阅[本文后面的 CSP 方案](#clear-portal-user-data-in-guest-directory)。

## <a name="clear-portal-user-data-using-a-user-principal-name"></a>使用用户主体名称清除门户用户数据

此方案假设默认提供商订阅和用户属于同一目录，或者你对该用户所在的目录具有读取访问权限。

在继续操作之前，请确保从 GitHub[下载最新版本的 Azure Stack 中心工具](azure-stack-powershell-download.md)。

对于此过程，请使用可以与 Azure Stack 中心的管理员资源管理器终结点进行通信的计算机。

1. 打开提升的 Windows PowerShell 会话（以管理员身份运行），导航到**test-azurestack**目录中的根文件夹，然后导入所需的 PowerShell 模块：

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. 运行以下命令。 请确保将占位符替换为与环境匹配的值。

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
   > `azureStackDirectoryTenantId` 是可选项。 如果未指定此值，则脚本将在 Azure Stack Hub 中注册的所有租户目录中搜索用户主体名称，然后清除所有匹配用户的门户数据。

## <a name="clear-portal-user-data-in-guest-directory"></a>清除来宾目录中的门户用户数据

在此方案中，Azure Stack 中心操作员无权访问用户所在的来宾目录。 这是云解决方案提供商（CSP）的常见方案。

要使 Azure Stack 中心操作员删除门户用户数据，至少需要用户对象 ID。

用户必须查询对象 ID 并将其提供给 Azure Stack 中心操作员。 操作员无权访问该用户所在的目录。

### <a name="user-retrieves-the-user-object-id"></a>用户检索用户对象 ID

1. 打开提升的 Windows PowerShell 会话（以管理员身份运行），导航到**test-azurestack**目录中的根文件夹，然后导入所需的 PowerShell 模块。

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. 运行以下命令。 请确保将占位符替换为与环境匹配的值。

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
   > 用户必须提供用户对象 ID，该 ID 是上一个脚本的输出到 Azure Stack 中心操作员。

## <a name="azure-stack-hub-operator-removes-the-portal-user-data"></a>Azure Stack 中心操作员删除门户用户数据

接收用户对象 ID 作为 Azure Stack 中心操作员后，运行以下命令以删除门户用户数据：

1. 打开提升的 Windows PowerShell 会话（以管理员身份运行），导航到**test-azurestack**目录中的根文件夹，然后导入所需的 PowerShell 模块。

   ```powershell
   Import-Module .\DatacenterIntegration\Portal\PortalUserDataUtilities.psm1
   ```

2. 运行以下命令，确保调整参数，使之与你的环境匹配：

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

- 向[Azure 注册 Azure Stack 集线器](azure-stack-registration.md)，并向[Azure Stack 中心市场](azure-stack-marketplace.md)提供项以提供用户。
