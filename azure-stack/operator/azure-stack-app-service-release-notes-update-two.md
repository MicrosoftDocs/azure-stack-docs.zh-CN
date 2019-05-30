---
title: 基于 Azure Stack 的应用服务 Update 2 发行说明 | Microsoft Docs
description: 了解基于 Azure Stack 的应用服务 Update 2 的功能、已知问题和更新下载位置。
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
ms.openlocfilehash: b56c90d9bac8039d428b7ee06a384956924e94f5
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269053"
---
# <a name="app-service-on-azure-stack-update-2-release-notes"></a>基于 Azure Stack 的应用服务 Update 2 发行说明

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

这些发行说明介绍了基于 Azure Stack 的 Azure 应用服务 Update 2 中的改进和修复，以及任何已知问题。 已知问题分为与部署、更新过程直接相关的问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]
> 请将 1804 更新应用于 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包，然后部署 Azure 应用服务 1.2。
>
>

## <a name="build-reference"></a>内部版本参考

基于 Azure Stack 的应用服务 Update 2 的生成号为 **72.0.13698.10**

### <a name="prerequisites"></a>必备组件

> [!IMPORTANT]
> 基于 Azure Stack 的 Azure 应用服务的新部署现在要求提供[三使用者通配型证书](azure-stack-app-service-before-you-get-started.md#get-certificates)，因为目前在 Azure 应用服务中处理适用于 Kudu 的 SSO 的方式已改进。 新的使用者是 **\*.sso.appservice.\<region\>.\<domainname\>.\<extension\>**
>
>

在开始部署之前，请参阅[准备工作文档](azure-stack-app-service-before-you-get-started.md)。

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

- 启用了部署槽位自动交换功能 - [配置自动交换](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap)

- 启用了在生产中测试功能 - [在生产中测试简介](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)

- 启用了 Azure Functions 代理 - [使用 Azure Functions 代理](https://docs.microsoft.com/azure/azure-functions/functions-proxies)

- 针对以下项添加了应用服务管理扩展 UX 支持：
  - 机密轮换
  - 证书轮换
  - 系统凭据轮换
  - 连接字符串轮换

### <a name="known-issues-post-installation"></a>已知问题（安装后）

- 当应用服务部署在现有虚拟网络中并且文件服务器仅在专用网络上可用时，工作人员将无法访问文件服务器。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。 为此，请转到管理门户中的 WorkersNsg 并添加具有以下属性的出站安全规则：
* 源:任意
* 源端口范围：*
* 目标：IP 地址
* 目标 IP 地址范围：文件服务器的 IP 范围
* 目标端口范围：445
* 协议：TCP
* 操作：允许
* 优先级：700
* 姓名：Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>云管理员在操作基于 Azure Stack 的 Azure 应用服务时的已知问题

请参阅 [Azure Stack 1804 发行说明](azure-stack-update-1903.md)中的文档

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 若要详细了解如何完成基于 Azure Stack 的应用服务的部署准备，请参阅[基于 Azure Stack 的应用服务的准备工作](azure-stack-app-service-before-you-get-started.md)。
