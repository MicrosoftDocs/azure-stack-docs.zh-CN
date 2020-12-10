---
title: 在 Azure Stack Hub 中删除 MySQL 资源提供程序
description: 了解如何从 Azure Stack Hub 部署中删除 MySQL 资源提供程序。
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2
ms.openlocfilehash: 7c3c33371e50df0dabb7db9fc8c0204fc3caaa83
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2020
ms.locfileid: "97011138"
---
# <a name="remove-the-mysql-resource-provider-in-azure-stack-hub"></a>在 Azure Stack Hub 中删除 MySQL 资源提供程序

删除 MySQL 资源提供程序之前，必须删除该提供程序的所有依赖项。 你还需要用来安装资源提供程序的部署包的副本。

> [!NOTE]
> 可以在[部署资源提供程序先决条件](./azure-stack-mysql-resource-provider-deploy.md#prerequisites)中找到资源提供程序安装程序的下载链接。

删除 MySQL 资源提供程序会删除操作员管理的关联计划和配额。 但它不会从宿主服务器中删除租户数据库。

## <a name="to-remove-the-mysql-resource-provider"></a>删除 MySQL 资源提供程序

1. 确认已删除所有现有的 MySQL 资源提供程序依赖项。

   > [!NOTE]
   > 即使依赖资源当前正在使用 MySQL 资源提供程序，也将继续卸载该资源提供程序。
  
2. 获取 MySQL 资源提供程序安装包的副本，然后运行自解压程序，将内容解压缩到一个临时目录。
3. 打开一个权限提升的 PowerShell 控制台新窗口，并切换到解压缩后的 MySQL 资源提供程序安装文件所在的目录。
4. 使用以下参数运行 DeployMySqlProvider.ps1 脚本：
    - **Uninstall**：删除资源提供程序和所有关联的资源。
    - **PrivilegedEndpoint**：特权终结点的 IP 地址或 DNS 名称。
    - **AzureEnvironment**：用于部署 Azure Stack Hub 的 Azure 环境。 仅对于 Azure AD 部署是必需的。
    - **CloudAdminCredential**：访问特权终结点时所需的云管理员凭据。
    - **AzCredential**：Azure Stack Hub 服务管理员帐户的凭据。 使用部署 Azure Stack Hub 时所用的相同凭据。 如果用于 AzCredential 的帐户需要多重身份验证 (MFA)，则脚本将失败。

## <a name="next-steps"></a>后续步骤

[提供应用服务作为 PaaS](azure-stack-app-service-overview.md)
