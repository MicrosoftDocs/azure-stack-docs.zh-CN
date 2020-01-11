---
title: Azure Stack 中心 MySQL 资源提供程序1.1.30.0 发行说明 |Microsoft Docs
description: 查看发行说明，了解 Azure Stack Hub MySQL 资源提供程序1.1.30.0 更新中的新增功能。
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
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 59a998fbbd209402f03a532f3ce421ed3df53408
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882430"
---
# <a name="mysql-resource-provider-11300-release-notes"></a>MySQL 资源提供程序 1.1.30.0 发行说明

这些发行说明介绍了 MySQL 资源提供程序版本1.1.30.0 中的改进和已知问题。

## <a name="build-reference"></a>内部版本参考
下载 MySQL 资源提供程序二进制文件，然后运行自解压缩程序，将内容提取到临时目录中。 资源提供程序至少有一个对应的 Azure Stack 中心生成。 安装此版本的 MySQL 资源提供程序所需的最低 Azure Stack 中心版本如下所示：

> |最低 Azure Stack 中心版本|MySQL 资源提供程序版本|
> |-----|-----|
> |Azure Stack 中心1808更新（1.1808.0.97）|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> 在部署最新版本的 MySQL 资源提供程序之前，将支持的最低 Azure Stack 中心更新应用到 Azure Stack 中心集成系统或部署最新 Azure Stack 开发工具包（ASDK）。

## <a name="new-features-and-fixes"></a>新增功能和修补程序
此版本的 Azure Stack 集线器 MySQL 资源提供程序包括以下改进和修补程序：

- **已为 MySQL 资源提供程序部署启用遥测**。 已为 MySQL 资源提供程序部署启用了遥测收集。 收集的遥测包括资源提供程序部署、开始和停止时间、退出状态、退出消息和错误详细信息（如果适用）。

- **TLS 1.2 加密更新**。 启用了 TLS 1.2-仅支持与内部 Azure Stack 集线器组件进行资源提供程序通信。 

### <a name="fixes"></a>修复项

- **MySQL 资源提供程序 Azure Stack 集线器 PowerShell 兼容性**。 MySQL 资源提供程序已更新为可与 Azure Stack 中心2018-03-01 混合 PowerShell 配置文件一起使用，并提供与 AzureRM 1.3.0 和更高版本的兼容性。

- **MySQL 登录更改密码边栏选项卡**。 修复了 "更改密码" 边栏选项卡上无法更改密码的问题。 删除了来自密码更改通知的链接。

## <a name="known-issues"></a>已知问题

- **MySQL sku 最多可能需要一小时才能在门户中显示**。 创建新的 MySQL 数据库时，最多可能需要一小时才能看到新创建的 Sku。

    **解决方法**：无。

- **重复使用 MySQL 登录名**。 尝试使用同一订阅下的现有登录名创建新的 MySQL 登录名将导致重复使用相同的登录名和现有密码。

    **解决方法**：在同一订阅下创建新登录名时使用不同的用户名，或在不同的订阅下使用相同的用户名创建登录名。

- **TLS 1.2 支持要求**。 如果尝试从不启用 TLS 1.2 的计算机部署或更新 MySQL 资源提供程序，则操作可能会失败。 在用于部署或更新资源提供程序的计算机上运行以下 PowerShell 命令，以验证是否按支持返回了 TLS 1.2：

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  如果命令的输出中不包括**Tls12** ，则不会在计算机上启用 TLS 1.2。

    **解决方法**：运行以下 PowerShell 命令以启用 TLS 1.2，然后启动资源提供程序部署或从同一 PowerShell 会话更新脚本：

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Azure Stack 中心的云管理员操作的已知问题
请参阅[Azure Stack 中心发行说明](azure-stack-servicing-policy.md)中的文档。

## <a name="next-steps"></a>后续步骤
[了解有关 MySQL 资源提供程序的详细信息](azure-stack-mysql-resource-provider.md)。

[准备部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md#prerequisites)。

[从以前的版本升级 MySQL 资源提供程序](azure-stack-mysql-resource-provider-update.md)。 