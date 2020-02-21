---
title: 在 Azure Stack 中心内删除 MySQL 资源提供程序
description: 了解如何从 Azure Stack 中心部署中删除 MySQL 资源提供程序。
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: e432e0a37dbcea7bf441594ba6669ee15578b3e4
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77492047"
---
# <a name="remove-the-mysql-resource-provider-in-azure-stack-hub"></a>在 Azure Stack 中心内删除 MySQL 资源提供程序

删除 MySQL 资源提供程序之前，必须删除所有提供程序依赖项。 还需要一个用于安装资源提供程序的部署包的副本。

> [!NOTE]
> 可以在[部署资源提供程序必备组件](./azure-stack-mysql-resource-provider-deploy.md#prerequisites)中找到资源提供程序安装程序的下载链接。

删除 MySQL 资源提供程序将删除操作员管理的关联计划和配额。 但它不会从宿主服务器中删除租户数据库。

## <a name="to-remove-the-mysql-resource-provider"></a>删除 MySQL 资源提供程序

1. 验证是否已删除所有现有 MySQL 资源提供程序依赖项。

   > [!NOTE]
   > 即使依赖资源当前正在使用资源提供程序，卸载 MySQL 资源提供程序也将继续。
  
2. 获取 MySQL 资源提供程序安装包的副本，然后运行自解压缩程序以将内容提取到临时目录。
3. 打开一个新的提升的 PowerShell 控制台窗口，并更改为你在其中提取 MySQL 资源提供程序安装文件的目录。
4. 使用以下参数运行 DeployMySqlProvider 脚本：
    - **卸载**：删除资源提供程序和所有关联的资源。
    - **PrivilegedEndpoint**：特权终结点的 IP 地址或 DNS 名称。
    - **AzureEnvironment**：用于部署 Azure Stack 集线器的 Azure 环境。 仅 Azure AD 部署中需要。
    - **CloudAdminCredential**：访问特权终结点所需的云管理员凭据。
    - **DirectoryTenantID**
    - **AzCredential**： Azure Stack 中心服务管理员帐户的凭据。 使用用于部署 Azure Stack 集线器的相同凭据。

## <a name="next-steps"></a>后续步骤

[将应用服务作为 PaaS 提供](azure-stack-app-service-overview.md)
