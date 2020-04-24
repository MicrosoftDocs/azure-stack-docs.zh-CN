---
title: 将 Node.js 应用部署到 Azure Stack Hub 中的虚拟机
description: 将 Node.js 应用部署到 Azure Stack Hub。
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/20
ms.openlocfilehash: 3166982098c68760f4f20f4094038f2ab275b73f
ms.sourcegitcommit: 4138a2a15f78e7db38b3a29acc963a71937146fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "77703054"
---
# <a name="deploy-a-nodejs-web-app-to-a-vm-in-azure-stack-hub"></a>将 Node.js Web 应用部署到 Azure Stack Hub 中的 VM

可以创建一个虚拟机 (VM) 来托管 Azure Stack Hub 中的 Node.js Web 应用。 在本文中，你将设置一个服务器，将该服务器配置为托管 Node.js Web 应用，然后将该应用部署到 Azure Stack Hub。

## <a name="create-a-vm"></a>创建 VM

1. 按照[部署 Linux VM 以在 Azure Stack Hub 中托管 Web 应用](azure-stack-dev-start-howto-deploy-linux.md)中的说明，在 Azure Stack Hub 中设置 VM。

2. 在“VM 网络”窗格中，确保可以访问以下端口：

    | 端口 | 协议 | 说明 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议 (HTTP) 是用于从服务器传递网页的协议。 客户端使用 DNS 名称或 IP 地址通过 HTTP 进行连接。 |
    | 443 | HTTPS | 安全超文本传输协议 (HTTPS) 是 HTTP 的安全版本，它需要一个安全证书，并允许对信息进行加密传输。 |
    | 22 | SSH | 安全外壳 (SSH) 是一种用于安全通信的加密网络协议。 你在 SSH 客户端上使用此连接来配置 VM 并部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议 (RDP) 允许远程桌面连接使用计算机的图形用户界面。   |
    | 1337 | 自定义 | Node.js 使用的端口。 对于生产服务器，通过 80 和 443 路由流量。 |

## <a name="install-node"></a>安装 Node

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[使用 PuTTY 通过 SSH 进行连接](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty)。

1. 在 VM 上的 bash 提示符下，输入以下命令：

    ```bash  
      sudo apt install nodejs-legacy
    ```

2. [安装 NPM](https://www.npmjs.com/)（Node.js 包或模块的包管理器）。 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash  
       node --version
    ```

3. [安装 Git](https://git-scm.com)（一种广泛分布的版本控制和源代码管理 (SCM) 系统）。 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash  
       sudo apt-get -y install git
    ```

3. 验证你的安装。 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash  
       node -v
    ```

## <a name="deploy-and-run-the-app"></a>部署和运行应用

1. 在 VM 上设置 Git 存储库。 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash  
       git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
    
       cd nodejs-docs-hello-world
        npm start
    ```

2. 启动应用。 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash  
       sudo node app.js
    ```

3. 转到新服务器。 应会看到你的 Web 应用程序正在运行。

    ```HTTP  
       http://yourhostname.cloudapp.net:1337
    ```

## <a name="next-steps"></a>后续步骤

- 详细了解如何[针对 Azure Stack Hub 进行开发](azure-stack-dev-start.md)。
- 了解[用作 IaaS 的 Azure Stack Hub 的常见部署](azure-stack-dev-start-deploy-app.md)。
- 若要了解 Node 编程语言并找到适用于 Node 的其他资源，请参阅 [Nodejs.org](https://nodejs.org)。
