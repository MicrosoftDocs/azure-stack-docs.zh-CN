---
title: Azure Stack Hub SQL 资源提供程序 1.1.93.0 发行说明
description: 查看发行说明以了解 Azure Stack Hub SQL 资源提供程序 1.1.93.0 更新中的新增功能。
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: 5becaf3bca62d4e37bac266649bf3821dc5a652e
ms.sourcegitcommit: af4374755cb4875a7cbed405b821f5703fa1c8cc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95812679"
---
# <a name="sql-resource-provider-11930-release-notes"></a>SQL 资源提供程序 1.1.93.0 发行说明

本发行说明介绍 SQL 资源提供程序 1.1.93.0 版中的改进和已知问题。

## <a name="build-reference"></a>内部版本参考
下载 SQL 资源提供程序二进制文件，然后运行自解压程序，将内容解压缩到一个临时目录。 资源提供程序具有相应的最低 Azure Stack Hub 版本。 下面列出了安装此 SQL 资源提供程序版本所需的最低 Azure Stack Hub 发行版：

> |支持的 Azure Stack Hub 版本|SQL 资源提供程序版本|
> |-----|-----|
> |版本2008、2005|[SQL RP 版本 1.1.93.0](https://aka.ms/azshsqlrp11930)|  
> |     |     |

> [!IMPORTANT]
> 在部署最新版本的 MySQL 资源提供程序之前，请先将支持的最低 Azure Stack Hub 更新应用到 Azure Stack Hub 集成系统。

## <a name="new-features-and-fixes"></a>新功能和修复

此 Azure Stack Hub SQL 资源提供程序版本包含以下改进和修复：

- 将基础 VM 更新到专用 Windows Server。 此 Windows Server 版本专用于 Azure Stack Hub Add-On RP Infrastructure，对租户市场不可见。 在部署或升级到此版本的 SQL 资源提供程序之前，请确保先下载 Microsoft AzureStack Add-On RP Windows Server INTERNAL ONLY 映像。
- 支持删除孤立的数据库元数据和宿主服务器元数据。 如果无法再连接宿主服务器，租户可以选择从门户中删除孤立的数据库元数据。 当没有链接到宿主服务器的孤立数据库元数据时，操作员将能够从管理门户中删除孤立的宿主服务器元数据。
- 使 Make KeyVaultPfxPassword 成为执行机密轮换时可选的参数。 有关详细信息，请参阅[此文档](azure-stack-sql-resource-provider-maintain.md#secrets-rotation)。
- 其他 bug 修复。

建议在将 Azure Stack Hub 升级到 2005 版本后应用 SQL 资源提供程序 1.1.93.0。

## <a name="known-issues"></a>已知问题
无。

## <a name="next-steps"></a>后续步骤

- [详细了解 SQL 资源提供程序](azure-stack-sql-resource-provider.md)。
- [准备部署 SQL 资源提供程序](azure-stack-sql-resource-provider-deploy.md#prerequisites)。
- [从旧版升级 SQL 资源提供程序](azure-stack-sql-resource-provider-update.md)。

