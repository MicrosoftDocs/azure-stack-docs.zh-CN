---
title: Azure Stack Hub 上的应用服务 Update 6 发行说明
description: 更新 6 Azure Stack 集线器上应用服务的发行说明，包括新功能、修复和已知问题。
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 06/24/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: cf6895cac1c0be2b55c99ff51ebccc0f46350437
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847718"
---
# <a name="app-service-on-azure-stack-hub-update-6-release-notes"></a>Azure Stack Hub 上的应用服务 Update 6 发行说明

这些发行说明介绍 Azure Stack 中心更新 6 Azure App Service 的新功能、修复和已知问题。 已知问题分为两部分：与升级过程相关的问题和生成（安装后）问题。

> [!IMPORTANT]
> 在部署 Azure App Service 1.6 之前，将1904更新应用到 Azure Stack 集线器集成系统或部署最新的 Azure Stack 开发工具包（ASDK）。

## <a name="build-reference"></a>内部版本参考

Azure Stack 中心更新6内部版本号上的应用服务是**82.0.1.50**。

## <a name="prerequisites"></a>必备条件

开始部署之前，请参阅[在 Azure Stack 集线器上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。

开始将 Azure Stack Hub 上的 Azure 应用服务升级到 1.6 之前：

- 确保所有角色都已准备好在 Azure Stack 中心管理员门户中 Azure App Service 管理。

- 在 Azure Stack 中心管理员门户中使用应用服务管理来备份应用服务机密

- 备份应用服务和 master 数据库：
  - AppService_Hosting；
  - AppService_Metering；
  - 主

- 备份租户应用内容文件共享。

  > [!Important]
  > 云操作员负责维护和操作文件服务器，并 SQL Server。  资源提供程序不管理这些资源。  云操作员负责备份应用服务数据库和租户内容文件共享。

- 将**自定义脚本扩展**版本**1.9.1**与 Azure Stack 中心市场联合。

## <a name="new-features-and-fixes"></a>新功能和修复

Azure Stack 中心更新 6 Azure App Service 包括以下改进和修补程序：

- **应用服务租户、管理、功能门户和 Kudu 工具**的更新。 与 Azure Stack 中心门户 SDK 版本一致。

- 将 **Azure Functions 运行时**更新到 **v1.0.12299**。

- 针对核心服务的更新，用于提高可靠性和错误消息传递，以便更轻松地诊断常见问题。

- **针对以下应用框架和工具的更新**：

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

## <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]
> 如果已向应用服务资源提供程序提供 SQL Always On 实例，则必须[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)，并同步数据库，以防止在发生数据库故障转移时任何服务中断。

## <a name="known-issues-post-installation"></a>已知问题（安装后）

- 在现有虚拟网络中部署应用服务时，辅助角色无法访问文件服务器，并且文件服务器仅在专用网络上可用，如 Azure Stack 中心部署文档的 Azure App Service 中所述。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。 转到管理员门户中的 WorkersNsg 并添加包含以下属性的出站安全规则：

* 源：任何
* 源端口范围：*
* 目标：IP 地址
* 目标 IP 地址范围：文件服务器的 IP 范围
* 目标端口范围：445
* 协议：TCP
* 操作：允许
* 优先级：700
* 名称：Outbound_Allow_SMB445

## <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Azure Stack 中心的云管理员操作 Azure App Service 的已知问题

请参阅[Azure Stack 中心1908发行说明](/azure-stack/operator/release-notes?view=azs-1908)中的文档。

## <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack-hub"></a>Azure Stack 中心 Azure App Service 上部署应用程序的租户的已知问题

- 部署中心灰显/不可用。

    租户不能使用部署中心，这是在2018年年底发布的一项功能。 租户仍可通过门户、CLI 和 PowerShell 使用标准部署方法（FTP、Web 部署、Git 等）。

- 部署选项（经典） UX 和部署凭据门户选项不可用。

    若要访问 Azure Stack 中心部署中的 "部署选项" 和 "部署凭据" 用户体验，租户应使用以下 URL 格式`https://portal.&lt;*region*&gt;.&lt;*FQDN*&gt;/?websitesExtension_oldvsts=true`访问门户：-对于 ASDK `https://portal.local.azurestack.external/?websitesExtension_oldvsts=true`，应使用此 URL 格式，然后导航到其应用。

- Azure function monitoring 持续显示门户中的 "正在加载"。

    尝试监视用户门户中的各个函数时，将看不到任何调用日志、成功计数或错误计数。 若要重新启用此功能，请依次转到“Function App”、“平台功能”、“应用程序设置”。************  添加一个名为**AzureWebJobsDashboard**的新应用设置，并将值设置为与 AzureWebJobsStorage 中设置的相同的值。 然后，在该函数的 "监视" 视图中，可以看到监视信息。

## <a name="next-steps"></a>后续步骤

- 有关 Azure App Service 的概述，请参阅[Azure Stack 集线器概述中的 Azure App Service 和 Azure Functions](azure-stack-app-service-overview.md)。
- 有关如何准备在 Azure Stack 上部署应用服务的详细信息，请参阅[在 Azure Stack 中心部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。