---
title: 在 Azure Stack 中删除 MySQL 资源提供程序 |Microsoft Docs
description: 了解如何从 Azure Stack 部署中删除 MySQL 资源提供程序。
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: a2b55707bc05aecf8681cb866c58b0ed34fb87cd
ms.sourcegitcommit: a23b80b57668615c341c370b70d0a106a37a02da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72682165"
---
# <a name="remove-the-mysql-resource-provider-in-azure-stack"></a>在 Azure Stack 中删除 MySQL 资源提供程序

删除 MySQL 资源提供程序之前，必须删除所有提供程序依赖项。 还需要一个用于安装资源提供程序的部署包的副本。

> [!NOTE]
> 可以在[部署资源提供程序必备组件](./azure-stack-mysql-resource-provider-deploy.md#prerequisites)中找到资源提供程序安装程序的下载链接。

删除 MySQL 资源提供程序不会从宿主服务器中删除租户数据库。

## <a name="dependency-cleanup"></a>依赖项清理

在运行 DeployMySqlProvider 脚本以删除资源提供程序之前，需要执行几个清理任务。

Azure Stack 运算符负责以下清理任务：

* 删除引用 MySQL 适配器的任何计划。
* 删除与 MySQL 适配器关联的任何配额。

## <a name="to-remove-the-mysql-resource-provider"></a>删除 MySQL 资源提供程序

1. 验证是否已删除所有现有 MySQL 资源提供程序依赖项。

   > [!NOTE]
   > 即使依赖资源当前正在使用资源提供程序，卸载 MySQL 资源提供程序也将继续。
  
2. 获取 MySQL 资源提供程序安装包的副本，然后运行自解压缩程序以将内容提取到临时目录。
3. 打开一个新的提升的 PowerShell 控制台窗口，并更改为你在其中提取 MySQL 资源提供程序安装文件的目录。
4. 使用以下参数运行 DeployMySqlProvider 脚本：
    - **卸载**：删除资源提供程序和所有关联的资源。
    - **PrivilegedEndpoint**：特权终结点的 IP 地址或 DNS 名称。
    - **AzureEnvironment**：用于部署 Azure Stack 的 Azure 环境。 仅 Azure AD 部署中需要。
    - **CloudAdminCredential**：访问特权终结点所需的云管理员凭据。
    - **DirectoryTenantID**
    - **AzCredential**： Azure Stack 服务管理员帐户的凭据。 使用用于部署 Azure Stack 的相同凭据。

## <a name="next-steps"></a>后续步骤

[将应用服务作为 PaaS 提供](azure-stack-app-service-overview.md)
