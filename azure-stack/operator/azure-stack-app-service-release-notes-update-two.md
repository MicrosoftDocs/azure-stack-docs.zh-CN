---
title: Azure Stack 集线器上的应用服务 Update 2 发行说明
description: 了解 Azure Stack 集线器上的应用服务更新2中的改进、修复和已知问题。
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/18/2019
ms.openlocfilehash: 2c80aafe3665b6ecb31e701483a5e903d0471e42
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703411"
---
# <a name="app-service-on-azure-stack-hub-update-2-release-notes"></a>Azure Stack 集线器上的应用服务 Update 2 发行说明

这些发行说明介绍 Azure Stack 中心更新 2 Azure App Service 的改进、修复和已知问题。 已知问题分为三部分：直接与部署相关的问题、更新过程的问题以及生成（安装后）的问题。

> [!IMPORTANT]
> 在部署 Azure App Service 1.2 之前，将1804更新应用到 Azure Stack 集线器集成系统或部署最新的 Azure Stack 开发工具包（ASDK）。

## <a name="build-reference"></a>内部版本参考

Azure Stack 中心更新2内部版本号上的应用服务是**72.0.13698.10**。

### <a name="prerequisites"></a>必备条件

> [!IMPORTANT]
> Azure Stack 中心的 Azure App Service 的新部署现在需要[三个使用者的通配符证书](azure-stack-app-service-before-you-get-started.md#get-certificates)，因为在 Azure App Service 中处理 Kudu SSO 的方式。 新主题为： **\*\<区域\>\<domainname\>\<扩展\>**

开始部署之前，请参阅在[Azure Stack 中心部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。

### <a name="new-features-and-fixes"></a>新增功能和修补程序

Azure Stack 中心更新2上的 Azure App Service 包括以下改进和修补程序：

- **应用服务租户、管理、功能门户和 Kudu 工具**的更新。 与 Azure Stack 中心门户 SDK 版本一致。

- 将**Azure Functions 运行时**更新为**1.0.11612**。

- 核心服务的更新，可提高可靠性和错误消息，从而更容易诊断常见问题。

- **以下应用程序框架和工具的更新**：
  - 添加了 .NET Framework 4.7。1
  - 已添加**node.js**版本：
    - NodeJS 6.12。3
    - NodeJS 8.9。4
    - NodeJS 8.10。0
    - NodeJS 8.11。1
  - 添加了**NPM**版本：
    - 5.6.0
  - 更新的 .NET Core 组件与公有云中 Azure App Service 一致。
  - 已更新 Kudu

- 已启用自动交换部署槽功能-[配置自动交换](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap)。

- 已启用生产功能的测试-[在生产中进行测试的简介](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)。

- Azure Functions 代理启用-[使用 Azure Functions 代理](https://docs.microsoft.com/azure/azure-functions/functions-proxies)。

- 已为添加应用服务管理扩展 UX UX：
  - 机密旋转
  - 证书轮换
  - 系统凭据轮换
  - 连接字符串旋转

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 在现有虚拟网络中部署应用服务时，辅助角色无法访问文件服务器，并且文件服务器仅在专用网络上可用。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加一个出站安全规则，该规则启用辅助子网和文件服务器之间的 SMB 流量。 在管理员门户中转到 WorkersNsg，并使用以下属性添加出站安全规则：

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

请参阅[Azure Stack 中心1804发行说明](azure-stack-update-1903.md)中的文档

## <a name="next-steps"></a>后续步骤

- 有关 Azure App Service 的概述，请参阅[Azure Stack 集线器概述](azure-stack-app-service-overview.md)中的 Azure App Service。
- 有关如何准备在 Azure Stack 集线器上部署应用服务的详细信息，请参阅[在 Azure Stack 中心部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。
