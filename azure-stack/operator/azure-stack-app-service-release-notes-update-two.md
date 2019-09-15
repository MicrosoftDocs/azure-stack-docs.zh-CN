---
title: Azure Stack 上的应用服务 Update 2 发行说明 |Microsoft Docs
description: 了解 Azure Stack 上应用服务的更新2中的改进、修复和已知问题。
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/18/2018
ms.openlocfilehash: f427a31001f8f486fd231af7e59ef2bb30592661
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974811"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>Azure Stack 上的应用服务 Update 2 发行说明

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

这些发行说明介绍 Azure Stack Update 2 Azure App Service 上的改进、修复和已知问题。 已知问题分为三部分：直接与部署相关的问题、更新过程的问题以及生成（安装后）的问题。

> [!IMPORTANT]
> 在部署 Azure App Service 1.2 之前，将1804更新应用到 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包（ASDK）。

## <a name="build-reference"></a>内部版本参考

Azure Stack Update 2 内部版本号的应用服务为**72.0.13698.10**。

### <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> Azure Stack 上的 Azure App Service 的新部署现在需要[三个使用者通配符证书](azure-stack-app-service-before-you-get-started.md#get-certificates)，因为在 Azure App Service 中处理 KUDU 的 SSO。 新的主题是：  **\*appservice。\<区域\>。\<domainname\>。\<拓\>**

开始部署之前，请参阅[Azure Stack 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。

### <a name="new-features-and-fixes"></a>新功能和修复

基于 Azure Stack 的 Azure 应用服务 Update 2 包含以下改进和修复：

- 针对**应用服务租户、管理员、函数门户和 Kudu 工具**的更新。 与 Azure Stack 门户 SDK 版本一致。

- 将 **Azure Functions 运行时**更新到 **v1.0.11612**。

- 针对核心服务的更新，用于提高可靠性和错误消息传递，以便更轻松地诊断常见问题。

- **针对以下应用程序框架和工具的更新**：
  - 增加了 .NET Framework 4.7.1
  - 增加了 **Node.JS** 版本：
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - 增加了 **NPM** 版本：
    - 5.6.0
  - 更新了 .NET Core 组件，使其与公有云中的 Azure 应用服务保持一致。
  - 更新了 Kudu

- 已启用自动交换部署槽功能-[配置自动交换](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap)。

- 已启用生产功能的测试-[在生产中进行测试的简介](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)。

- Azure Functions 代理启用-[使用 Azure Functions 代理](https://docs.microsoft.com/azure/azure-functions/functions-proxies)。

- 已为添加应用服务管理扩展 UX UX：
  - 机密轮换
  - 证书轮换
  - 系统凭据轮换
  - 连接字符串轮换

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 当应用服务部署在现有虚拟网络中并且文件服务器仅在专用网络上可用时，工作人员将无法访问文件服务器。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加一个出站安全规则，该规则启用辅助子网和文件服务器之间的 SMB 流量。 在管理员门户中转到 WorkersNsg，并使用以下属性添加出站安全规则：

* 源：任意
* 源端口范围：*
* 目标：IP 地址
* 目标 IP 地址范围：文件服务器的 IP 范围
* 目标端口范围：445
* 协议：TCP
* 操作：Allow
* 优先级：700
* 姓名：Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure Stack 上的云管理员操作 Azure App Service 的已知问题

请参阅 [Azure Stack 1804 发行说明](azure-stack-update-1903.md)中的文档

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 有关如何准备在 Azure Stack 上部署应用服务的详细信息，请参阅[在 Azure Stack 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。
