---
title: Azure Stack 集线器上的应用服务更新6发行说明
description: 了解 Azure Stack 集线器上的应用服务的更新6中的内容、已知问题，以及下载更新的位置。
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 06/24/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 418e27424f91d4c676565acc27bcde77962d76ec
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875889"
---
# <a name="app-service-on-azure-stack-hub-update-6-release-notes"></a>Azure Stack 集线器上的应用服务更新6发行说明

这些发行说明介绍 Azure Stack 中心更新6和任何已知问题的 Azure App Service 改进和修复。 已知问题分为直接与部署、更新过程和生成（安装后）相关的问题。

> [!IMPORTANT]
> 在部署 Azure App Service 1.6 之前，将1904更新应用到 Azure Stack 集线器集成系统或部署最新的 Azure Stack 开发工具包。


## <a name="build-reference"></a>内部版本参考

Azure Stack 集线器上的应用服务 Update 6 内部版本号为**82.0.1.50**

### <a name="prerequisites"></a>必备组件

开始部署之前[，请参阅入门文档之前](azure-stack-app-service-before-you-get-started.md)的。

开始将 Azure Stack 中心的 Azure App Service 升级到1.6 之前：

- 确保所有角色都已准备就绪，可在 Azure Stack 中心管理门户中 Azure App Service 管理

- 备份应用服务和 Master 数据库：
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- 备份租户应用内容文件共享

- 将**自定义脚本扩展**版本**1.9.1**与 Marketplace 联合

### <a name="new-features-and-fixes"></a>新增功能和修补程序

Azure Stack 中心更新 6 Azure App Service 包括以下改进和修补程序：

- **应用服务租户、管理、功能门户和 Kudu 工具**的更新。 与 Azure Stack 中心门户 SDK 版本一致。

- 将**Azure Functions 运行时**更新为**1.0.12299**。

- 核心服务的更新，可提高可靠性和错误消息，从而更容易诊断常见问题。

- **以下应用程序框架和工具的更新**：
  - ASP.NET Core 2.2。4
  - NodeJS 10.15.2
  - 祖鲁 OpenJDK 8.36.0。1
  - Tomcat 7.0.81
  - Tomcat 8.5.37
  - Tomcat 9.0.14
  - PHP 5.6.39
  - PHP 7.0.33
  - PHP 7.1.25
  - PHP 7.2.13
  - 已将 Kudu 更新为81.10329.3844

- **所有角色的基础操作系统更新**：
  - [2019-04 基于 x64 的系统的 Windows Server 2016 累积更新（KB4493473）](https://support.microsoft.com/help/4493473/windows-10-update-kb4493473)

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
 * 协议： TCP
 * 操作：允许
 * 优先级：700
 * 名称： Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Azure Stack 中心的云管理员操作 Azure App Service 的已知问题

请参阅[Azure Stack 中心1908发行说明](/azure-stack/operator/release-notes?view=azs-1908)中的文档

### <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack-hub"></a>Azure Stack 中心 Azure App Service 上部署应用程序的租户的已知问题

- 部署中心灰显

租户仍不能使用部署中心，这是在2018年年底发布的一项功能。  租户仍可通过门户、CLI 和 PowerShell 使用标准部署方法（FTP、Web 部署、Git 等）。

- 部署选项（经典） UX 和部署凭据门户选项不可用

为了达到部署选项和部署凭据 Azure Stack 中心部署中的用户体验，租户应使用此 URL 格式访问门户- https://portal.&lt ;*区域*&gt; 。&lt;*FQDN*&gt; /？ websitesExtension_oldvsts = true-对于 ASDK 将[https://portal.local.azurestack.external/?websitesExtension_oldvsts=true](https://portal.local.azurestack.external/?websitesExtension_oldvsts=true) ，然后以正常方式导航到其应用程序。

- Azure Function Monitoring 持续显示门户中的 "正在加载"

尝试监视单个函数时，用户门户中将不会显示任何调用日志、成功计数或错误计数。  若要重新启用此功能，请跳到**Function App**，中转到 "**平台功能**"，然后参阅 "**应用程序设置**"。  添加新的应用设置-名称**AzureWebJobsDashboard** ，并将值设置为在 AzureWebJobsStorage 中设置的相同值。  然后，在该函数的 "监视" 视图中，会看到监视信息。

## <a name="next-steps"></a>后续步骤

- 有关 Azure App Service 的概述，请参阅[Azure Stack 集线器概述](azure-stack-app-service-overview.md)中的 Azure App Service。
- 有关如何准备在 Azure Stack 集线器上部署应用服务的详细信息，请参阅在[Azure Stack 中心开始应用服务之前](azure-stack-app-service-before-you-get-started.md)。
