---
title: Azure Stack MySQL 资源提供程序1.1.47.0 发行说明 |Microsoft Docs
description: 查看发行说明，了解 Azure Stack MySQL 资源提供程序1.1.47.0 更新中的新增功能。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: 979aa822c981b4d34cb430e69dc15f80661818fe
ms.sourcegitcommit: 11e0c2d9abbc0a2506f992976b3c9f8ca4e746b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823477"
---
# <a name="mysql-resource-provider-11470-release-notes"></a>MySQL 资源提供程序1.1.47.0 发行说明

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

这些发行说明介绍了 MySQL 资源提供程序版本1.1.47.0 中的改进和已知问题。

## <a name="build-reference"></a>内部版本参考
下载 MySQL 资源提供程序二进制文件，然后运行自解压缩程序，将内容提取到临时目录中。 资源提供程序至少有一个对应的 Azure Stack 生成。 安装此版本的 MySQL 资源提供程序所需的最低 Azure Stack 发行版本如下所示：

> |最低 Azure Stack 版本|MySQL 资源提供程序版本|
> |-----|-----|
> |版本1910（1.1910.0.58）|[MySQL RP 版本1.1.47。0](https://aka.ms/azurestackmysqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> 在部署最新版本的 MySQL 资源提供程序之前，将支持的最低 Azure Stack 更新应用到 Azure Stack 集成系统或部署最新 Azure Stack 开发工具包（ASDK）。

## <a name="new-features-and-fixes"></a>新增功能和修补程序

此版本的 Azure Stack MySQL 资源提供程序是一个修补程序版本，可使资源提供程序与1910更新中的某些最新门户更改兼容，无需任何新功能。

它还支持当前最新的 Azure Stack API 版本配置文件 2019-03-01-混合和 Azure Stack PowerShell 模块1.8.0。 因此，在部署和更新过程中，无需安装特定的模块历史记录版本。

建议在将 Azure Stack 升级到1910版本后，应用 MySQL 资源提供程序修补程序1.1.47.0。

## <a name="known-issues"></a>已知问题

无。

## <a name="next-steps"></a>后续步骤
[了解有关 MySQL 资源提供程序的详细信息](azure-stack-mysql-resource-provider.md)。

[准备部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md#prerequisites)。

[从以前的版本升级 MySQL 资源提供程序](azure-stack-mysql-resource-provider-update.md)。 
