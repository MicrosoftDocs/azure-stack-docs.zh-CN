---
title: 使用 SQL 数据库
titleSuffix: Azure Stack Hub
description: 了解如何使用 SQL Server 资源提供程序在 Azure Stack 中心提供 SQL 数据库即服务。
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
ms.openlocfilehash: cc8a5e11c6cc4b01b76b9a8fd94f97227ae8388c
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814348"
---
# <a name="use-sql-databases-on-azure-stack-hub"></a>使用 Azure Stack 集线器上的 SQL 数据库

使用 SQL 资源提供程序在[Azure Stack 中心](azure-stack-overview.md)提供 sql 数据库即服务。 在安装资源提供程序并将其连接到一个或多个 SQL Server 实例后，你和你的用户可以创建：

- 适用于云原生应用的数据库。
- 使用 SQL 的网站。
- 使用 SQL 的工作负荷。

安装 SQL 资源提供程序之前，需要考虑几个限制：

- 用户只能创建和管理单个数据库。 最终用户无法访问数据库服务器实例。 这可能会限制与需要访问 master、Temp DB 或动态管理数据库的本地数据库应用程序的兼容性。
- Azure Stack 中心操作员负责部署、更新、保护、配置和维护 SQL 数据库服务器和主机。 RP 服务不提供任何主机和数据库服务器实例管理功能。 
- 不同订阅中不同用户的数据库可以位于同一个数据库服务器实例上。 RP 不提供隔离不同主机或数据库服务器实例上的数据库的任何机制。
- RP 不提供数据库的租户使用情况报告。

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 资源提供程序适配器体系结构

资源提供程序包含以下组件：

- **SQL 资源提供程序适配器虚拟机（vm）** ，这是运行提供程序服务的 WINDOWS Server vm。
- **资源提供程序**，用于处理请求并访问数据库资源。
- **承载 SQL Server 的服务器**，它为称为宿主服务器的数据库提供容量。

必须创建至少一个 SQL Server 实例，或提供对外部 SQL Server 实例的访问权限。

> [!NOTE]
> 必须通过租户订阅创建安装在 Azure Stack 集线器集成系统上的宿主服务器。 不能通过默认提供程序订阅来创建它们。 必须通过租户门户或使用具有相应登录的 PowerShell 来创建它们。 所有宿主服务器都是可计费的 Vm，并且必须具有许可证。 服务管理员可以是租户订阅的所有者。

## <a name="next-steps"></a>后续步骤

[部署 SQL 服务器资源提供程序](azure-stack-sql-resource-provider-deploy.md)
