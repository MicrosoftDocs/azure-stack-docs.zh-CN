---
title: 在 Azure Stack Hub 上为 Linux 创建 SSH 密钥
description: 了解如何在 Azure Stack 集线器上创建适用于 Linux 的 SSH 密钥
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 4d85a543484fb7dfd33e6fb6c412198a4e646345
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525942"
---
# <a name="create-an-ssh-key-for-linux-on-azure-stack-hub"></a>在 Azure Stack Hub 上为 Linux 创建 SSH 密钥

可以在 Windows 计算机上为 Linux 计算机创建 SSH（安全外壳）密钥。 使用本文中步骤生成的公钥对 VM 进行 SSH 身份验证。 如果你使用的是 Windows 计算机，请在 Windows 上安装 Ubuntu，以便通过 bash、ssh、git、apt 等实用工具获取终端。 运行 **ssh-keygen** 来创建密钥。

## <a name="open-bash-on-windows"></a>在 Windows 上打开 Bash

1. 如果你没有在计算机上安装适用于 Linux 的 Windows 子系统，请[在 Windows 上安装 Ubuntu](https://www.microsoft.com/en-us/p/ubuntu/9nblggh4msv6?activetab=pivot:overviewtab)。  
    有关使用适用于 Linux 的 Windows 子系统的详细信息，请参阅[适用于 Linux 的 Windows 子系统的文档](/windows/wsl/about)。

2. 在工具栏中键入 **Ubuntu**，然后选择“打开”。

## <a name="create-a-key-with-ssh-keygen"></a>使用 ssh-keygen 创建密钥

1. 在 bash 提示符下键入以下命令：

    ```bash  
    ssh-keygen -t rsa
    ```

    Bash 将显示以下提示：

    ```bash
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/username/.ssh/id_rsa):
    ```

2. 键入文件名和通行短语。 再次键入通行短语。

    Bash 将显示以下提示：

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

3. 查看公共 ssh 密钥：

    ```bash
    cat /home/<username>/<filename>
    ```

    Bash 会显示如下所示的内容：

    ```bash
    ssh-rsa AAAAB3NzaC1ycTHISISANEXAMPLEDITqEJRNrf6tXy9c0vKnMhiol1BFzHFV3
    +suXk6NDeFcA9uI58VdD/CuvG826R+3OPnXutDdl2MLyH3DGG1fJAHObUWQxmDWluhSGb
    JMHiw2L9Wnf9klG6+qWLuZgjB3TQdus8sZI8YdB4EOIuftpMQ1zkAJRAilY0p4QxHhKbU
    IkvWqBNR+rd5FcQx33apIrB4LMkjd+RpDKOTuSL2qIM2+szhdL5Vp5Y6Z1Ut1EpOrkbg1
    cVw7oW0eP3ROPdyNqnbi9m1UVzB99aoNXaepmYviwJGMzXsTkiMmi8Qq+F8/qy7i4Jxl0
    aignia880qOtQrvNEvyhgZOM5oDhgE3IJ username@machine
    ```

4. 复制文本 `ssh-rsa [...]`，一直复制到 `username@machinename`。 请确保文本不包含任何回车符。 使用 AKS 引擎创建 VM 或 Kubernetes 群集时，可以使用此文本。

5. 如果你使用的是 Windows 计算机，可以使用 **\\\\wsl$** 来访问 Linux 文件。

    1. 在工具栏中键入 `\\wsl$`。 你的发行版的默认窗口随即打开。

    2. 导航到：`\\wsl$\Ubuntu\home\<username>`，找到公钥和私钥，并将其保存到一个安全位置。

## <a name="next-steps"></a>后续步骤

- [使用 Azure Stack Hub 上的 AKS 引擎部署 Kubernetes 群集](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
- [快速入门：使用 Azure Stack Hub 门户创建 Linux 服务器 VM](azure-stack-quick-linux-portal.md)
