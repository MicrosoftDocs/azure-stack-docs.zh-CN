---
title: 使用 SQL 数据库
titleSuffix: Azure Stack Hub
description: 了解如何使用 SQL Server 资源提供程序将 SQL 数据库作为 Azure Stack Hub 的一项服务提供。
author: bryanla
ms.topic: article
ms.date: 10/02/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: 15eaf26162b0d3f647d65dfab66e3d9327b2f357
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77697138"
---
# <a name="use-sql-databases-on-azure-stack-hub"></a>在 Azure Stack Hub 中使用 SQL 数据库

在 [Azure Stack Hub](azure-stack-overview.md) 上使用 SQL 资源提供程序来提供 SQL 数据库。 在安装资源提供程序并将其连接到一个或多个 SQL Server 实例后，你和你的用户可以创建：

- 适用于云原生应用的 SQL 数据库。
- 用于 Web 应用程序的 SQL 数据库。

安装 SQL 资源提供程序之前需要考虑的限制：

- 用户只能创建和管理单个数据库。 最终用户无法访问数据库服务器实例。 这可能会限制与需要访问 master、Temp DB 或动态管理数据库的本地数据库应用的兼容性。
- 你的 Azure Stack Hub 操作员负责部署、更新、保护、配置和维护 SQL 数据库服务器和主机。 RP 服务不提供任何主机和数据库服务器实例管理功能。
- 不同订阅中不同用户的数据库可以位于同一个数据库服务器实例上。 RP 不提供隔离不同主机或数据库服务器实例上的数据库的任何机制。
- RP 不提供关于数据库的租户使用情况的任何报告。

对于本地的传统 SQL Server 工作负载，建议使用 Azure Stack Hub 上的 SQL Server 虚拟机。

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 资源提供程序适配器体系结构

该资源提供程序由以下三个组件构成：

- **SQL 资源提供程序适配器虚拟机 (VM)** ，这是运行提供程序服务的 Windows Server VM。
- **资源提供程序**，它处理请求并访问数据库资源。
- **托管 SQL 服务器的服务器**，为称作宿主服务器的数据库提供容量。

必须创建至少一个 SQL Server 实例，或者提供对外部 SQL Server 实例的访问权限。

> [!NOTE]
> 必须通过租户订阅创建安装在 Azure Stack Hub 集成系统上的宿主服务器， 而不能通过默认提供商订阅创建。 必须从用户门户或者使用 PowerShell 以及相应的登录名来创建它们。 所有宿主服务器都是可计费的 VM，并且必须具有许可证。 服务管理员可以是租户订阅的所有者。

## <a name="next-steps"></a>后续步骤

[部署 SQL 服务器资源提供程序](azure-stack-sql-resource-provider-deploy.md)
