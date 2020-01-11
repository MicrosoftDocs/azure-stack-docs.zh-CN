---
title: Azure Stack 集线器上的应用服务 update 7 发行说明 |Microsoft Docs
description: 了解 Azure Stack 集线器上的应用服务的更新7中的内容、已知问题，以及下载更新的位置。
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
ms.date: 10/11/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 87a2fae4c2020b9c37a2907b42299871b0c85095
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75880254"
---
# <a name="app-service-on-azure-stack-hub-update-7-release-notes"></a>Azure Stack 集线器上的应用服务更新7发行说明

这些发行说明介绍 Azure Stack 中心更新7和任何已知问题的 Azure App Service 中的改进和修复。 已知问题分为直接与部署、更新过程和生成（安装后）相关的问题。

> [!IMPORTANT]
> 在部署 Azure App Service 1.7 之前，将1907更新应用到 Azure Stack 集线器集成系统或部署最新的 Azure Stack 开发工具包。


## <a name="build-reference"></a>内部版本参考

Azure Stack 集线器上的应用服务 Update 7 内部版本号为**84.0.2.10**

### <a name="prerequisites"></a>必备组件

开始部署之前[，请参阅入门文档之前](azure-stack-app-service-before-you-get-started.md)的。

开始将 Azure Stack 中心的 Azure App Service 升级到1.7 之前：

- 确保所有角色都已准备就绪，可在 Azure Stack 中心管理门户中 Azure App Service 管理

- 备份应用服务和 Master 数据库：
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- 备份租户应用内容文件共享

- 将**自定义脚本扩展**版本**1.9.3**与 Marketplace 联合

### <a name="new-features-and-fixes"></a>新增功能和修补程序

Azure Stack 中心更新7上的 Azure App Service 包括以下改进和修补程序：

- [CVE-2019-1372](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1372)远程代码执行漏洞的解决方法

- **应用服务租户、管理、功能门户和 Kudu 工具**的更新。 与 Azure Stack 中心门户 SDK 版本一致。

- 将**Azure Functions 运行时**更新为**1.0.12582**。

- 核心服务的更新，可提高可靠性和错误消息，从而更容易诊断常见问题。

- **以下应用程序框架和工具的更新**：
  - ASP.NET Core 2.2.46
  - Zul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 5.6.40
  - PHP 7.3。6
  - 已将 Kudu 更新为82.10503.3890

- **所有角色的基础操作系统更新**：
  - [2019-08 基于 x64 的系统的 Windows Server 2016 累积更新（KB4512495）](https://support.microsoft.com/help/4512495)

- **现在用户门户中启用了访问限制**：
  - 在此版本中，用户可以根据发布的[Azure App Service 访问限制](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)文档，为其 Web/Api/函数应用程序配置访问限制，**注意**： Azure Stack 中心的 Azure App Service 不支持服务终结点。

- **部署选项（经典）功能已还原**：
  - 用户可以再次使用部署选项（经典）从 GitHub、Bitbucket、Dropbox、OneDrive、本地和外部存储库中配置其应用的部署，并为应用程序设置部署凭据。

- 已正确配置**Azure 函数监视**。

- **Windows 更新行为**：根据客户反馈，我们已更改了更新7中应用服务角色的 Windows 更新配置方式：
  - 三种模式：
    - **禁用-Windows 更新**服务已禁用，将用 Azure Stack Hub 版本上的 Azure App Service 附带的 KB 更新 Windows;
    - 启用**自动**Windows 更新服务，Windows 更新将确定更新的方式和时间;
    - 已禁用**托管**Windows 更新服务，Azure App Service 会在每个角色的 OnStart 期间执行 Windows 更新循环。

  **新**部署-默认情况下禁用 Windows 更新服务。

  **现有**部署-如果已修改控制器上的设置，则值现在将从**False**更改为 "**已禁用**"，并且以前值 " **true** " 将变为**自动**

### <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]
> 如果已向应用服务资源提供程序提供 SQL Always On 实例，则必须[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)，并同步数据库，以防止在发生数据库故障转移时任何服务中断。

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 在现有虚拟网络中部署应用服务时，辅助角色无法访问文件服务器，并且文件服务器仅在专用网络上可用，如 Azure Stack 中心部署文档的 Azure App Service 中所述。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以启用辅助网和文件服务器之间的 SMB 流量。 在管理门户中转到 WorkersNsg，并使用以下属性添加出站安全规则：
 * 源：任意
 * 源端口范围： *
 * 目标： IP 地址
 * 目标 IP 地址范围：文件服务器的 Ip 范围
 * 目标端口范围：445
 * 协议：TCP
 * 操作：允许
 * 优先级：700
 * 名称： Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Azure Stack 中心的云管理员操作 Azure App Service 的已知问题

请参阅[Azure Stack 中心1907发行说明](azure-stack-release-notes-1907.md)中的文档

## <a name="next-steps"></a>后续步骤

- 有关 Azure App Service 的概述，请参阅[Azure Stack 集线器概述](azure-stack-app-service-overview.md)中的 Azure App Service。
- 有关如何准备在 Azure Stack 集线器上部署应用服务的详细信息，请参阅在[Azure Stack 中心开始应用服务之前](azure-stack-app-service-before-you-get-started.md)。
