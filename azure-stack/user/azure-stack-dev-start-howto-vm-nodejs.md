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
ms.openlocfilehash: 4fcf76b8f4950fa7ca919d57281c5662b31e96f6
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838305"
---
# <a name="deploy-a-nodejs-web-app-to-a-vm-in-azure-stack"></a>将 Node.js web 应用部署到 Azure Stack 中的 VM

可以创建虚拟机 (VM) 来托管在 Azure Stack 中的 Node.js web 应用。 在本文中，您将服务器设置、 配置服务器以承载 Node.js web 应用，然后将应用部署到 Azure Stack。

## <a name="create-a-vm"></a>创建 VM

1. 在 Azure Stack 中将 VM 设置中的说明[部署 Linux VM 托管在 Azure Stack 中的 web 应用](azure-stack-dev-start-howto-deploy-linux.md)。

2. 在 VM 网络窗格中，请确保可以访问以下端口：

    | Port | Protocol | 描述 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议 (HTTP) 是用于从服务器提供网页的协议。 客户端通过 HTTP 进行连接的 DNS 名称或 IP 地址。 |
    | 443 | HTTPS | 超文本传输协议安全 (HTTPS) 是 HTTP 的一个安全的需要的安全证书，并允许加密传输信息版本。 |
    | 22 | SSH | 安全外壳 (SSH) 是安全通信的加密的网络协议。 使用 SSH 客户端使用此连接，将 VM 配置和部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议 (RDP) 允许在计算机上使用图形用户界面的远程桌面连接。   |
    | 1337 | 自定义 | 通过 Node.js 使用的端口。 对于生产服务器，你将通信路由通过 80 和 443。 |

## <a name="install-node"></a>安装节点

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[通过使用 PuTTY SSH 连接](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty)。

1. 在 VM 上在 bash 提示符处，输入以下命令：

    ```bash  
      sudo apt install nodejs-legacy
    ```

2. [安装 NPM](https://www.npmjs.com/)，Node.js 包或模块的包管理器。 仍连接到你的 VM 在 SSH 会话中，输入以下命令：

    ```bash  
       go version
    ```

3. [安装 Git](https://git-scm.com)，广泛分布的版本控制和源的代码管理 (SCM) 系统。 在你仍然连接 SSH 会话中为你的 VM，请输入以下命令：

    ```bash  
       sudo apt-get -y install git
    ```

3. 验证你的安装。 在你仍然连接 SSH 会话中为你的 VM，请输入以下命令：

    ```bash  
       node -v
    ```

## <a name="deploy-and-run-the-app"></a>部署和运行应用

1. 设置 Git 存储库在 VM 上。 尽管你仍连接在 SSH 会话中到 VM，输入以下命令：

    ```bash  
       git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
    
       cd nodejs-docs-hello-world
        npm start
    ```

2. 启动应用。 在你仍然连接 SSH 会话中为你的 VM，请输入以下命令：

    ```bash  
       sudo node app.js
    ```

3. 请转到新服务器。 应会看到正在运行的 web 应用程序。

    ```HTTP  
       http://yourhostname.cloudapp.net:1337
    ```

## <a name="next-steps"></a>后续步骤

- 了解如何[开发适用于 Azure Stack](azure-stack-dev-start.md)。
- 了解如何[IaaS 作为 Azure Stack 的常见部署](azure-stack-dev-start-deploy-app.md)。
- 若要了解编程语言的节点和节点中找到的其他资源，请参阅[Nodejs.org](https://nodejs.org)。
