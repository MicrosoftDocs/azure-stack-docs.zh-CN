---
title: 在 Azure Stack 集线器中将 "中转 web 应用" 部署到虚拟机
description: 如何在 Azure Stack 集线器中将 "中转" web 应用部署到 VM
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: e57de0bbcb51b4e1bf2d3d4501b57cbc5115927e
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883761"
---
# <a name="deploy-a-go-web-app-to-a-vm-in-azure-stack-hub"></a>在 Azure Stack 集线器中将 "中转" web 应用部署到 VM

你可以创建一个虚拟机（VM）以在 Azure Stack 中心内托管一个 "开始" web 应用。 在本文中，你将设置一个服务器，将服务器配置为托管你的 web 应用，然后将该应用部署到 Azure Stack 中心。

## <a name="create-a-vm"></a>创建 VM

1. 按照[部署 LINUX VM 以在 Azure Stack 中心中承载 web 应用](azure-stack-dev-start-howto-deploy-linux.md)中的说明，在 Azure Stack 集线器中设置 VM。

2. 在 "VM 网络" 窗格中，确保可访问以下端口：

    | Port | 协议 | Description |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议（HTTP）是用于从服务器传递网页的协议。 客户端通过 HTTP 连接 DNS 名称或 IP 地址。 |
    | 443 | HTTPS | 超文本传输协议（HTTPS）是 HTTP 的安全版本，它需要安全证书，并允许加密传输信息。 |
    | 22 | SSH | 安全外壳（SSH）是一种用于安全通信的加密网络协议。 将此连接与 SSH 客户端结合使用，以配置 VM 和部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议（RDP）允许远程桌面连接在计算机上使用图形用户界面。   |
    | 3000 | 自定义 | 使用端口3000正在开发的 web 框架。 对于生产服务器，可通过80和443路由流量。 |

## <a name="install-go"></a>安装开始

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[通过 SSH 连接到 PuTTY ](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty)。

1. 在虚拟机上的 bash 提示符下，输入以下命令：

    ```bash  
    wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
    sudo tar -xvf go1.10.linux-amd64.tar.gz
    sudo mv go /usr/local
    ```

2. 设置虚拟机上的 "离开" 环境。 在 SSH 会话中仍连接到 VM 时，请输入以下命令：

    ```bash  
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/Projects/ADMFactory/Golang
    export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

    vi ~/.profile
    ```

3. 验证你的安装。 在 SSH 会话中仍连接到 VM 时，请输入以下命令：

    ```bash  
        go version
    ```

3. [安装 Git](https://git-scm.com)，这是一个广泛分发的版本控制和源代码管理（SCM）系统。 在 SSH 会话中仍连接到 VM 时，请输入以下命令：

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>部署和运行应用

1. 在 VM 上设置 Git 存储库。 在 SSH 会话中仍连接到 VM 时，请输入以下命令：

    ```bash  
       git clone https://github.com/appleboy/go-hello
    
       cd go-hello
       go get -d
    ```

2. 启动应用。 在 SSH 会话中仍连接到 VM 时，请输入以下命令：

    ```bash  
       go run hello-world.go
    ```

3. 中转到新服务器。 应会看到正在运行的 web 应用程序。

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>后续步骤

- 了解有关如何[针对 Azure Stack 中心进行开发的](azure-stack-dev-start.md)详细信息。
- 了解[Azure Stack 集线器作为 IaaS 的常见部署](azure-stack-dev-start-deploy-app.md)。
- 若要了解转向编程语言并查找其他资源，请参阅[Golang.org](https://golang.org)。
