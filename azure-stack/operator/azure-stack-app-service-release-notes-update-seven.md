---
title: Azure Stack update 7 发行说明中的应用服务 |Microsoft Docs
description: 了解 Azure Stack 上的应用服务的更新7、已知问题和更新下载位置。
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: ec07277043068835d1d1d5a41285ee5df5ee7691
ms.sourcegitcommit: 701685f0b59e5a3d1a8d39fe477b8df701a51cd2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165336"
---
# <a name="app-service-on-azure-stack-update-7-release-notes"></a>Azure Stack 上的应用服务更新7发行说明

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

这些发行说明介绍 Azure Stack Update 7 和任何已知问题的 Azure App Service 改进和修复。 已知问题分为与部署、更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]
> 在部署 Azure App Service 1.7 之前, 将1907更新应用到 Azure Stack 集成系统, 或部署最新的 Azure Stack 开发工具包。


## <a name="build-reference"></a>内部版本参考

Azure Stack 上的应用服务 Update 7 内部版本号为**84.0.2.10**

### <a name="prerequisites"></a>先决条件

在开始部署之前，请参阅[准备工作文档](azure-stack-app-service-before-you-get-started.md)。

开始在 Azure Stack 到1.7 的 Azure App Service 升级之前:

- 确保所有角色在 Azure Stack 管理门户的 Azure应用服务管理中处于“就绪”状态

- 备份应用服务和 Master 数据库：
  - AppService_Hosting；
  - AppService_Metering；
  - Master

- 备份租户应用内容文件共享

- 将**自定义脚本扩展**版本**1.9.3**与 Marketplace 联合

### <a name="new-features-and-fixes"></a>新功能和修复

Azure Stack Update 7 上的 Azure App Service 包括以下改进和修补程序:

- 针对**应用服务租户、管理员、函数门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 将**Azure Functions 运行时**更新为**1.0.12582**。

- 针对核心服务的更新，用于提高可靠性和错误消息传递，以便更轻松地诊断常见问题。

- **针对以下应用程序框架和工具的更新**：
  - ASP.NET Core 2.2.46
  - Zul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 5.6.40
  - PHP 7.3。6
  - 已将 Kudu 更新为82.10503.3890

- **对所有角色的基础操作系统的更新**：
  - [2019-08 基于 x64 的系统的 Windows Server 2016 累积更新 (KB4512495)](https://support.microsoft.com/help/4512495)

- **现在用户门户中启用了访问限制**:
  - 在此版本中, 用户可以根据发布的[Azure App Service 访问限制](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)文档, 为其 Web/Api/函数应用程序配置访问限制:Azure Stack 上的 Azure App Service 不支持服务终结点。

- **部署选项 (经典) 功能已还原**:
  - 用户可以再次使用部署选项 (经典) 从 GitHub、Bitbucket、Dropbox、OneDrive、本地和外部存储库中配置其应用的部署, 并为应用程序设置部署凭据。

- 已正确配置**Azure 函数监视**。

- **Windows 更新行为**:根据客户反馈, 我们已更改了更新7中应用服务角色的 Windows 更新配置方式:
  - 三种模式:
    - 禁用-Windows 更新服务已禁用, 将用 Azure Stack 版本上的 Azure App Service 附带的 KB 更新 Windows;
    - 启用**自动**Windows 更新服务, Windows 更新将确定更新的方式和时间;
    - 已禁用**托管**Windows 更新服务, Azure App Service 会在每个角色的 OnStart 期间执行 Windows 更新循环。

  **新**部署-默认情况下禁用 Windows 更新服务。

  **现有**部署-如果已修改控制器上的设置, 则值现在将从**False**更改为 "**已禁用**", 并且以前值 " **true** " 将变为**自动**

### <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]
> 如果已经为应用服务资源提供程序提供 SQL Always On 实例，则必须[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)并同步数据库，以免在进行数据库故障转移时丢失服务。

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 如 Azure Stack 上的 Azure 应用服务部署文档中所述，当应用服务部署在现有虚拟网络中并且文件服务器仅在专用网络上可用时，工作人员将无法访问文件服务器。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。 转到管理门户中的 WorkersNsg 并添加具有以下属性的出站安全规则：
 * 源：任意
 * 源端口范围：*
 * 目标：IP 地址
 * 目标 IP 地址范围：文件服务器的 IP 范围
 * 目标端口范围：445
 * 协议：TCP
 * 操作：Allow
 * 优先级：700
 * 姓名：Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>云管理员在操作基于 Azure Stack 的 Azure 应用服务时的已知问题

请参阅[Azure Stack 1907 发行说明](azure-stack-release-notes-1907.md)中的文档

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 若要详细了解如何完成基于 Azure Stack 的应用服务的部署准备，请参阅[基于 Azure Stack 的应用服务的准备工作](azure-stack-app-service-before-you-get-started.md)。
