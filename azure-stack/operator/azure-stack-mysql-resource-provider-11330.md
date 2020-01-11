---
title: Azure Stack 中心 MySQL 资源提供程序1.1.33.0 发行说明 |Microsoft Docs
description: 查看发行说明，了解 Azure Stack Hub MySQL 资源提供程序1.1.33.0 更新中的新增功能。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 4067068090a4ae243b40af4f382a80c8bfc14455
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882226"
---
# <a name="mysql-resource-provider-11330--release-notes"></a>MySQL 资源提供程序1.1.33.0 发行说明

这些发行说明介绍了 MySQL 资源提供程序版本1.1.33.0 中的改进和已知问题。

## <a name="build-reference"></a>内部版本参考
下载 MySQL 资源提供程序二进制文件，然后运行自解压缩程序，将内容提取到临时目录中。 资源提供程序至少有一个对应的 Azure Stack 中心生成。 安装此版本的 MySQL 资源提供程序所需的最低 Azure Stack 中心版本如下所示：

> |最低 Azure Stack 中心版本|MySQL 资源提供程序版本|
> |-----|-----|
> |版本1808（1.1808.0.97）|[MySQL RP 版本1.1.33。0](https://aka.ms/azurestackmysqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> 在部署最新版本的 MySQL 资源提供程序之前，将支持的最低 Azure Stack 中心更新应用到 Azure Stack 中心集成系统或部署最新 Azure Stack 开发工具包（ASDK）。

## <a name="new-features-and-fixes"></a>新增功能和修补程序
此版本的 Azure Stack 集线器 MySQL 资源提供程序包括以下改进和修补程序：

### <a name="fixes"></a>修复项

- **MySQL 资源提供程序门户扩展可能会选择错误的订阅**。 MySQL 资源提供程序使用 Azure 资源管理器调用来确定要使用的第一个服务管理员订阅，这可能不是*默认的提供程序订阅*。 如果发生这种情况，MySQL 资源提供程序不能正常工作。

- **MySQL 宿主服务器不列出托管的数据库。** 查看 MySQL 宿主服务器的租户资源时，可能不会列出用户创建的数据库。

- **如果未启用 TLS 1.2，则以前的 MySQL 资源提供程序（1.1.30.0）部署可能失败**。 更新了 MySQL 资源提供程序1.1.33.0，以便在部署资源提供程序、更新资源提供程序或轮换机密时启用 TLS 1.2。

- **MySQL 资源提供程序的机密轮换失败**。 修复了轮换机密时导致以下错误代码的问题： `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>已知问题

- **MySQL sku 最多可能需要一小时才能在门户中显示**。 创建新的 MySQL 数据库时，最多可能需要一小时才能看到新创建的 Sku。 

    **解决方法**：无。

- **重复使用 MySQL 登录名**。 尝试使用同一订阅下的现有登录名创建新的 MySQL 登录名将导致重复使用相同的登录名和现有密码。

    **解决方法**：在同一订阅下创建新登录名时使用不同的用户名，或在不同的订阅下使用相同的用户名创建登录名。

- **共享 MySQL 登录将导致数据不一致**。 如果在同一订阅下为多个 MySQL 数据库共享 MySQL 登录，则更改登录密码将导致数据不一致。

    **解决方法**：始终对同一订阅下的不同数据库使用不同的登录名。


### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Azure Stack 中心的云管理员操作的已知问题
请参阅[Azure Stack 中心发行说明](azure-stack-servicing-policy.md)中的文档。

## <a name="next-steps"></a>后续步骤
[了解有关 MySQL 资源提供程序的详细信息](azure-stack-mysql-resource-provider.md)。

[准备部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md#prerequisites)。

[从以前的版本升级 MySQL 资源提供程序](azure-stack-mysql-resource-provider-update.md)。 