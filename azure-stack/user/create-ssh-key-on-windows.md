---
title: 将 Kubernetes 群集部署到 Azure Stack 集线器上的自定义虚拟网络
description: 了解如何将 Kubernetes 群集部署到 Azure Stack 集线器上的自定义虚拟网络。
author: mattbriggs
ms.topic: article
ms.date: 2/28/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 2/28/2020
ms.openlocfilehash: ff66cdaffd4ffc7a6896edf0191871669ac84b16
ms.sourcegitcommit: 4301e8dee16b4db32b392f5979dfec01ab6566c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79318022"
---
# <a name="create-an-ssh-key-for-linux-on-azure-stack-hub"></a>在 Azure Stack 集线器上创建适用于 Linux 的 SSH 密钥

可以在 windows 计算机上为 Linux 计算机创建 SSH （安全外壳）密钥。 使用本文中的步骤生成的公钥，通过 Vm 进行 SSH 身份验证。 如果你使用的是 windows 计算机，请在 Windows 上安装 Ubuntu，以使用 bash、ssh、git 和 Windows 上的 Ubuntu 来获取终端。 运行**ssh-keygen** ，创建密钥。

## <a name="open-bash-on-windows"></a>在 Windows 上打开 bash

1. 如果计算机上没有安装适用于 Linux 的 Windows 子系统，请[在 Windows 上安装 Ubuntu](https://www.microsoft.com/en-us/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)。  
    有关使用适用于 Linux 的 Windows 子系统的详细信息，请参阅[适用于 linux 的 Windows 子系统文档](https://docs.microsoft.com/windows/wsl/about)。

2. 在工具栏中键入**Ubuntu** ，然后选择 "**打开**"。

## <a name="create-a-key-with-ssh-keygen"></a>使用 ssh 创建密钥 ssh-keygen

1. 在 bash 提示符下键入以下命令：

    ```bash  
    ssh-keygen -t rsa
    ```

    Bash 显示以下提示：

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/username/.ssh/id_rsa):
    ```

2. 键入文件名和密码。 再次键入通行短语。

    Bash 显示以下内容：

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/user/.ssh/id_rsa): key.txt
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in key.txt.
    Your public key has been saved in key.txt.pub.
    The key fingerprint is:
    SHA256:xanotrealoN6z1/KChqeah0CYVeyhL50/0rq37qgy6Ik username@machine
    The key's randomart image is:
    +---[RSA 2048]----+
    |   o.     .      |
    |  . o.   +       |
    | + o .+ o o      |
    |o o .  O +       |
    | . o .o S .      |
    |  o +. .         |
    |.  o +..o. .     |
    |= . ooB +o+ .    |
    |E=..*X=*.. +.    |
    +----[SHA256]-----+
    ```

3. 查看和公共 ssh 密钥：

    ```bash
    cat /home/<username>/<filename>
    ```

    Bash 显示如下所示的内容：

    ```bash
    ssh-rsa AAAAB3NzaC1ycTHISISANEXAMPLEDITqEJRNrf6tXy9c0vKnMhiol1BFzHFV3
    +suXk6NDeFcA9uI58VdD/CuvG826R+3OPnXutDdl2MLyH3DGG1fJAHObUWQxmDWluhSGb
    JMHiw2L9Wnf9klG6+qWLuZgjB3TQdus8sZI8YdB4EOIuftpMQ1zkAJRAilY0p4QxHhKbU
    IkvWqBNR+rd5FcQx33apIrB4LMkjd+RpDKOTuSL2qIM2+szhdL5Vp5Y6Z1Ut1EpOrkbg1
    cVw7oW0eP3ROPdyNqnbi9m1UVzB99aoNXaepmYviwJGMzXsTkiMmi8Qq+F8/qy7i4Jxl0
    aignia880qOtQrvNEvyhgZOM5oDhgE3IJ username@machine
    ```

4. 将 `ssh-rsa [...]` 的文本复制到 `username@machinename`。 请确保文本不包含任何回车符。 使用 AKS 引擎创建 VM 或 Kubernetes 群集时，可以使用此文本。

5. 如果使用的是 Windows 计算机，可以使用 **\\\\wsl $** 来访问 Linux 文件。

    1. 在工具栏中键入 `\\wsl$`。 你的分发将打开的默认窗口。

    2. 导航到： `\\wsl$\Ubuntu\home\<username>` 并找到公钥和私钥，并将其保存到安全位置。

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack 集线器上使用 AKS 引擎部署 Kubernetes 群集](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
- [快速入门：使用 Azure Stack 中心门户创建 Linux 服务器 VM](azure-stack-quick-linux-portal.md)