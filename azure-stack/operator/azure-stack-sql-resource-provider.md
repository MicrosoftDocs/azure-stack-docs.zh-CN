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
ms.openlocfilehash: d2ce6c0af2912a2658db80301c9a64c8e3d5c066
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780722"
---
# <a name="use-sql-databases-on-azure-stack"></a>在 Azure Stack 上使用 SQL 数据库

使用 SQL Server 资源提供程序在[Azure Stack](azure-stack-overview.md)提供 SQL 数据库即服务。 在安装资源提供程序并将其连接到一个或多个 SQL Server 实例后，你和你的用户可以创建：

- 适用于云原生应用的数据库。
- 使用 SQL 的网站。
- 使用 SQL 的工作负荷。

资源提供程序不提供[AZURE SQL 数据库](https://azure.microsoft.com/services/sql-database/)的所有数据库管理功能。 例如，不支持自动分配资源的弹性池。 但是，资源提供程序支持对 SQL Server 数据库的相似的创建、读取、更新和删除（CRUD）操作。

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
