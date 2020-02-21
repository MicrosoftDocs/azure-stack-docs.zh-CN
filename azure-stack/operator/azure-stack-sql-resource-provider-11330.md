---
title: Azure Stack 中心 SQL 资源提供程序1.1.33.0 发行说明
titleSuffix: Azure Stack Hub
description: 查看 Azure Stack 中心 SQL 资源提供程序1.1.33.0 更新的发行说明。
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 208f0556b125eed30063dc768d9b88875176d344
ms.sourcegitcommit: b2173b4597057e67de1c9066d8ed550b9056a97b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77491860"
---
# <a name="sql-resource-provider-11330-release-notes"></a>SQL 资源提供程序1.1.33.0 发行说明

这些发行说明介绍了 SQL 资源提供程序版本1.1.33.0 中的改进和已知问题。

## <a name="build-reference"></a>内部版本参考
下载 SQL 资源提供程序二进制文件，然后运行自解压缩程序，将内容提取到临时目录中。 资源提供程序至少有一个对应的 Azure Stack 中心生成。 安装此版本的 SQL 资源提供程序所需的最低 Azure Stack 中心版本如下所示：

> |最低 Azure Stack 中心版本|SQL 资源提供程序版本|
> |-----|-----|
> |版本1808（1.1808.0.97）|[SQL RP 版本1.1.33。0](https://aka.ms/azurestacksqlrp11330)|  
> |     |     |

> [!IMPORTANT]
> 部署最新版本的 SQL 资源提供程序之前，请将支持的最低 Azure Stack 中心更新应用到 Azure Stack 中心集成系统。

## <a name="new-features-and-fixes"></a>新增功能和修补程序
此版本的 Azure Stack 中心 SQL 资源提供程序包括以下改进和修补程序：

### <a name="fixes"></a>修复项

- **SQL 资源提供程序门户扩展可能会选择错误的订阅**。 SQL 资源提供程序使用 Azure 资源管理器调用来确定要使用的第一个服务管理员订阅，这可能不是*默认的提供程序订阅*。 如果出现这种情况，SQL 资源提供程序将不能正常工作。

- **SQL 宿主服务器不列出托管的数据库。** 查看 SQL 宿主服务器的租户资源时，可能不会列出用户创建的数据库。

- **如果未启用 TLS 1.2，则以前的 SQL 资源提供程序（1.1.30.0）部署可能失败**。 更新了 SQL 资源提供程序1.1.33.0，以便在部署资源提供程序、更新资源提供程序或轮换机密时启用 TLS 1.2。

- **SQL 资源提供程序的机密轮换失败**。 修复了轮换机密时导致以下错误代码的问题： `New-AzureRmResourceGroupDeployment - Error: Code=InvalidDeploymentParameterValue; Message=The value of deployment parameter 'StorageAccountBlobUri' is null.`

## <a name="known-issues"></a>已知问题

- **SQL sku 最多可能需要一小时才能在门户中显示**。 创建新的 SQL 数据库时，最多可能需要一小时才能看到新创建的 Sku。

    **解决方法**：无。

- 重新**使用 SQL 登录名**。 尝试使用同一订阅下的现有登录名创建新的 SQL 登录名，将导致重复使用相同的登录名和现有密码。

    **解决方法**：在同一订阅下创建新登录名时使用不同的用户名，或在不同的订阅下使用相同的用户名创建登录名。

- **共享 SQL 登录名将导致数据不一致**。 如果在同一订阅下为多个 SQL 数据库共享 SQL 登录名，则更改登录密码将导致数据不一致。

    **解决方法**：始终对同一订阅下的不同数据库使用不同的登录名。

- **SQL 资源提供程序无法将 SQL Server 添加 Always On 侦听器**。 使用 SQL Server Always On 侦听器的侦听器 IP 地址时，SQL 资源提供程序 VM 将无法解析侦听器的主机名。

    **解决方法**：确保 DNS 能够正确地将侦听器 IP 解析为侦听器主机名。

### <a name="known-issues-for-cloud-admins-operating-azure-stack-hub"></a>Azure Stack 中心的云管理员操作的已知问题
请参阅[Azure Stack 中心发行说明](azure-stack-servicing-policy.md)中的文档。

## <a name="next-steps"></a>后续步骤
[了解有关 SQL 资源提供程序的详细信息](azure-stack-sql-resource-provider.md)。

[准备部署 SQL 资源提供程序](azure-stack-sql-resource-provider-deploy.md#prerequisites)。

[从以前的版本升级 SQL 资源提供程序](azure-stack-sql-resource-provider-update.md)。