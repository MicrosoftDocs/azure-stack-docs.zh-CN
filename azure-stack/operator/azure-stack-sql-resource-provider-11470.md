---
title: Azure Stack Hub SQL 资源提供程序 1.1.47.0 发行说明
description: 查看最新的 Azure Stack Hub SQL 资源提供程序更新中的新增内容，包括新功能、修复和已知问题。
author: PatAltimore
ms.topic: article
ms.date: 11/26/2019
ms.author: patricka
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: d95be92f4cab37c41e1802fdcb18969e3276f459
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869213"
---
# <a name="sql-resource-provider-11470-release-notes"></a>SQL 资源提供程序 1.1.47.0 发行说明

本发行说明介绍 SQL 资源提供程序 1.1.47.0 版中的改进和已知问题。

## <a name="build-reference"></a>内部版本参考

下载 SQL 资源提供程序二进制文件，然后运行自解压程序，将内容解压缩到一个临时目录。 资源提供程序具有相应的最低 Azure Stack Hub 版本。 下面列出了安装此 SQL 资源提供程序版本所需的最低 Azure Stack Hub 发行版：

> |最低 Azure Stack Hub 版本|SQL 资源提供程序版本|
> |-----|-----|
> |版本 1910 (1.1910.0.58)|[SQL RP 版本 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> 在部署最新版本的 SQL 资源提供程序之前，请先将支持的最低 Azure Stack Hub 更新应用到 Azure Stack Hub 集成系统。

## <a name="new-features-and-fixes"></a>新功能和修复

此版本的 Azure Stack Hub SQL 资源提供程序是一个修补程序版本，可使资源提供程序与 1910 更新中的最新门户更改兼容。 没有任何新功能。

它还支持最新的 Azure Stack Hub API 版本配置文件 2019-03-01-hybrid 和 Azure Stack Hub PowerShell 模块 1.8.0。 因此，部署和更新期间无需安装模块的特定历史版本。

Azure Stack Hub 升级到 1910 更新后，请按照资源提供程序更新过程应用 SQL 资源提供程序修补程序 1.1.47.0。 它将帮助解决管理员门户中的已知问题，在该门户中，SQL 资源提供程序中的容量监视一直保持加载状态。

## <a name="known-issues"></a>已知问题

为 Azure Stack Hub 集成系统[轮换证书](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation)时，即使不打算更新 Key Vault 证书密码，也必须使用 KeyVaultPfxPassword 参数。

## <a name="next-steps"></a>后续步骤

- [详细了解 SQL 资源提供程序](azure-stack-sql-resource-provider.md)。
- [准备部署 SQL 资源提供程序](azure-stack-sql-resource-provider-deploy.md#prerequisites)。
- [从旧版升级 SQL 资源提供程序](azure-stack-sql-resource-provider-update.md)。
