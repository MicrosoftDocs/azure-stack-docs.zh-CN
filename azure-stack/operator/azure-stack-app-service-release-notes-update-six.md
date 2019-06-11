---
title: Azure Stack 上的应用服务 update 6 发行说明 |Microsoft Docs
description: 了解有关新增功能更新中有六个 Azure Stack 上的应用服务、 已知的问题和下载更新的位置。
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
ms.date: 06/10/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: d3464681463cfb66a368210beed79d5ef4c28739
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828312"
---
# <a name="app-service-on-azure-stack-update-6-release-notes"></a>应用服务的 Azure Stack 更新 6 发行说明

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

这些发行说明介绍了改进和修补程序在 Azure 应用服务中的 Azure Stack 更新 6 和任何已知的问题。 已知问题分为与部署、更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]
> 适用于 Azure Stack 集成系统的 1904年更新或部署 Azure 应用服务 1.6 之前部署最新的 Azure Stack 开发工具包。


## <a name="build-reference"></a>内部版本参考

Azure Stack 更新 6 生成号上的应用服务是**82.0.1.50**

### <a name="prerequisites"></a>必备组件

在开始部署之前，请参阅[准备工作文档](azure-stack-app-service-before-you-get-started.md)。

将 Azure Stack 上的 Azure 应用服务升级到 1.6 开始之前：

- 确保所有角色在 Azure Stack 管理门户的 Azure应用服务管理中处于“就绪”状态

- 备份应用服务和 Master 数据库：
  - AppService_Hosting；
  - AppService_Metering；
  - Master

- 备份租户应用内容文件共享

- 同步发布市场的**自定义脚本扩展**版本 **1.9.1**

### <a name="new-features-and-fixes"></a>新功能和修复

Azure Stack 更新 6 上的 azure 应用服务包括以下改进和修补程序：

- 针对**应用服务租户、管理员、函数门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 更新**Azure Functions 运行时**到**v1.0.12299**。

- 针对核心服务的更新，用于提高可靠性和错误消息传递，以便更轻松地诊断常见问题。

- **针对以下应用程序框架和工具的更新**：
  - ASP.NET Core 2.2.4
  - NodeJS 10.15.2
  - Zulu OpenJDK 8.36.0.1
  - Tomcat 7.0.81
  - Tomcat 8.5.37
  - Tomcat 9.0.14
  - PHP 5.6.39
  - PHP 7.0.33
  - PHP 7.1.25
  - PHP 7.2.13
  - 已更新到 81.10329.3844 Kudu

- **对所有角色的基础操作系统的更新**：
  - [2019-04 适用于 Windows Server 2016 的基于 x64 的系统 (KB4493473) 的累积更新](https://support.microsoft.com/help/4493473/windows-10-update-kb4493473)

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
 * 操作：允许
 * 优先级：700
 * 姓名：Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>云管理员在操作基于 Azure Stack 的 Azure 应用服务时的已知问题

中的文档，请参阅[Azure Stack 1904 发行说明](azure-stack-release-notes-1904.md)

### <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack"></a>租户部署 Azure Stack 上的 Azure 应用服务上的应用程序的已知的问题

- 部署中心灰显

租户尚不能进行的部署中心，这是一项功能，在 2018 年底发布在公有云中使用。  租户仍可使用通过门户、 CLI 和 PowerShell 的标准部署方法 （FTP、 Web 部署、 Git 等）。

- 部署选项 （经典） 的用户体验和部署凭据门户选项不可用

要达到的部署选项和部署凭据的用户体验在 Azure Stack 部署中，租户应访问门户使用此 URL 格式- https://portal.&lt;*区域*&gt;。&lt;*FQDN*&gt;/？ websitesExtension_oldvsts = true-这是对于 asdk 来说[ https://portal.local.azurestack.external/?websitesExtension_oldvsts=true ](https://portal.local.azurestack.external/?websitesExtension_oldvsts=true) ，然后正常情况下导航到其应用程序。

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 若要详细了解如何完成基于 Azure Stack 的应用服务的部署准备，请参阅[基于 Azure Stack 的应用服务的准备工作](azure-stack-app-service-before-you-get-started.md)。
