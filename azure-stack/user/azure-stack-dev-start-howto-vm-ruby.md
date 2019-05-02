---
title: 到 Azure Stack 中的虚拟机部署 Ruby 应用 |Microsoft Docs
description: 将 Ruby 应用程序部署到 Azure Stack 中的虚拟机。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 7744d1adcdcb1dde53c6ef887498a9a3978f4513
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2019
ms.locfileid: "64481936"
---
# <a name="how-to-deploy-a-ruby-web-app-to-a-vm-in-azure-stack"></a>如何将 Ruby web 应用部署到 Azure Stack 中的 VM

可以创建用于承载 Ruby Web 应用在 Azure Stack 中的虚拟机。 本文探讨您需要在设置服务器、 配置服务器以托管 Ruby web 应用，然后部署应用程序中遵循的步骤。

Ruby 是一种语言的小心平衡。 其创建者，Yukihiro"Matz"Matsumoto 混合他最喜欢的语言 （Perl、 Smalltalk、 Eiffel、 Ada 和 Lisp） 形成一种新语言平衡函数编程和命令式编程的部分。 若要了解 Ruby 编程语言，并查找用于 Python 的其他资源，请参阅[Ruby lang.org](https://www.ruby-lang.org)。

本文将 on Rails web 框架中使用 Ruby 和 Ruby。

## <a name="create-a-vm"></a>创建 VM

1. 设置 Azure Stack 中设置 VM。 按照中的步骤[部署 Linux VM 托管在 Azure Stack 中的 web 应用](azure-stack-dev-start-howto-deploy-linux.md)。

2. 在 VM 网络的边栏选项卡，确保以下端口可访问：

    | 端口 | Protocol | 描述 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议 (HTTP) 是分布式、 协作式和超媒体信息系统应用程序协议。 客户端将连接到 web 应用使用的公共 IP 或 DNS 名称的 VM。 |
    | 443 | HTTPS | 安全超文本传输协议 (HTTPS) 是一个扩展的超文本传输协议 (HTTP)。 它用于通过计算机网络的安全通信。 客户端将连接到 web 应用使用的公共 IP 或 DNS 名称的 VM。 |
    | 22 | SSH | 安全外壳 (SSH) 是加密的网络协议进行安全地运行网络服务，通过不安全的网络。 要将此连接用于 SSH 客户端将 VM 配置和部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议允许使用图形用户界面的远程桌面连接你的计算机。   |
    | 3000 | “自定义” | Ruby 上 rails web 框架开发中使用端口 3000。 对于生产服务器，要在通过路由流量 80 和 443。 |

## <a name="install-ruby"></a>安装 Ruby

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[通过使用 PuTTy SSH 连接](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty)。
1. 安装 PPA 存储库。 在 VM 上在 bash 提示符处，键入以下命令：

    ```bash  
    sudo apt -y install software-properties-common
    sudo apt-add-repository ppa:brightbox/ruby-ng

    sudo apt update
    ```

2. 安装 Ruby 和 Ruby rails 在 VM 上的。 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
    sudo apt install ruby
    gem install rails -v 4.2.6
    ```

3. 安装 Ruby rails 依赖项。 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
    sudo apt-get install make
    sudo apt-get install gcc
    sudo apt-get install sqlite3
    sudo apt-get install nodejs
    sudo gem install sqlite
    sudo gem install bundler
    ```

    > [!Note]  
    > 在安装时，您可能需要重复运行`sudo gem install bundler`。 如果依赖项尝试进行安装和失败，请查看错误日志并解决问题。

4. 验证你的安装。 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
        ruby -v
    ```

3. 安装 Git。 [Git](https://git-scm.com)是广泛分布的修订控制和源代码代码管理 (SCM) 系统。 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="create-and-run-an-app"></a>创建并运行应用

1. 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash
        rails new myapp
        cd myapp
        rails server -b 0.0.0.0 -p 3000
    ```

2.  现在导航到新服务器，您应看到正在运行的 web 应用程序。

    ```HTTP  
       http://yourhostname.cloudapp.net:3000
    ```

## <a name="next-steps"></a>后续步骤

- 了解有关如何对[开发适用于 Azure Stack](azure-stack-dev-start.md)
- 了解如何[IaaS 作为 Azure Stack 的常见部署](azure-stack-dev-start-deploy-app.md)。