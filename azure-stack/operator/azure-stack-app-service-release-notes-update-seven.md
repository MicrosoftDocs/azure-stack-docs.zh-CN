---
title: Azure Stack Hub 上的应用服务 Update 7 发行说明
description: 更新 7 Azure Stack 集线器上应用服务的发行说明，包括新功能、修复和已知问题。
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 10/11/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 8faeca7cf964ce459ebc10cc047c7b9475deeec4
ms.sourcegitcommit: 3fd4a38dc8446e0cdb97d51a0abce96280e2f7b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580075"
---
# <a name="app-service-on-azure-stack-hub-update-7-release-notes"></a>Azure Stack Hub 上的应用服务 Update 7 发行说明

这些发行说明介绍 Azure Stack 中心更新 7 Azure App Service 的新功能、修复和已知问题。 已知问题分为两部分：与升级过程相关的问题和生成（安装后）问题。

> [!IMPORTANT]
> 在部署 Azure App Service 1.7 之前，将1910更新应用到 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包（ASDK）。

## <a name="build-reference"></a>内部版本参考

Azure Stack 集线器 Update 7 内部版本号上的应用服务为**84.0.2.10**。

### <a name="prerequisites"></a>先决条件

开始部署之前，请参阅[在 Azure Stack 集线器上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。

开始将 Azure Stack Hub 上的 Azure 应用服务升级到 1.7 之前：

- 确保所有角色都已准备好在 Azure Stack 中心管理员门户中 Azure App Service 管理。

- 备份应用服务和 master 数据库：
  - AppService_Hosting；
  - AppService_Metering；
  - Master

- 备份租户应用内容文件共享。

- 将**自定义脚本扩展**版本**1.9.3**与 Azure Stack 中心市场联合。

### <a name="new-features-and-fixes"></a>新功能和修复

Azure Stack Hub 上的 Azure 应用服务 Update 7 包含以下改进和修复：

- [CVE-2019-1372](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1372)远程代码执行漏洞的解决方法。

- **应用服务租户、管理员、功能门户和 Kudu 工具**的更新。 与 Azure Stack Hub 门户 SDK 版本一致。

- 将 **Azure Functions 运行时**更新到 **v1.0.12582**。

- 针对核心服务的更新，用于提高可靠性和错误消息传递，以便更轻松地诊断常见问题。

- **针对以下应用框架和工具的更新**：

  - ASP.NET Core 2.2.46
  - Zul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 5.6.40
  - PHP 7.3.6
  - 已将 Kudu 更新到 82.10503.3890

- **对所有角色的基础操作系统的更新**：
  - [适用于 x64 系统的 Windows Server 2016 的 2019-08 累积更新 (KB4512495)](https://support.microsoft.com/help/4512495)

- **现在用户门户中启用了访问限制**：
  - 用户现在可以根据发布的[Azure App Service 访问限制](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)文档，为其 WEB/API/函数应用配置访问限制。
  
  > [!NOTE]
  > Azure Stack 中心上的 Azure App Service 不支持服务终结点。

- **部署选项（经典）功能已还原**：
  - 用户可以再次使用部署选项（经典）从 GitHub、Bitbucket、Dropbox、OneDrive、本地和外部存储库中配置其应用的部署，并为应用设置部署凭据。

- 已正确配置**Azure 函数监视**。

- **Windows 更新行为**：根据客户反馈，我们更改了从 update 7 中的应用服务角色配置 Windows 更新方式：
  - 三种模式：
    - **禁用-Windows 更新**服务已禁用，Windows 将更新为 Azure App Service Azure Stack 集线器版本附带的 KB;
    - 启用**自动**Windows 更新服务，Windows 更新确定更新的方式和时间;
    - 已禁用**托管**Windows 更新服务，Azure App Service 会在每个角色的 OnStart 期间执行 Windows 更新循环。

  **新**部署 - Windows 更新服务默认禁用。

  **现有**部署-如果你已修改控制器上的设置，则值将从**False**更改为 "**已禁用**"，并且以前值 " **True** " 将变为 "**自动**"。

### <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]
> 如果已向应用服务资源提供程序提供 SQL Always On 实例，则必须[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)，并同步数据库，以防止在发生数据库故障转移时任何服务中断。

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 在现有虚拟网络中部署应用服务时，辅助角色无法访问文件服务器，并且文件服务器仅在专用网络上可用，如 Azure Stack 中心部署文档的 Azure App Service 中所述。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。 转到管理员门户中的 WorkersNsg 并添加包含以下属性的出站安全规则：

* 源：任何
* 源端口范围：*
* 目标： IP 地址
* 目标 IP 地址范围：文件服务器的 IP 范围
* 目标端口范围：445
* 协议：TCP
* 操作：允许
* 优先级：700
* 名称：Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Azure Stack 中心的云管理员操作 Azure App Service 的已知问题

请参阅[Azure Stack 中心1907发行说明](azure-stack-release-notes-1907.md)中的文档

## <a name="next-steps"></a>后续步骤

- 有关 Azure App Service 的概述，请参阅[Azure Stack 集线器概述中的 Azure App Service 和 Azure Functions](azure-stack-app-service-overview.md)。
- 有关如何准备在 Azure Stack 集线器上部署应用服务的详细信息，请参阅[在 Azure Stack 中心部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。
