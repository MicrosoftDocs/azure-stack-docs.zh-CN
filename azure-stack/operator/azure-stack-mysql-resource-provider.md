---
title: Azure Stack 上使用 MySQL 数据库作为 PaaS |Microsoft Docs
description: 了解如何在 Azure Stack 上部署 MySQL 资源提供程序并提供 MySQL 数据库即服务。
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
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: b66a72ce872d64f8fde3cb80ced5e6ad33d80b4d
ms.sourcegitcommit: d619612f54eeba3231ed73ed149ff894f9bf838a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2019
ms.locfileid: "74993778"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>在 Microsoft Azure Stack 上使用 MySQL 数据库

MySQL 数据库通常与网站结合使用，并支持许多网站平台。 例如，可以使用应用服务资源提供程序（PaaS）加载项创建 WordPress 网站。

部署资源提供程序并将其连接到一个或多个 MySQL 服务器实例后，可以：

* 使用 Azure 资源管理器部署模板创建 MySQL 数据库。
* 提供 MySQL 数据库即服务。  

安装 MySQL 资源提供程序之前，请考虑以下几个限制：

- 用户只能创建和管理单个数据库。 最终用户无法访问数据库服务器实例。 这可能会限制与需要访问 master、Temp DB 或动态管理数据库的本地数据库应用程序的兼容性。
- 你的 Azure Stack 操作员负责部署、更新、保护、配置和维护 MySQL 数据库服务器和主机。 RP 服务不提供任何主机和数据库服务器实例管理功能。 
- 不同订阅中不同用户的数据库可以位于同一个数据库服务器实例上。 RP 不提供隔离不同主机或数据库服务器实例上的数据库的任何机制。
- RP 不提供数据库的租户使用情况报告。

## <a name="mysql-resource-provider-adapter-architecture"></a>MySQL 资源提供程序适配器体系结构

资源提供程序具有以下组件：

* **MySQL 资源提供程序适配器虚拟机（vm）** ，这是运行提供程序服务的 WINDOWS Server vm。
* **资源提供程序**，用于处理请求并访问数据库资源。
* **承载 MySQL Server 的服务器**，它为称为宿主服务器的数据库提供容量。 你可以自己创建 MySQL 实例，或提供对外部 MySQL 实例的访问权限。 [Azure Stack 快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows)提供了一个示例模板，可用于：

  * 创建 MySQL 服务器。
  * 从 Azure Marketplace 下载并部署 MySQL Server。

> [!NOTE]
> 必须通过租户订阅创建安装在 Azure Stack 集成系统上的宿主服务器。 不能通过默认提供程序订阅来创建它们。 必须通过租户门户或使用适当的登录从 PowerShell 会话创建它们。 所有宿主服务器都是可计费的 Vm，并且必须具有许可证。 服务管理员可以是租户订阅的所有者。

### <a name="required-privileges"></a>所需特权

系统帐户必须具有以下权限：

* **数据库：** 创建、删除
* **登录名：** create、set、drop、grant、revoke  

## <a name="next-steps"></a>后续步骤

[部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)
