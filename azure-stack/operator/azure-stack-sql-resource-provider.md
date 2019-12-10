---
title: 使用 SQL 数据库
titleSuffix: Azure Stack
description: 了解如何使用 SQL Server 资源提供程序在 Azure Stack 提供 SQL 数据库即服务。
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
ms.openlocfilehash: 4aef28f0351a89f02dc4c00cd042d6bdd33ee957
ms.sourcegitcommit: 6bb20ed3dcbd64231331a8e807ba69eff8b7439b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74946795"
---
# <a name="use-sql-databases-on-azure-stack"></a>在 Azure Stack 上使用 SQL 数据库

使用 SQL Server 资源提供程序在[Azure Stack](azure-stack-overview.md)提供 SQL 数据库即服务。 在安装资源提供程序并将其连接到一个或多个 SQL Server 实例后，你和你的用户可以创建：

- 适用于云原生应用的数据库。
- 使用 SQL 的网站。
- 使用 SQL 的工作负荷。

资源提供程序不提供[AZURE SQL 数据库](https://azure.microsoft.com/services/sql-database/)的所有数据库管理功能。 例如，不支持自动分配资源的弹性池。 但是，资源提供程序支持对 SQL Server 数据库的相似的创建、读取、更新和删除（CRUD）操作。

Azure Stack 操作员负责部署、配置和维护数据库服务器实例，以实现安全性、HA、备份、修补和更新。 数据库服务器实例由不同的用户数据库共享，包括数据库服务器名称和公共 IP 地址。 并且不会报告数据库使用情况。

## <a name="sql-resource-provider-adapter-architecture"></a>SQL 资源提供程序适配器体系结构

资源提供程序包含以下组件：

- **SQL 资源提供程序适配器虚拟机（vm）** ，这是运行提供程序服务的 WINDOWS Server vm。
- **资源提供程序**，用于处理请求并访问数据库资源。
- **承载 SQL Server 的服务器**，它为称为宿主服务器的数据库提供容量。

必须创建至少一个 SQL Server 实例，或提供对外部 SQL Server 实例的访问权限。

> [!NOTE]
> 必须通过租户订阅创建安装在 Azure Stack 集成系统上的宿主服务器。 不能通过默认提供程序订阅来创建它们。 必须通过租户门户或使用具有相应登录的 PowerShell 来创建它们。 所有宿主服务器都是可计费的 Vm，并且必须具有许可证。 服务管理员可以是租户订阅的所有者。

## <a name="next-steps"></a>后续步骤

[部署 SQL 服务器资源提供程序](azure-stack-sql-resource-provider-deploy.md)
