---
title: 将 Ruby 应用部署到 Azure Stack 中的虚拟机 | Microsoft Docs
description: 将 Ruby 应用部署到 Azure Stack 中的虚拟机。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: e54fc1cd1e8fe8588a9498b5af8476437880e988
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824183"
---
# <a name="deploy-a-ruby-web-app-to-a-vm-in-azure-stack"></a>将 Ruby Web 应用部署到 Azure Stack 中的 VM

可以创建一个 VM 来托管 Azure Stack 中的 Ruby Web 应用。 在本文中，我们需设置一个服务器，将该服务器配置为托管 Ruby Web 应用，然后将该应用部署到 Azure Stack。

本文使用 Ruby 以及 Ruby on Rails Web 框架。

## <a name="create-a-vm"></a>创建 VM

1. 在 Azure Stack 中设置 VM。 有关说明，请参阅[部署 Linux VM 以在 Azure Stack 中托管 Web 应用](azure-stack-dev-start-howto-deploy-linux.md)。

2. 在“VM 网络”窗格中，确保可以访问以下端口：

    | Port | Protocol | 描述 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议 (HTTP) 是用于从服务器传递网页的协议。 客户端使用 DNS 名称或 IP 地址通过 HTTP 进行连接。 |
    | 443 | HTTPS | 安全超文本传输协议 (HTTPS) 是 HTTP 的安全版本，它需要一个安全证书，并允许对信息进行加密传输。 |
    | 22 | SSH | 安全外壳 (SSH) 是一种用于安全通信的加密网络协议。 你在 SSH 客户端上使用此连接来配置 VM 并部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议 (RDP) 允许远程桌面连接使用计算机的图形用户界面。   |
    | 3000 | 自定义 | 开发中的 Ruby on Rails Web 框架使用的端口。 对于生产服务器，通过 80 和 443 路由流量。 |

## <a name="install-ruby"></a>安装 Ruby

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[使用 PuTTy 通过 SSH 进行连接](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty)。

1. 安装 PPA 存储库。 在 VM 上的 bash 提示符下，输入以下命令：

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. 在 VM 上安装 Ruby 和 Ruby on Rails。 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. 安装 Ruby on Rails 依赖项。 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > 安装 Ruby on Rails 依赖项时，可能需要反复运行 `sudo gem install bundler`。 如果安装失败，请查看错误日志并解决问题。

4. 验证安装。 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash  
        ruby -v
    ```

3. [安装 Git](https://git-scm.com)，一种广泛分布的版本控制和源代码管理 (SCM) 系统。 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>创建并运行应用

1. 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2. 转到新服务器。 应会看到你的 Web 应用程序正在运行。

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>后续步骤

- 详细了解如何[针对 Azure Stack 进行开发](azure-stack-dev-start.md)。
- 了解[用作 IaaS 的 Azure Stack 的常见部署](azure-stack-dev-start-deploy-app.md)。
- 若要了解 Ruby 编程语言并查找 Ruby 的其他资源，请参阅[Ruby-lang.org](https://www.ruby-lang.org)。
