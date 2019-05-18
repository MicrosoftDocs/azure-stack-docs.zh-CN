---
title: 转到 web 应用部署到 Azure Stack 中的虚拟机 |Microsoft Docs
description: 如何将转到 web 应用部署到 Azure Stack 中的 VM
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 8b1f207929e018b27bb3f20db8d2b7d5abe46088
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838347"
---
# <a name="deploy-a-go-web-app-to-a-vm-in-azure-stack"></a>将转到 web 应用部署到 Azure Stack 中的 VM

可以创建虚拟机 (VM) 来托管在 Azure Stack 中的转到 web 应用。 在本文中，您将服务器设置、 配置服务器以承载转到 web 应用，然后将应用部署到 Azure Stack。

## <a name="create-a-vm"></a>创建 VM

1. 在 Azure Stack 中将 VM 设置中的说明[部署 Linux VM 托管在 Azure Stack 中的 web 应用](azure-stack-dev-start-howto-deploy-linux.md)。

2. 在 VM 网络窗格中，请确保可以访问以下端口：

    | Port | Protocol | 描述 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议 (HTTP) 是用于从服务器提供网页的协议。 客户端通过 HTTP 进行连接的 DNS 名称或 IP 地址。 |
    | 443 | HTTPS | 超文本传输协议安全 (HTTPS) 是 HTTP 的一个安全的需要的安全证书，并允许加密传输信息版本。 |
    | 22 | SSH | 安全外壳 (SSH) 是安全通信的加密的网络协议。 使用 SSH 客户端使用此连接，将 VM 配置和部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议 (RDP) 允许在计算机上使用图形用户界面的远程桌面连接。   |
    | 3000 | 自定义 | 转到 web 框架开发中使用端口 3000。 对于生产服务器，你将通信路由通过 80 和 443。 |

## <a name="install-go"></a>安装 Go

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[通过使用 PuTTY SSH 连接](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty)。

1. 在 bash 提示符处，在 VM 上，输入以下命令：

    ```bash  
    wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
    sudo tar -xvf go1.10.linux-amd64.tar.gz
    sudo mv go /usr/local
    ```

2. 设置你的 VM 上的转到环境。 尽管你仍连接在 SSH 会话中到 VM，输入以下命令：

    ```bash  
    export GOROOT=/usr/local/go
    export GOPATH=$HOME/Projects/ADMFactory/Golang
    export PATH=$GOPATH/bin:$GOROOT/bin:$PATH

    vi ~/.profile
    ```

3. 验证你的安装。 在你仍然连接 SSH 会话中为你的 VM，请输入以下命令：

    ```bash  
        go version
    ```

3. [安装 Git](https://git-scm.com)，广泛分布的版本控制和源代码代码管理 (SCM) 系统。 在你仍然连接 SSH 会话中为你的 VM，请输入以下命令：

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>部署和运行应用

1. 设置 Git 存储库在 VM 上。 尽管你仍连接在 SSH 会话中到 VM，输入以下命令：

    ```bash  
       git clone https://github.com/appleboy/go-hello
    
       cd go-hello
       go get -d
    ```

2. 启动应用。 在你仍然连接 SSH 会话中为你的 VM，请输入以下命令：

    ```bash  
       go run hello-world.go
    ```

3. 请转到新服务器。 应会看到正在运行的 web 应用程序。

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>后续步骤

- 了解如何[开发适用于 Azure Stack](azure-stack-dev-start.md)。
- 了解如何[IaaS 作为 Azure Stack 的常见部署](azure-stack-dev-start-deploy-app.md)。
- 若要了解 Go 编程语言，并找到适用于 Go 的其他资源，请参阅[Golang.org](https://golang.org)。
