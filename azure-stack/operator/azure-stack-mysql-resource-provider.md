---
title: 在 Azure Stack 中心使用 MySQL 数据库作为 PaaS
description: 了解如何部署 MySQL 资源提供程序，以及如何在 Azure Stack 中心提供 MySQL 数据库即服务。
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: 73c916a193492662cdbb3ba20031c58932721053
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698583"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack-hub"></a>在 Microsoft Azure Stack 中心使用 MySQL 数据库

使用 MySQL 资源提供程序在[Azure Stack 中心](azure-stack-overview.md)提供 MySQL 数据库。 部署资源提供程序并将其连接到一个或多个 MySQL 服务器实例后，可以创建：

* 适用于云原生应用的 MySQL 数据库。
* 用于 web 应用程序的 MySQL 数据库。  

安装 MySQL 资源提供程序之前，请考虑以下几个限制：

- 用户只能创建和管理单个数据库。 最终用户无法访问数据库服务器实例。 这可能会限制与需要访问 master、Temp DB 或动态管理数据库的本地数据库应用程序的兼容性。
- Azure Stack 中心操作员负责部署、更新、保护、配置和维护 MySQL 数据库服务器和主机。 RP 服务不提供任何主机和数据库服务器实例管理功能。 
- 不同订阅中不同用户的数据库可以位于同一个数据库服务器实例上。 RP 不提供隔离不同主机或数据库服务器实例上的数据库的任何机制。
- RP 不提供数据库的租户使用情况报告。

## <a name="mysql-resource-provider-adapter-architecture"></a>MySQL 资源提供程序适配器体系结构

资源提供程序具有以下组件：

* **MySQL 资源提供程序适配器虚拟机（vm）** ，这是运行提供程序服务的 WINDOWS Server vm。
* **资源提供程序**，用于处理请求并访问数据库资源。
* **承载 MySQL Server 的服务器**，它为称为宿主服务器的数据库提供容量。 你可以自己创建 MySQL 实例，或提供对外部 MySQL 实例的访问权限。 [Azure Stack 中心快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows)提供了一个示例模板，可用于：

  * 创建 MySQL 服务器。
  * 从 Azure Marketplace 下载并部署 MySQL Server。

> [!NOTE]
> 必须通过租户订阅创建安装在 Azure Stack 集线器集成系统上的宿主服务器。 不能通过默认提供程序订阅来创建它们。 必须使用用户门户或使用适当的登录从 PowerShell 会话创建它们。 所有宿主服务器都是可计费的 Vm，并且必须具有许可证。 服务管理员可以是租户订阅的所有者。

### <a name="required-privileges"></a>必需的权限

系统帐户必须具有以下权限：

* **数据库：** 创建、删除
* **登录名：** create、set、drop、grant、revoke  

## <a name="next-steps"></a>后续步骤

[部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)
