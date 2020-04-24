---
title: Azure Stack Hub MySQL 资源提供程序 1.1.47.0 发行说明
description: 查看发行说明以了解 Azure Stack Hub MySQL 资源提供程序 1.1.47.0 更新中的新增功能。
author: justinha
ms.topic: article
ms.date: 11/26/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: 4984f36b4632f2bcb204c93ef409ea82a7f299f5
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77698821"
---
# <a name="mysql-resource-provider-11470-release-notes"></a>MySQL 资源提供程序 1.1.47.0 发行说明

本发行说明描述 MySQL 资源提供程序 1.1.47.0 版中的改进和已知问题。

## <a name="build-reference"></a>内部版本参考
下载 MySQL 资源提供程序二进制文件，然后运行自解压程序，将内容解压缩到一个临时目录。 资源提供程序具有相应的最低 Azure Stack Hub 版本。 下面列出了安装此 MySQL 资源提供程序版本所需的最低 Azure Stack Hub 发行版：

> |最低 Azure Stack Hub 版本|MySQL 资源提供程序版本|
> |-----|-----|
> |版本 1910 (1.1910.0.58)|[MySQL RP 版本 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> 在部署最新版本的 MySQL 资源提供程序之前，请先将支持的最低 Azure Stack Hub 更新版应用到 Azure Stack Hub 集成系统，或部署最新的 Azure Stack 开发工具包 (ASDK)。

## <a name="new-features-and-fixes"></a>新功能和修复

此版本的 Azure Stack Hub MySQL 资源提供程序是一个修补程序版本，可使资源提供程序与 1910 更新中的某些最新门户更改兼容，而没有任何新功能。

它还支持当前最新的 Azure Stack Hub API 版本配置文件 2019-03-01-hybrid 和 Azure Stack Hub PowerShell 模块 1.8.0。 因此，部署和更新期间无需安装模块的特定历史版本。

建议在将 Azure Stack Hub 升级到 1910 版本后应用 MySQL 资源提供程序修补程序 1.1.47.0。

## <a name="known-issues"></a>已知问题

无。

## <a name="next-steps"></a>后续步骤
[详细了解 MySQL 资源提供程序](azure-stack-mysql-resource-provider.md)。

[准备部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md#prerequisites)。

[从旧版升级 MySQL 资源提供程序](azure-stack-mysql-resource-provider-update.md)。 
