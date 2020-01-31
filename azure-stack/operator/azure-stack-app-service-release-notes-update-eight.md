---
title: Azure Stack 集线器上的应用服务 update 8 发行说明
description: 了解 Azure Stack 集线器上的应用服务的 update 8 中的内容、已知问题，以及下载更新的位置。
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 01/13/2020
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 639c9267a9d42b20a15bc30ab6b72706816bf7ee
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76874478"
---
# <a name="app-service-on-azure-stack-hub-update-8-release-notes"></a>Azure Stack 集线器上的应用服务 update 8 发行说明

这些发行说明介绍 Azure Stack 中心更新8和任何已知问题的 Azure App Service 改进和修复。 已知问题分为直接与部署、更新过程和生成（安装后）相关的问题。

> [!IMPORTANT]
> 在部署 Azure App Service 1.8 之前，将1910更新应用到 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包。


## <a name="build-reference"></a>内部版本参考

Azure Stack 集线器上的应用服务 Update 8 内部版本号为**86.0.2.13**

### <a name="prerequisites"></a>必备组件

开始部署之前[，请参阅入门文档之前](azure-stack-app-service-before-you-get-started.md)的。

开始在 Azure Stack 到1.8 的 Azure App Service 升级之前：

- 确保在 Azure Stack 管理门户的 "Azure App Service 管理" 中，所有角色都已准备就绪

- 备份应用服务和 Master 数据库：
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- 备份租户应用内容文件共享

- 将**自定义脚本扩展**版本**1.9.3**与 Marketplace 联合

### <a name="new-features-and-fixes"></a>新增功能和修补程序

Azure Stack Update 8 上的 Azure App Service 包括以下改进和修补程序：

- **应用服务租户、管理、功能门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 将**Azure Functions 运行时**更新为**1.0.12615**。

- 核心服务的更新，可提高可靠性和错误消息，从而更容易诊断常见问题。

- **以下应用程序框架和工具的更新**：
  - ASP.NET Core 3.1。0
  - ASP.NET Core 3.0。1
  - ASP.NET Core 2.2。8
  - ASP.NET Core 模块 v2 13.1.19331。0
  - Azul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 7.1.32
  - PHP 7.2.22
  - PHP 7.3。9
  - 已将 Kudu 更新为85.11024.4154
  - Msdeploy.exe 3.5.80916.15
  - NodeJS 10.16。3
  - NPM 6.9。0
  - 适用于 Windows 2.19.1.0 的 Git

- **所有角色的基础操作系统更新**：
  - [2019-12 基于 x64 的系统的 Windows Server 2016 累积更新（KB4530689）](https://support.microsoft.com/help/4530689)

- **托管磁盘对新部署的支持**

Azure Stack 中心的 Azure App Service 的所有新部署都将对所有虚拟机和虚拟机规模集使用托管磁盘。  所有现有部署将继续使用非托管磁盘。

- **前端负载平衡器强制实施的 TLS 1。2**

在此更新中，将为所有应用程序强制实施**TLS 1.2** 。

### <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]
> 如果已向应用服务资源提供程序提供 SQL Always On 实例，则必须[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)，并同步数据库，以防止在发生数据库故障转移时任何服务中断。

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 在现有虚拟网络中部署应用服务时，辅助角色无法访问文件服务器，并且文件服务器仅在专用网络上可用，如 Azure Stack 部署文档中的 Azure App Service 中所述。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以启用辅助网和文件服务器之间的 SMB 流量。 在管理门户中转到 WorkersNsg，并使用以下属性添加出站安全规则：
 * 源：任意
 * 源端口范围： *
 * 目标： IP 地址
 * 目标 IP 地址范围：文件服务器的 Ip 范围
 * 目标端口范围：445
 * 协议： TCP
 * 操作：允许
 * 优先级：700
 * 名称： Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure Stack 上的云管理员操作 Azure App Service 的已知问题

请参阅[Azure Stack 1907 发行说明](azure-stack-release-notes-1907.md)中的文档

## <a name="next-steps"></a>后续步骤

- 有关 Azure App Service 的概述，请参阅[Azure Stack 概述 Azure App Service](azure-stack-app-service-overview.md)。
- 有关如何准备在 Azure Stack 上部署应用服务的详细信息，请参阅[Azure Stack 上的应用服务入门](azure-stack-app-service-before-you-get-started.md)。
