---
title: Azure Stack Hub 上的应用服务 Update 2 发行说明
description: 了解 Azure Stack Hub 上的应用服务 Update 2 中的改进、修复和已知问题。
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/18/2019
ms.openlocfilehash: b06ab0601616f8c089d7bcf90526dae97a8c6e5f
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86489532"
---
# <a name="app-service-on-azure-stack-hub-update-2-release-notes"></a>Azure Stack Hub 上的应用服务 Update 2 发行说明

这些发行说明介绍 Azure Stack Hub 上的 Azure 应用服务 Update 2 中的改进、修复和已知问题。 已知问题分为三个部分：与部署直接相关的问题、更新过程问题，以及内部版本（安装后）的问题。

> [!IMPORTANT]
> 请将 1804 更新应用于 Azure Stack Hub 集成系统，或部署最新的 Azure Stack 开发工具包 (ASDK)，然后部署 Azure 应用服务 1.2。

## <a name="build-reference"></a>内部版本参考

Azure Stack Hub 上的应用服务 Update 2 的内部版本号为 **72.0.13698.10**。

## <a name="prerequisites"></a>先决条件

> [!IMPORTANT]
> 基于 Azure Stack Hub 的 Azure 应用服务的新部署现在要求提供三使用者通配型证书，因为在 Azure 应用服务中处理适用于 Kudu 的 SSO 的方式已改进。 新主题为：\*.sso.appservice.\<region\>.\<domainname\>.\<extension\>****

在开始部署之前，请参阅[在 Azure Stack Hub 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。

## <a name="new-features-and-fixes"></a>新功能和修复

Azure Stack Hub 上的 Azure 应用服务 Update 2 包含以下改进和修复：

- 针对**应用服务租户、管理员、函数门户和 Kudu 工具**的更新。 与 Azure Stack Hub 门户 SDK 版本一致。

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

- 启用了部署槽位自动交换功能 - [配置自动交换](/azure/app-service/deploy-staging-slots#configure-auto-swap)。

- 启用了在生产中测试功能 - [在生产中测试简介](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)。

- 启用了 Azure Functions 代理 - [使用 Azure Functions 代理](/azure/azure-functions/functions-proxies)。

- 针对以下项添加了应用服务管理扩展 UX 支持：
  - 机密轮换
  - 证书轮换
  - 系统凭据轮换
  - 连接字符串轮换

## <a name="known-issues-post-installation"></a>已知问题（安装后）

- 当应用服务部署在现有虚拟网络中并且文件服务器仅在专用网络上可用时，工作人员将无法访问文件服务器。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。 转到管理员门户中的 WorkersNsg 并添加包含以下属性的出站安全规则：

* 源：任意
* 源端口范围：*
* 目标：IP 地址
* 目标 IP 地址范围：文件服务器的 IP 范围
* 目标端口范围：445
* 协议：TCP
* 操作：允许
* 优先级：700
* 姓名：Outbound_Allow_SMB445

## <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>云管理员在操作基于 Azure Stack Hub 的 Azure 应用服务时的已知问题

请参阅 [Azure Stack Hub 1804 发行说明](./release-notes.md?view=azs-2002)中的文档

## <a name="next-steps"></a>后续步骤

- 有关 Azure 应用服务的概述，请参阅[基于 Azure Stack Hub 的 Azure 应用服务概述](azure-stack-app-service-overview.md)。
- 若要详细了解如何完成基于 Azure Stack Hub 的应用服务的部署准备，请参阅[在 Azure Stack Hub 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)。
