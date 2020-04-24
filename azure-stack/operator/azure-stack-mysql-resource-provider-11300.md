---
title: Azure Stack Hub MySQL 资源提供程序 1.1.30.0 发行说明
description: 查看发行说明以了解 Azure Stack Hub MySQL 资源提供程序 1.1.30.0 更新中的新增功能。
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: 710b5d48e4ce906eb357c5e17cff4132bcd747ba
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77698923"
---
# <a name="mysql-resource-provider-11300-release-notes"></a>MySQL 资源提供程序 1.1.30.0 发行说明

本发行说明介绍 MySQL 资源提供程序 1.1.30.0 版中的改进和已知问题。

## <a name="build-reference"></a>内部版本参考
下载 MySQL 资源提供程序二进制文件，然后运行自解压程序，将内容解压缩到一个临时目录。 资源提供程序具有相应的最低 Azure Stack Hub 版本。 下面列出了安装此 MySQL 资源提供程序版本所需的最低 Azure Stack Hub 发行版：

> |最低 Azure Stack Hub 版本|MySQL 资源提供程序版本|
> |-----|-----|
> |Azure Stack Hub 1808 更新 (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> 在部署最新版本的 MySQL 资源提供程序之前，请先将支持的最低 Azure Stack Hub 更新版应用到 Azure Stack Hub 集成系统，或部署最新的 Azure Stack 开发工具包 (ASDK)。

## <a name="new-features-and-fixes"></a>新功能和修复
此 Azure Stack Hub MySQL 资源提供程序版本包含以下改进和修复：

- **为 MySQL 资源提供程序部署启用遥测**。 为 MySQL 资源提供程序部署启用了遥测数据收集功能。 收集的遥测数据包括资源提供程序部署、开始和停止时间、退出状态、退出消息和错误详细信息（如果适用）。

- **TLS 1.2 加密更新**。 为资源提供程序启用了仅限 TLS 1.2 的支持，使其能够与内部 Azure Stack Hub 组件通信。 

### <a name="fixes"></a>修复项

- **MySQL 资源提供程序 Azure Stack Hub PowerShell 兼容性**。 MySQL 资源提供程序经过更新，可使用 Azure Stack Hub 2018-03-01-hybrid PowerShell 配置文件，并可与 AzureRM 1.3.0 和更高版本兼容。

- **MySQL 登录 - 更改密码边栏选项卡**。 修复了无法在“更改密码”边栏选项卡上更改密码的问题。 删除了密码更改通知中的链接。

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
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>云管理员操作 Azure Stack Hub 时的已知问题
请参阅 [Azure Stack Hub 发行说明](azure-stack-servicing-policy.md)中的文档。

## <a name="next-steps"></a>后续步骤
[详细了解 MySQL 资源提供程序](azure-stack-mysql-resource-provider.md)。

[准备部署 MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md#prerequisites)。

[从旧版升级 MySQL 资源提供程序](azure-stack-mysql-resource-provider-update.md)。 