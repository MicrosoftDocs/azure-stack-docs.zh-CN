---
title: 将 node.js 应用部署到 Azure Stack 集线器中的虚拟机
description: 将 node.js 应用部署到 Azure Stack 中心。
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: ab6d3e378c4a6f40e597367f06d9e5c4e31eb0c3
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883751"
---
# <a name="deploy-a-nodejs-web-app-to-a-vm-in-azure-stack-hub"></a>将 node.js web 应用部署到 Azure Stack 集线器中的 VM

你可以创建虚拟机（VM）以在 Azure Stack Hub 中承载 node.js web 应用。 在本文中，你将设置一个服务器，将服务器配置为托管 node.js web 应用，然后将该应用部署到 Azure Stack 中心。

## <a name="create-a-vm"></a>创建 VM

1. 按照[部署 LINUX VM 以在 Azure Stack 中心中承载 web 应用](azure-stack-dev-start-howto-deploy-linux.md)中的说明，在 Azure Stack 集线器中设置 VM。

2. 在 "VM 网络" 窗格中，确保可访问以下端口：

    | Port | 协议 | Description |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议（HTTP）是用于从服务器传递网页的协议。 客户端通过 HTTP 连接 DNS 名称或 IP 地址。 |
    | 443 | HTTPS | 超文本传输协议（HTTPS）是 HTTP 的安全版本，它需要安全证书，并允许加密传输信息。 |
    | 22 | SSH | 安全外壳（SSH）是一种用于安全通信的加密网络协议。 将此连接与 SSH 客户端结合使用，以配置 VM 和部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议（RDP）允许远程桌面连接在计算机上使用图形用户界面。   |
    | 1337 | 自定义 | Node.js 使用的端口。 对于生产服务器，可通过80和443路由流量。 |

## <a name="install-node"></a>安装节点

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[通过 SSH 连接到 PuTTY](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty)。

1. 在虚拟机上的 bash 提示符下，输入以下命令：

    ```bash  
      sudo apt install nodejs-legacy
    ```

2. [安装 NPM](https://www.npmjs.com/)，用于 node.js 包或模块的包管理器。 如果仍在 SSH 会话中连接到 VM，请输入以下命令：

    ```bash  
       node --version
    ```

3. [安装 Git](https://git-scm.com)，这是一个广泛分发的版本控制和源代码管理（SCM）系统。 在 SSH 会话中仍连接到 VM 时，请输入以下命令：

    ```bash  
       sudo apt-get -y install git
    ```

3. 验证你的安装。 在 SSH 会话中仍连接到 VM 时，请输入以下命令：

    ```bash  
       node -v
    ```

## <a name="deploy-and-run-the-app"></a>部署和运行应用

1. 在 VM 上设置 Git 存储库。 在 SSH 会话中仍连接到 VM 时，请输入以下命令：

    ```bash  
       git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
    
       cd nodejs-docs-hello-world
        npm start
    ```

2. 启动应用。 在 SSH 会话中仍连接到 VM 时，请输入以下命令：

    ```bash  
       sudo node app.js
    ```

3. 中转到新服务器。 应会看到正在运行的 web 应用程序。

    ```HTTP  
       http://yourhostname.cloudapp.net:1337
    ```

## <a name="next-steps"></a>后续步骤

- 了解有关如何[针对 Azure Stack 中心进行开发的](azure-stack-dev-start.md)详细信息。
- 了解[Azure Stack 集线器作为 IaaS 的常见部署](azure-stack-dev-start-deploy-app.md)。
- 若要了解 Node 编程语言并查找节点的其他资源，请参阅[Nodejs.org](https://nodejs.org)。
