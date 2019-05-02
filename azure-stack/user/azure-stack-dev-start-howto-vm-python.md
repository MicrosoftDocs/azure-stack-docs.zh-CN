---
title: 如何将 Python web 应用部署到 Azure Stack 中的虚拟机 |Microsoft Docs
description: 将 Python web 应用部署到 Azure Stack 中的虚拟机。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: b1a588dd084962c095a534f6569333b34e694bc6
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2019
ms.locfileid: "64481837"
---
# <a name="how-to-deploy-a-python-web-app-to-a-vm-in-azure-stack"></a>如何将 Python web 应用部署到 Azure Stack 中的 VM

可以创建用于托管在 Azure Stack 中的将 Python Web 应用的虚拟机。 本文探讨您需要在设置服务器、 配置服务器来托管 Python web 应用，然后部署应用程序中遵循的步骤。

Python 是解释型、 高级、 通用的编程语言。 创建的 Guido van Rossum 和在 1991 年首次发布，Python 也有的设计理念，强调代码的可读性，值得注意的使用有意义的空格。 它提供了启用小型和大型刻度上的清除编程构造。 若要了解 Python 编程语言，并查找用于 Python 的其他资源，请参阅[Python.org](https://www.python.org)。

本文将使用 Python 3.x Ngnix 服务器上的虚拟环境中运行 Flask。

## <a name="create-a-vm"></a>创建 VM

1. 设置 Azure Stack 中设置 VM。 按照中的步骤[部署 Linux VM 托管在 Azure Stack 中的 web 应用](azure-stack-dev-start-howto-deploy-linux.md)。

2. 在 VM 网络的边栏选项卡，确保以下端口可访问：

    | 端口 | Protocol | 描述 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议 (HTTP) 是分布式、 协作式和超媒体信息系统应用程序协议。 客户端将连接到 web 应用使用的公共 IP 或 DNS 名称的 VM。 |
    | 443 | HTTPS | 安全超文本传输协议 (HTTPS) 是一个扩展的超文本传输协议 (HTTP)。 它用于通过计算机网络的安全通信。 客户端将连接到 web 应用使用的公共 IP 或 DNS 名称的 VM。 |
    | 22 | SSH | 安全外壳 (SSH) 是加密的网络协议进行安全地运行网络服务，通过不安全的网络。 要将此连接用于 SSH 客户端将 VM 配置和部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议允许使用图形用户界面的远程桌面连接你的计算机。   |
    | 5000, 8000 | “自定义” | 使用 Flask web 框架中开发的端口 5000，8000。 对于生产服务器，要在通过路由流量 80 和 443。 |

## <a name="install-python"></a>安装 Python

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[通过使用 PuTTy SSH 连接](azure-stack-dev-start-howto-ssh-public-key.md#connect-via-ssh-with-putty)。
2. 在 VM 上在 bash 提示符处，键入以下命令：

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. 验证你的安装。 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
        python -version
    ```


3. 安装 Nginx。 [Nginx](https://www.nginx.com/resources/wiki/)是 web 服务器，它还可用作反向代理、 负载均衡器、 邮件代理服务器和 HTTP 缓存。 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. 安装 Git。 [Git](https://git-scm.com)是广泛分布的修订控制和源代码代码管理 (SCM) 系统。 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>部署和运行应用

1. 设置 Git 存储库在 VM 上。 仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
       git clone https://github.com/mattbriggs/flask-hello-world.git
    
       cd flask-hello-world
    ```

2. 创建虚拟环境并填充所有包依赖项。  仍连接到你的 VM 在 SSH 会话中，键入以下命令：

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3.  现在导航到新服务器，您应看到正在运行的 web 应用程序。

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>更新服务器

1. 连接到 VM 的 SSH 会话。 通过键入来停止服务器`CTRL+C`。
2. 键入以下命令：

    ```bash  
    deactivate
    open the git repo
    git pull
    ```

3. 激活虚拟环境，并启动应用

    ```bash  
    source venv/bin/activate
    export FLASK_APP=application.py
    flask run -h 0.0.0.0
    ```

## <a name="next-steps"></a>后续步骤

- 了解有关如何对[开发适用于 Azure Stack](azure-stack-dev-start.md)
- 了解如何[IaaS 作为 Azure Stack 的常见部署](azure-stack-dev-start-deploy-app.md)。
