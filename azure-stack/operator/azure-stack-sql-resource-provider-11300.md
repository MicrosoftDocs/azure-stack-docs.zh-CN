---
title: Azure Stack 中心 SQL 资源提供程序1.1.30.0 发行说明
titleSuffix: Azure Stack Hub
description: 查看 Azure Stack 中心 SQL 资源提供程序1.1.30.0 更新的发行说明。
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 07abcb71715c3078887e00c89f5eac53f5f98def
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882865"
---
# <a name="sql-resource-provider-11300-release-notes"></a>SQL 资源提供程序 1.1.30.0 发行说明

这些发行说明介绍了 SQL 资源提供程序版本1.1.30.0 中的改进和已知问题。

## <a name="build-reference"></a>内部版本参考
下载 SQL 资源提供程序二进制文件，然后运行自解压缩程序，将内容提取到临时目录中。 资源提供程序至少有一个对应的 Azure Stack 中心生成。 安装此版本的 SQL 资源提供程序所需的最低 Azure Stack 中心版本如下所示：

> |最低 Azure Stack 中心版本|SQL 资源提供程序版本|
> |-----|-----|
> |版本1808（1.1808.0.97）|[1.1.30.0](https://aka.ms/azurestacksqlrp11300)|
> |     |     |

> [!IMPORTANT]
> 部署最新版本的 SQL 资源提供程序之前，请将支持的最低 Azure Stack 中心更新应用到 Azure Stack 中心集成系统。

## <a name="new-features-and-fixes"></a>新增功能和修补程序
此版本的 Azure Stack 中心 SQL 资源提供程序包括以下改进和修补程序：

- **已为 SQL 资源提供程序部署启用遥测**。 已为 SQL 资源提供程序部署启用了遥测收集。 收集的遥测包括资源提供程序部署、开始和停止时间、退出状态、退出消息和错误详细信息（如果适用）。

- **TLS 1.2 加密更新**。 启用了 TLS 1.2-仅支持与内部 Azure Stack 集线器组件进行资源提供程序通信。 

### <a name="fixes"></a>修复项

- **SQL 资源提供程序 Azure Stack 集线器 PowerShell 兼容性**。 SQL 资源提供程序已更新为可与 Azure Stack 中心2018-03-01 混合 PowerShell 配置文件一起使用，并提供与 AzureRM 1.3.0 和更高版本的兼容性。

- **SQL 登录更改密码边栏选项卡**。 修复了 "更改密码" 边栏选项卡上无法更改密码的问题。 删除了来自密码更改通知的链接。

- **SQL 宿主服务器设置边栏选项卡更新**。 修复了 "设置" 边栏选项卡错误地将错误标题为 "Password" 的问题。

## <a name="known-issues"></a>已知问题

- **SQL sku 最多可能需要一小时才能在门户中显示**。 创建新的 SQL 数据库时，最多可能需要一小时才能看到新创建的 Sku。

    **解决方法**：无。

- 重新**使用 SQL 登录名**。 尝试使用同一订阅下的现有登录名创建新的 SQL 登录名，将导致重复使用相同的登录名和现有密码。

    **解决方法**：在同一订阅下创建新登录名时使用不同的用户名，或在不同的订阅下使用相同的用户名创建登录名。

- **共享 SQL 登录名将导致数据不一致**。 如果在同一订阅下为多个 SQL 数据库共享 SQL 登录名，则更改登录密码将导致数据不一致。

    **解决方法**：始终对同一订阅下的不同数据库使用不同的登录名。

- **TLS 1.2 支持要求**。 如果尝试从不启用 TLS 1.2 的计算机部署或更新 SQL 资源提供程序，则操作可能会失败。 在用于部署或更新资源提供程序的计算机上运行以下 PowerShell 命令，以验证是否按支持返回了 TLS 1.2：

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  如果命令的输出中不包括**Tls12** ，则不会在计算机上启用 TLS 1.2。

    **解决方法**：运行以下 PowerShell 命令以启用 TLS 1.2，然后启动资源提供程序部署或从同一 PowerShell 会话更新脚本：

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```

- **SQL 资源提供程序无法将 SQL Server 添加 Always On 侦听器**。 使用 SQL Server Always On 侦听器的侦听器 IP 地址时，SQL 资源提供程序 VM 将无法解析侦听器的主机名。

    **解决方法**：确保 DNS 能够正确地将侦听器 IP 解析为侦听器主机名。

### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Azure Stack 中心的云管理员操作的已知问题
请参阅[Azure Stack 中心发行说明](azure-stack-servicing-policy.md)中的文档。

## <a name="next-steps"></a>后续步骤
[了解有关 SQL 资源提供程序的详细信息](azure-stack-sql-resource-provider.md)。

[准备部署 SQL 资源提供程序](azure-stack-sql-resource-provider-deploy.md#prerequisites)。

[从以前的版本升级 SQL 资源提供程序](azure-stack-sql-resource-provider-update.md)。