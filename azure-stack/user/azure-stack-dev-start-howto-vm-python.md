---
title: 将 Python Web 应用部署到 Azure Stack Hub 中的虚拟机
description: 将 Python Web 应用部署到 Azure Stack Hub 中的虚拟机。
author: mattbriggs
ms.topic: overview
ms.date: 3/12/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 3/12/2020
ms.openlocfilehash: 700589e985aa651a1a7ae4a5ffbc020b8e6a2d3f
ms.sourcegitcommit: 4138a2a15f78e7db38b3a29acc963a71937146fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "79313001"
---
# <a name="deploy-a-python-web-app-to-a-vm-in-azure-stack-hub"></a>将 Python Web 应用部署到 Azure Stack Hub 中的 VM

可以创建一个 VM 来托管 Azure Stack Hub 中的 Python Web 应用。 在本文中，你将设置一个服务器，将该服务器配置为托管 Python Web 应用，然后将该应用部署到 Azure Stack Hub。

本文使用 Python 3.x 在 Nginx 服务器上的虚拟环境中运行 Flask。

## <a name="create-a-vm"></a>创建 VM

1. 按照[部署 Linux VM 以在 Azure Stack Hub 中托管 Web 应用](azure-stack-dev-start-howto-deploy-linux.md)中的说明，在 Azure Stack Hub 中设置 VM。

2. 在“VM 网络”窗格中，确保可以访问以下端口：

    | 端口 | 协议 | 说明 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议 (HTTP) 是用于从服务器传递网页的协议。 客户端使用 DNS 名称或 IP 地址通过 HTTP 进行连接。 |
    | 443 | HTTPS | 安全超文本传输协议 (HTTPS) 是 HTTP 的安全版本，它需要一个安全证书，并允许对信息进行加密传输。 |
    | 22 | SSH | 安全外壳 (SSH) 是一种用于安全通信的加密网络协议。 你在 SSH 客户端上使用此连接来配置 VM 并部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议 (RDP) 允许远程桌面连接使用计算机的图形用户界面。   |
    | 5000、8000 | 自定义 | 开发中的 Flask Web 框架使用的端口。 对于生产服务器，通过 80 和 443 路由流量。 |

## <a name="install-python"></a>安装 Python

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[使用 PuTTy 通过 SSH 进行连接](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty)。
2. 在 VM 上的 bash 提示符下，输入以下命令：

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. 验证你的安装。 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash  
        python -version
    ```

3. [安装 Nginx](https://www.nginx.com/resources/wiki/)（一个轻量级 Web 服务器）。 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. [安装 Git](https://git-scm.com)，一种广泛分布的版本控制和源代码管理 (SCM) 系统。 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>部署和运行应用

1. 在 VM 上设置 Git 存储库。 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash  
       git clone https://github.com/Azure-Samples/azure-stack-hub-flask-hello-world.git
    
       cd azure-stack-hub-flask-hello-world
    ```

2. 创建一个虚拟环境，在其中填充所有包依赖项。 仍在 SSH 会话中连接到 VM 时，输入以下命令：

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3. 转到新服务器。 应会看到你的 Web 应用程序正在运行。

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>更新服务器

1. 在 SSH 会话中连接到 VM。 通过键入 Ctrl+C 来停止服务器。

2. 输入以下命令：

    ```bash  
    deactivate
    open the git repo
    git pull
    ```

3. 激活虚拟环境并启动应用：

    ```bash  
    source venv/bin/activate
    export FLASK_APP=application.py
    flask run -h 0.0.0.0
    ```

## <a name="next-steps"></a>后续步骤

- 详细了解如何[针对 Azure Stack Hub 进行开发](azure-stack-dev-start.md)。
- 了解[用作 IaaS 的 Azure Stack Hub 的常见部署](azure-stack-dev-start-deploy-app.md)。
- 若要了解 Python 编程语言并找到适用于 Python 的其他资源，请参阅 [Python.org](https://www.python.org)。
