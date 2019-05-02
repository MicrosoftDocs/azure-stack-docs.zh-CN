---
title: 如何与 Azure Stack 配合使用 SSH 公钥 |Microsoft Docs
description: 如何使用 SSH 公钥
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 0f4133052022963e1ed0457dd479a00bcc5bb749
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490063"
---
# <a name="how-to-use-an-ssh-public-key"></a>如何使用 SSH 公钥

您可能需要创建的 SSH 公钥和私钥对来托管 web 应用的 Azure Stack 中使用从您的开发计算机和服务器 VM 上打开的 SSH 连接。 这篇文章将指导你完成获取你的密钥和使用密钥以连接到服务器的步骤。 可以使用 SSH 客户端以在 Linux 服务器上，启动 bash 提示符或将文件移到和从服务器的 SFTP 客户端。

## <a name="create-an-ssh-public-key-on-windows"></a>在 Windows 上创建 SSH 公钥

在本部分中，您将使用的 PuTTY 密钥生成器来创建 SSH 公钥和私钥对在 Azure Stack 上创建安全连接到 Linux 计算机时要使用。 PuTTY SSH 和 Telnet 的 Windows 和 Unix 平台的免费实现是连同`xterm`终端仿真程序。

1. [下载并安装 PuTTY 适用于你的计算机。](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. 打开 PuTTY 密钥生成器。

1. 设置**参数**到**RSA**。

1. 在对生成的密钥中设置的位数`2048`。  

    ![使用 PuTTY 生成 SSH 公钥](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. 然后选择“生成”。 在中**密钥**区域中，通过将光标移动的空白区域来生成某些随机性。

1. 添加**密钥通行短语**并确认它在**确认**框。 记下你的密码。
    ![使用 PuTTY 生成 SSH 公钥](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. 选择**将公钥保存在**并将保存到可以访问的位置。

1. 选择**保存私钥**并将保存到的位置，您可以访问它，并且请记住它所属的公钥。

你的公钥存储在您保存的文本文件。 如果您打开它，它将包含如下所示的文本：

```text  
---- BEGIN SSH2 PUBLIC KEY ----
Comment: "rsa-key-20190330"
THISISANEXAMPLEDONOTUSE AAAAB3NzaC1yc2EAAAABJQAAAQEAthW2CinpqhXq
9uSa8/lSH7tLelMXnFljSrJIcpxp3MlHlYVbjHHoKfpvQek8DwKdOUcFIEzuStfT
Z8eUI1s5ZXkACudML68qQT8R0cmcFBGNY20K9ZMz/kZkCEbN80DJ+UnWgjdXKLvD
Dwl9aQwNc7W/WCuZtWPazee95PzAShPefGZ87Jp0OCxKaGYZ7UXMrCethwfVumvU
aj+aPsSThXncgVQUhSf/1IoRtnGOiZoktVvt0TIlhxDrHKHU/aZueaFXYqpxDLIs
BvpmONCSR3YnyUtgWV27N6zC7U1OBdmv7TN6M7g01uOYQKI/GQ==
---- END SSH2 PUBLIC KEY ----
```

时使用的公钥，您复制并粘贴为值的文本框的整个上下文，当应用程序要求提供密钥。

<!-- 
## Create an SSH public key on Linux

ToDo: I need to write this section.

-->
## <a name="connect-with-ssh-using-putty"></a>使用 SSH 使用 PuTTY 进行连接

如果已安装 PuTTY，可以密钥生成器以及 SSH 客户端。 打开 SSH 客户端，PuTTY、 配置你的连接值和 SSH 密钥，和如果要在 Azure Stack 与同一网络上，连接到 VM。

在连接之前，你将需要：
- PuTTY
- IP 地址和使用 SSH 公共密钥作为身份验证类型在 Azure Stack 中的 Linux 计算机的用户名。
- 需要为机打开端口 22。
- 创建计算机时使用的公共 SSH 密钥。
- 客户端计算机运行 Azure Stack 与位于同一网络上的 PuTTY。

### <a name="connect-via-ssh-with-putty"></a>通过使用 PuTTy SSH 连接

1. 打开 Putty。

    ![使用 PuTTY 连接](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. 添加用户名和计算机的公共 IP 地址。 例如，`username@192.XXX.XXX.XX`有关**主机名**。 
3. 验证**端口**`22`设置以及**连接类型**设置为`SSH`。
4. 展开**SSH** > **身份验证**中**类别**树。

    ![SSH 私钥](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. 选择**浏览**和查找私钥文件 (filename.ppk) 的公共和私有密钥对。
6. 在类别树中选择会话。

    ![SSH 公钥与私钥](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. 键入在会话的名称**保存的会话**，然后选择**保存**。
8. 选择会话的名称和**负载**。
9. 选择“打开”。 将打开 SSH 会话。

## <a name="connect-with-sftp-with-filezilla"></a>使用 FileZilla 与 SFTP 连接

Filezilla 可以用作支持 SFTP，若要将文件移到和从 Linux 计算机的 FTP 客户端。 FileZilla 在 Windows 10、 Linux 和 macOS 上运行。 FileZilla 客户端支持通过 TLS (FTPS) 和 SFTP FTP，而且还 FTP。 它是分布式的开放源代码软件免费的 GNU 通用公共许可证的条款下。

### <a name="set-your-connection"></a>设置你的连接

1. [下载并安装 FileZilla](https://filezilla-project.org/download.php)。
1. 打开 FileZilla。
1. 选择**文件** > **站点管理器**。

    ![SSH 公钥与私钥](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. 选择**SFTP-SSH 文件传输协议**有关**协议**。
1. 添加您的计算机中的公共 IP 地址**主机**框。
1. 选择**正常**有关**登录类型**。
1. 添加你的用户名和密码。
1. 选择“确定”。
1. 选择**编辑** > **设置**。

    ![SSH 公钥与私钥](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. 展开**连接**中**选择页**树。 选择**SFTP**。
1. 选择**添加密钥文件**并添加私钥文件，如 filename.ppk。
1. 选择“确定”。

### <a name="open-your-connection"></a>打开你的连接

1. 打开 FileZilla。
1. 选择**文件** > **站点管理器**。
1. 选择你的站点的名称，然后选择**Connect**。

## <a name="next-steps"></a>后续步骤

了解有关如何对[开发适用于 Azure Stack](azure-stack-dev-start.md)