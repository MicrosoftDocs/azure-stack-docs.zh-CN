---
title: 将 Python web 应用部署到 Azure Stack 中的虚拟机 |Microsoft Docs
description: 将 Python web 应用部署到 Azure Stack 中的虚拟机。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: f37e963ad73a361f9d4cd5a6e68ec4213d5f32fb
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838325"
---
# <a name="deploy-a-python-web-app-to-a-vm-in-azure-stack"></a>将 Python web 应用部署到 Azure Stack 中的 VM

可以创建用于托管在 Azure Stack 中的将 Python web 应用的虚拟机。 在本文中，将服务器设置、 配置服务器以托管你的 Python web 应用，然后将应用部署到 Azure Stack。

本文使用 Python 3.x 的 Nginx 服务器上的虚拟环境中运行 Flask。

## <a name="create-a-vm"></a>创建 VM

1. 在 Azure Stack 中将 VM 设置中的说明[部署 Linux VM 托管在 Azure Stack 中的 web 应用](azure-stack-dev-start-howto-deploy-linux.md)。

2. 在 VM 网络窗格中，请确保可以访问以下端口：

    | Port | Protocol | 描述 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议 (HTTP) 是用于从服务器提供网页的协议。 客户端通过 HTTP 进行连接的 DNS 名称或 IP 地址。 |
    | 443 | HTTPS | 超文本传输协议安全 (HTTPS) 是 HTTP 的一个安全的需要的安全证书，并允许加密传输信息版本。 |
    | 22 | SSH | 安全外壳 (SSH) 是安全通信的加密的网络协议。 使用 SSH 客户端使用此连接，将 VM 配置和部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议 (RDP) 允许在计算机上使用图形用户界面的远程桌面连接。   |
    | 5000, 8000 | 自定义 | Flask web 框架开发中使用的端口。 对于生产服务器，你将通信路由通过 80 和 443。 |

## <a name="install-python"></a>安装 Python

1. 使用 SSH 客户端连接到 VM。 有关说明，请参阅[通过使用 PuTTy SSH 连接](azure-stack-dev-start-howto-ssh-public-key.md#connect-with-ssh-by-using-putty)。
2. 在 VM 上在 bash 提示符处，输入以下命令：

    ```bash  
    sudo apt-get -y install python3 python3-venv python3-dev
    ```

3. 验证你的安装。 在你仍然连接 SSH 会话中为你的 VM，请输入以下命令：

    ```bash  
        python -version
    ```

3. [安装 Nginx](https://www.nginx.com/resources/wiki/)，轻型 web 服务器。 在你仍然连接 SSH 会话中为你的 VM，请输入以下命令：

    ```bash  
       sudo apt-get -y install nginx git
    ```

4. [安装 Git](https://git-scm.com)，广泛分布的版本控制和源代码代码管理 (SCM) 系统。 在你仍然连接 SSH 会话中为你的 VM，请输入以下命令：

    ```bash  
       sudo apt-get -y install git
    ```

## <a name="deploy-and-run-the-app"></a>部署和运行应用

1. 设置 Git 存储库在 VM 上。 尽管你仍连接在 SSH 会话中到 VM，输入以下命令：

    ```bash  
       git clone https://github.com/mattbriggs/flask-hello-world.git
    
       cd flask-hello-world
    ```

2. 创建虚拟环境中，并填充所有包依赖项。 尽管你仍连接在 SSH 会话中到 VM，输入以下命令：

    ```bash  
    python3 -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    
    export FLASK_APP=application.py
    # export FLASK_DEBUG=1 
    flask run -h 0.0.0.0
    ```

3. 请转到新服务器。 应会看到正在运行的 web 应用程序。

    ```HTTP  
       http://yourhostname.cloudapp.net:5000
    ```

## <a name="update-your-server"></a>更新服务器

1. 连接到 VM 的 SSH 会话。 通过键入 Ctrl + C 停止服务器。

2. 输入以下命令：

    ```bash  
    deactivate
    open the git repo
    git pull
    ```

3. 激活虚拟环境，并启动应用：

    ```bash  
    source venv/bin/activate
    export FLASK_APP=application.py
    flask run -h 0.0.0.0
    ```

## <a name="next-steps"></a>后续步骤

- 了解如何[开发适用于 Azure Stack](azure-stack-dev-start.md)。
- 了解如何[IaaS 作为 Azure Stack 的常见部署](azure-stack-dev-start-deploy-app.md)。
- 若要了解 Python 编程语言，并查找用于 Python 的其他资源，请参阅[Python.org](https://www.python.org)。
