---
title: Azure Stack 中心 SQL 资源提供程序1.1.47.0 发行说明
description: 了解最新 Azure Stack 中心 SQL 资源提供程序更新中的内容，包括任何已知问题以及下载位置。
author: justinha
ms.topic: article
ms.date: 11/26/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 11/26/2019
ms.openlocfilehash: 9303a9d60fad754a5ba644d4b6e1c5cd3d331497
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77697410"
---
# <a name="sql-resource-provider-11470-release-notes"></a>SQL 资源提供程序1.1.47.0 发行说明

这些发行说明介绍了 SQL 资源提供程序版本1.1.47.0 中的改进和已知问题。

## <a name="build-reference"></a>内部版本参考
下载 SQL 资源提供程序二进制文件，然后运行自解压缩程序，将内容提取到临时目录中。 资源提供程序至少有一个对应的 Azure Stack 中心生成。 安装此版本的 SQL 资源提供程序所需的最低 Azure Stack 中心版本如下所示：

> |最低 Azure Stack 中心版本|SQL 资源提供程序版本|
> |-----|-----|
> |版本1910（1.1910.0.58）|[SQL RP 版本1.1.47。0](https://aka.ms/azurestacksqlrp11470)|  
> |     |     |

> [!IMPORTANT]
> 部署最新版本的 SQL 资源提供程序之前，请将支持的最低 Azure Stack 中心更新应用到 Azure Stack 中心集成系统。

## <a name="new-features-and-fixes"></a>新增功能和修补程序

此版本的 Azure Stack 中心 SQL 资源提供程序是一个修补程序版本，可使资源提供程序与1910更新中的某些最新门户更改兼容，无需任何新功能。

它还支持当前最新的 Azure Stack 中心 API 版本配置文件 2019-03-01-混合和 Azure Stack 中心 PowerShell 模块1.8.0。 因此，在部署和更新过程中，无需安装特定的模块历史记录版本。

Azure Stack 中心升级到1910更新后，请按照资源提供程序更新过程应用 SQL 资源提供程序修补程序1.1.47.0。 它将帮助解决在 SQL 资源提供程序中进行容量监视的管理门户中的已知问题。

## <a name="known-issues"></a>已知问题

无。

## <a name="next-steps"></a>后续步骤
[了解有关 SQL 资源提供程序的详细信息](azure-stack-sql-resource-provider.md)。

[准备部署 SQL 资源提供程序](azure-stack-sql-resource-provider-deploy.md#prerequisites)。

[从以前的版本升级 SQL 资源提供程序](azure-stack-sql-resource-provider-update.md)。 
