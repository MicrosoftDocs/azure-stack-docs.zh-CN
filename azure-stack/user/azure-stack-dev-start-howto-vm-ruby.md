---
title: 在 Azure Stack 集线器中将 Ruby 应用程序部署到虚拟机
description: 将 Ruby 应用部署到 Azure Stack 集线器中的虚拟机。
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 2f4a9d31c1121387efc2310b25703344a85ea5ab
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704108"
---
# <a name="deploy-a-ruby-web-app-to-a-vm-in-azure-stack-hub"></a>在 Azure Stack 集线器中将 Ruby web 应用部署到 VM

可以在 Azure Stack Hub 中创建一个 VM 来托管 Ruby web 应用。 在本文中，你将设置一个服务器，将服务器配置为托管 Ruby web 应用，然后将该应用部署到 Azure Stack 中心。

本文使用 Ruby 和 Ruby on Rails web 框架。

## <a name="create-a-vm"></a>创建 VM

1. 在 Azure Stack Hub 中设置 VM。 有关说明，请参阅[部署 LINUX VM 以在 Azure Stack 中心内托管 web 应用](azure-stack-dev-start-howto-deploy-linux.md)。

2. 在 "VM 网络" 窗格中，确保可访问以下端口：

    | 端口 | 协议 | 说明 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议（HTTP）是用于从服务器传递网页的协议。 客户端通过 HTTP 连接 DNS 名称或 IP 地址。 |
    | 443 | HTTPS | 超文本传输协议（HTTPS）是 HTTP 的安全版本，它需要安全证书，并允许加密传输信息。 |
    | 22 | SSH | 安全外壳（SSH）是一种用于安全通信的加密网络协议。 将此连接与 SSH 客户端结合使用，以配置 VM 和部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议（RDP）允许远程桌面连接在计算机上使用图形用户界面。   |
    | 3000 | 自定义 | 开发中的 Ruby on Rails web 框架使用的端口。 对于生产服务器，可通过80和443路由流量。 |

## <a name="install-ruby"></a>安装 Ruby

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[通过 SSH 连接到 PuTTy](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty)。

1. 安装 PPA 存储库。 在虚拟机上的 bash 提示符下，输入以下命令：

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. 在虚拟机上安装 Ruby 和 Ruby on Rails。 在 SSH 会话中仍连接到 VM 时，请输入以下命令：

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. 安装 Ruby on Rails 依赖项。 在 SSH 会话中仍连接到 VM 时，请输入以下命令：

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > 在安装 Ruby on Rails 依赖项时，可能需要反复运行 `sudo gem install bundler`。 如果安装失败，请查看错误日志，并解决问题。

4. 验证你的安装。 在 SSH 会话中仍连接到 VM 时，请输入以下命令：

    ```bash  
        ruby -v
    ```

3. [安装 Git](https://git-scm.com)，这是一个广泛分发的版本控制和源代码管理（SCM）系统。 在 SSH 会话中仍连接到 VM 时，请输入以下命令：

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>创建并运行应用

1. 在 SSH 会话中仍连接到 VM 时，请输入以下命令：

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2. 中转到新服务器。 应会看到正在运行的 web 应用程序。

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>后续步骤

- 了解有关如何[针对 Azure Stack 中心进行开发的](azure-stack-dev-start.md)详细信息。
- 了解[Azure Stack 集线器作为 IaaS 的常见部署](azure-stack-dev-start-deploy-app.md)。
- 若要了解 Ruby 编程语言并查找 Ruby 的其他资源，请参阅[Ruby-lang.org](https://www.ruby-lang.org)。
