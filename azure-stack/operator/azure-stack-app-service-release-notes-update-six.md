---
title: Azure Stack 上的应用服务 Update 6 发行说明 | Microsoft Docs
description: 了解基于 Azure Stack 的应用服务 Update 6 的功能、已知问题和更新下载位置。
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
ms.date: 06/24/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 93ec7faec82fad101c7bab2d2cec2783c8a060da
ms.sourcegitcommit: c46d913ebfa4cb6c775c5117ac5c9e87d032a271
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101019"
---
# <a name="app-service-on-azure-stack-update-6-release-notes"></a>基于 Azure Stack 的应用服务 Update 6 发行说明

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

本发行说明介绍 Azure Stack 上的 Azure 应用服务 Update 6 中的改进和修复，以及任何已知问题。 已知问题分为与部署、更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]
> 请将 1904 更新应用于 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包，然后部署 Azure 应用服务 1.6。


## <a name="build-reference"></a>内部版本参考

Azure Stack 上的应用服务 Update 6 的内部版本号为 **82.0.1.50**

### <a name="prerequisites"></a>先决条件

在开始部署之前，请参阅[准备工作文档](azure-stack-app-service-before-you-get-started.md)。

开始将 Azure Stack 上的 Azure 应用服务升级到 1.6 之前：

- 确保所有角色在 Azure Stack 管理门户的 Azure应用服务管理中处于“就绪”状态

- 备份应用服务和 Master 数据库：
  - AppService_Hosting；
  - AppService_Metering；
  - Master

- 备份租户应用内容文件共享

- 同步发布市场的**自定义脚本扩展**版本 **1.9.1**

### <a name="new-features-and-fixes"></a>新功能和修复

Azure Stack 上的 Azure 应用服务 Update 6 包含以下改进和修复：

- 针对**应用服务租户、管理员、函数门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 将 **Azure Functions 运行时**更新到 **v1.0.12299**。

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
  - 已将 Kudu 更新到 81.10329.3844

- **对所有角色的基础操作系统的更新**：
  - [适用于 x64 系统的 Windows Server 2016 的 2019-04 累积更新 (KB4493473)](https://support.microsoft.com/help/4493473/windows-10-update-kb4493473)

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

请参阅[Azure Stack 1908 发行说明](/azure-stack/operator/release-notes?view=azs-1908)中的文档

### <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack"></a>将应用程序部署到基于 Azure Stack 的 Azure 应用服务的租户的已知问题

- 部署中心灰显

租户尚不能使用部署中心，该中心是在 2018 年年底的公有云中发布的一项功能。  租户仍可通过门户、CLI 和 PowerShell 使用标准部署方法（FTP、Web 部署、Git 等）。

- 部署选项（经典）UX 和部署凭据门户选项不可用

若要访问 Azure Stack 部署中的部署选项和部署凭据用户体验，租户应使用 https://portal.&lt ;*region*&gt;.&lt;*FQDN*&gt; /?websitesExtension_oldvsts=true 这种 URL 格式（对于 ASDK，该格式为 [https://portal.local.azurestack.external/?websitesExtension_oldvsts=true](https://portal.local.azurestack.external/?websitesExtension_oldvsts=true)）来访问门户，然后以常规方式导航到其应用程序。

- Azure Function 监视在门户中持续显示“正在加载”

尝试在租户门户中监视单个 Functions 时，将看不到调用日志、成功计数或错误计数。  若要重新启用此功能，请依次转到“Function App”、“平台功能”、“应用程序设置”。  添加新的名为 **AzureWebJobsDashboard** 的应用设置，将其值设置为与 AzureWebJobsStorage 中设置的值相同。  然后转到函数的“监视器”视图，此时会看到监视信息。

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 若要详细了解如何完成基于 Azure Stack 的应用服务的部署准备，请参阅[基于 Azure Stack 的应用服务的准备工作](azure-stack-app-service-before-you-get-started.md)。
