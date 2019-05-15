---
title: Azure Stack MySQL 资源提供程序 1.1.30.0 发行说明 | Microsoft Docs
description: 了解 Azure Stack MySQL 资源提供程序最新更新版的信息，包括任何已知问题和下载位置。
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
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: e5126f55a60b98ec7c410b7f8f2256ad08fa9753
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618568"
---
# <a name="mysql-resource-provider-11300--release-notes"></a>MySQL 资源提供程序 1.1.30.0 发行说明

适用于：Azure Stack 集成系统和 Azure Stack 开发工具包

本发行说明介绍 MySQL 资源提供程序 1.1.30.0 版中的改进和已知问题。

## <a name="build-reference"></a>内部版本参考
下载 MySQL 资源提供程序二进制文件，然后运行自解压程序，将内容解压缩到一个临时目录。 资源提供程序有一个相应的 Azure Stack 最低内部版本。 下面列出了安装此 MySQL 资源提供程序版本所需的最低 Azure Stack 发行版：

> |最低 Azure Stack 版本|MySQL 资源提供程序版本|
> |-----|-----|
> |Azure Stack 1808 更新 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> 在部署最新版本的 MySQL 资源提供程序之前，请先将支持的最低 Azure Stack 更新版应用到 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包 (ASDK)。

## <a name="new-features-and-fixes"></a>新功能和修复
此 Azure Stack MySQL 资源提供程序版本包含以下改进和修复：

- **为 MySQL 资源提供程序部署启用遥测**。 为 MySQL 资源提供程序部署启用了遥测数据收集功能。 收集的遥测数据包括资源提供程序部署、开始和停止时间、退出状态、退出消息和错误详细信息（如果适用）。

- **TLS 1.2 加密更新**。 为资源提供程序启用了仅限 TLS 1.2 的支持，使其能够与内部 Azure Stack 组件通信。 

### <a name="fixes"></a>修复项

- **MySQL 资源提供程序 Azure Stack PowerShell 兼容性**。 MySQL 资源提供程序经过更新，可与 Azure Stack 2018-03-01-hybrid PowerShell 配置文件配合运行，并与 AzureRM 1.3.0 和更高版本兼容。

- **MySQL 登录 - 更改密码边栏选项卡**。 修复了问题，不能更改密码边栏选项卡上更改密码。 删除了密码更改通知中的链接。

## <a name="known-issues"></a>已知问题 

- **MySQL SKU 最长可能需要在一小时后才显示在门户中**。 创建新的 MySQL 数据库时，新建的 SKU 最长可能需要在一小时后才显示出来并可供使用。 

    **解决方法**：无。

- **重复使用 MySQL 登录名**。 尝试使用与现有登录名相同的用户名在同一订阅下创建新的 MySQL 登录名时，会导致重复使用同一登录名和现有密码的问题。 

    **解决方法**：在同一订阅下创建新登录名时使用不同的用户名，或者在不同订阅下使用相同的用户名创建登录名。

- **TLS 1.2 支持要求**。 如果你尝试从未启用 TLS 1.2 的计算机部署或更新 MySQL 资源提供程序，则操作可能会失败。 在用于部署或更新资源提供程序的计算机上运行以下 PowerShell 命令，以验证是否返回支持 TLS 1.2：

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  如果命令输出中未包含 **Tls12**，则计算机上未启用 TLS 1.2。

    **解决方法**：运行以下 PowerShell 命令以启用 TLS 1.2，然后从同一 PowerShell 会话启动资源提供程序部署或更新脚本：

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>云管理员操作 Azure Stack 时的已知问题
请参阅 [Azure Stack 发行说明](azure-stack-servicing-policy.md)中的文档。

## <a name="next-steps"></a>后续步骤
[详细了解 MySQL 资源提供程序](azure-stack-mysql-resource-provider.md)。

[准备部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md#prerequisites)。

[从旧版升级 MySQL 资源提供程序](azure-stack-mysql-resource-provider-update.md)。 