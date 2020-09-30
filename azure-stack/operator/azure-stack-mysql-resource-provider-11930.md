---
title: Azure Stack 中心 MySQL 资源提供程序1.1.93.0 发行说明
description: 查看发行说明，了解 Azure Stack Hub MySQL 资源提供程序1.1.93.0 更新中的新增功能。
author: caoyang
ms.topic: article
ms.date: 09/22/2020
ms.author: caoyang
ms.reviewer: xiaofmao
ms.lastreviewed: 09/22/2020
ms.openlocfilehash: f298e2e9e55d8057a63264fb347069ef07204a16
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91585894"
---
# <a name="mysql-resource-provider-11930-release-notes"></a>MySQL 资源提供程序1.1.93.0 发行说明

这些发行说明介绍了 MySQL 资源提供程序版本1.1.93.0 中的改进和已知问题。

## <a name="build-reference"></a>内部版本参考
下载 MySQL 资源提供程序二进制文件，然后运行自解压程序，将内容解压缩到一个临时目录。 资源提供程序具有相应的最低 Azure Stack Hub 版本。 下面列出了安装此 MySQL 资源提供程序版本所需的最低 Azure Stack Hub 发行版：

> |支持的 Azure Stack Hub 版本|MySQL 资源提供程序版本|
> |-----|-----|
> |版本2005|[MySQL RP 版本1.1.93。0](https://aka.ms/azshmysqlrp11930)|  
> |     |     |

> [!IMPORTANT]
> 部署最新版本的 MySQL 资源提供程序之前，请将支持的最低 Azure Stack 中心更新应用到 Azure Stack 中心集成系统。

## <a name="new-features-and-fixes"></a>新功能和修复

此版本的 Azure Stack 集线器 MySQL 资源提供程序包括以下改进和修补程序：

- **将基本 VM 更新为专用 Windows Server。** 此 Windows Server 版本专用于 Azure Stack 中心附加 RP 基础结构，它对租户 marketplace 不可见。 在部署或升级到此版本的 MySQL 资源提供程序之前，请确保下载 **Microsoft Test-azurestack 外接程序 RP Windows SERVER 内部** 映像。
- **支持删除孤立的数据库元数据和宿主服务器元数据。** 当无法再连接托管服务器时，租户将提供从门户中删除孤立数据库元数据的选项。 如果没有与宿主服务器关联的孤立数据库元数据，操作员将能够从管理门户中删除孤立的宿主服务器元数据。
- **执行机密旋转时，请将 KeyVaultPfxPassword 设置为可选参数。** 有关详细信息，请查看 [此文档](azure-stack-sql-resource-provider-maintain.md#secrets-rotation) 。
- **其他 bug 修复。**

建议在 Azure Stack Hub 升级到2005版本后，应用 MySQL 资源提供程序1.1.93.0。

## <a name="known-issues"></a>已知问题
无。

## <a name="next-steps"></a>后续步骤

- [详细了解 MySQL 资源提供程序](azure-stack-mysql-resource-provider.md)。
- [准备部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md#prerequisites)。
- [从旧版升级 MySQL 资源提供程序](azure-stack-mysql-resource-provider-update.md)。
