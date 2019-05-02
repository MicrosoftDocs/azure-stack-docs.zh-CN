---
title: 将 Node.js 应用部署到 Azure Stack 中的虚拟机 |Microsoft Docs
description: 将 Node.js 应用部署到 Azure Stack。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: bebf3b349a994379d5f54bd387533b8d4a63ccdd
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2019
ms.locfileid: "64481925"
---
# <a name="how-to-deploy-a-nodejs-web-app-to-a-vm-in-azure-stack"></a>如何将 Node.js web 应用部署到 Azure Stack 中的 VM

可以创建用于托管你的 Node.js Web 应用在 Azure Stack 中的虚拟机。 本文探讨您需要在设置服务器、 配置服务器以托管节点的 web 应用，然后部署应用程序中遵循的步骤。

Node.js 是一个基于 Chrome V8 JavaScript 引擎的 JavaScript 运行时。 作为异步事件驱动的 JavaScript 运行时，节点旨在构建可缩放的网络应用程序。 若要了解编程语言的节点和节点中找到的其他资源，请参阅[Nodejs.org](https://nodejs.org)。

## <a name="create-a-vm"></a>创建 VM

1. 设置 Azure Stack 中设置 VM。 按照中的步骤[部署 Linux VM 托管在 Azure Stack 中的 web 应用](azure-stack-dev-start-howto-deploy-linux.md)。

2. 在 VM 网络的边栏选项卡，确保以下端口可访问：

    | 端口 | Protocol | 描述 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议 (HTTP) 是分布式、 协作式和超媒体信息系统应用程序协议。 客户端将连接到 web 应用使用的公共 IP 或 DNS 名称的 VM。 |
    | 443 | HTTPS | 安全超文本传输协议 (HTTPS) 是一个扩展的超文本传输协议 (HTTP)。 它用于通过计算机网络的安全通信。 客户端将连接到 web 应用使用的公共 IP 或 DNS 名称的 VM。 |
    | 22 | SSH | 安全外壳 (SSH) 是加密的网络协议进行安全地运行网络服务，通过不安全的网络。 要将此连接用于 SSH 客户端将 VM 配置和部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议允许使用图形用户界面的远程桌面连接你的计算机。   |
    | 1337 | “自定义” | 通过 Node.js 使用端口 1337年。 对于生产服务器，要在通过路由流量 80 和 443。 |

## <a name="install-node"></a>安装节点

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[通过使用 PuTTy SSH 连接](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty)。
1. 在 VM 上在 bash 提示符处，键入以下命令：

    ```bash  
      sudo apt install nodejs-legacy
    ```

2. 安装 NPM。 [NPM](https://www.npmjs.com/)是 Node.js 的程序包或模块的程序包管理器。 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
       go version
    ```

3. 安装 Git。 [Git](https://git-scm.com)是广泛分布的修订控制和源代码代码管理 (SCM) 系统。 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
       sudo apt-get -y install git
    ```

3. 验证你的安装。 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
       node -v
    ```

## <a name="deploy-and-run-the-app"></a>部署和运行应用

1. 设置 Git 存储库在 VM 上。 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
       git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
    
       cd nodejs-docs-hello-world
        npm start
    ```

2. 启动应用。 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
       sudo node app.js
    ```

3.  现在导航到新服务器，您应看到正在运行的 web 应用程序。

    ```HTTP  
       http://yourhostname.cloudapp.net:1337
    ```

## <a name="next-steps"></a>后续步骤

- 了解有关如何对[开发适用于 Azure Stack](azure-stack-dev-start.md)
- 了解如何[IaaS 作为 Azure Stack 的常见部署](azure-stack-dev-start-deploy-app.md)。