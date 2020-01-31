---
title: 如何将 SSH 公钥与 Azure Stack 中心一起使用
description: 如何使用 SSH 公钥
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: c9a37dab12f3a96e2f44aa9ccb92eaea295a0d0b
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883801"
---
# <a name="use-an-ssh-public-key"></a>使用 SSH 公钥

若要在托管 web 应用的 Azure Stack 中心实例中使用从开发计算机到服务器 VM 的开放 SSH 连接，你可能需要创建一个安全外壳（SSH）公钥和私钥对。 

本文介绍如何创建密钥，并使用它们连接到服务器。 您可以使用 SSH 客户端在 Linux 服务器上获取 bash 提示，或者使用安全 FTP （SFTP）客户端将文件移入和移出服务器。

## <a name="create-an-ssh-public-key-on-windows"></a>在 Windows 上创建 SSH 公钥

在本部分中，将使用 PuTTY 密钥生成器创建一个公共 SSH 密钥和私钥对，以便在与 Azure Stack 中心实例中的 Linux 计算机建立安全连接时使用。 PuTTY 是一个免费终端模拟器，可让你通过 SSH 和 Telnet 连接到服务器。

1. [为计算机下载并安装 PuTTY。](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. 打开 PuTTY 密钥生成器。

    ![带有空白键的 PuTTY 密钥生成器](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. 在 "**参数**" 下，选择 " **RSA**"。

1. 在 "**生成的密钥**" 框中，输入**2048**。  

1. 然后选择“生成”。

1. 在 "**密钥**" 区域中，通过将光标移动到空白区域，生成一些随机字符。

    ![带有填充的密钥框的 PuTTY 密钥生成器](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. 输入**密钥密码**，并在 "**确认密码**" 框中确认该密码。 记下通行短语供以后使用。

1. 选择 "**保存公钥**"，并将其保存到可以访问的位置。

1. 选择 "**保存私钥**"，并将其保存到可以访问的位置。 请记住，它属于公钥。

公钥存储在保存的文本文件中。 文本如下所示：

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

当应用程序请求该密钥时，将复制并粘贴该文本文件的全部内容。

## <a name="connect-with-ssh-by-using-putty"></a>使用 PuTTY 连接 SSH

安装 PuTTY 时，可以使用 PuTTY 密钥生成器和 SSH 客户端。 在本部分中，将打开 SSH 客户端 PuTTY，并配置连接值和 SSH 密钥。 如果与 Azure Stack 中心实例位于同一网络中，请连接到 VM。

在连接之前，你将需要：
- PuTTY
- Azure Stack 中心实例中 Linux 计算机的 IP 地址和用户名，其中使用 SSH 公钥作为身份验证类型。
- 为计算机打开端口22。
- 创建计算机时使用的公共 SSH 密钥。
- 运行 PuTTY 的客户端计算机与 Azure Stack 中心实例位于同一网络上。

1. 打开 PuTTY。

    ![PuTTY 配置窗格](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. 在 "**主机名（或 IP 地址）** " 框中，输入计算机的用户名和公共 IP 地址（例如 **username@192.XXX.XXX.XX** ）。 
3. 验证**端口**是否为**22**并且**连接类型**为 " **SSH**"。
4. 在**类别**树中，展开 " **SSH**和**身份验证**"。

    ![PuTTY 配置窗格-SSH 私钥](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. 在 "**身份验证的私钥文件**" 框旁，选择 "**浏览**"，然后搜索公钥和私钥对的私钥文件（ *\<filename > .ppk*）。
6. 在**类别**树中，选择 "**会话**"。

    ![PuTTY 配置窗格 "保存的会话" 框](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. 在 "**已保存的会话**" 下，输入会话的名称，然后选择 "**保存**"。
8. 在 "**保存的会话**" 列表中，选择会话的名称，然后选择 "**加载**"。
9. 选择“打开”。 SSH 会话将打开。

## <a name="connect-with-sftp-with-filezilla"></a>通过 FileZilla 与 SFTP 连接

若要将文件移入和移出 Linux 计算机，可以使用 FileZilla，它是支持安全 FTP （SFTP）的 FTP 客户端。 FileZilla 在 Windows 10、Linux 和 macOS 上运行。 FileZilla 客户端支持 FTP、FTP over TLS （FTPS）和 SFTP。 它是在 GNU 通用公共许可证条款下免费分发的开源软件。

### <a name="set-your-connection"></a>设置连接

1. [下载并安装 FileZilla](https://filezilla-project.org/download.php)。
1. 打开 FileZilla。
1. 选择 "**文件** > **站点管理器**"。

    !["FileZilla 站点管理器" 窗格](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. 在 "**协议**" 下拉列表中，选择 " **SFTP-SSH 文件传输协议**"。
1. 在 "**主机**" 框中，输入计算机的公共 IP 地址。
1. 在 "**登录类型**" 框中，选择 "**常规**"。
1. 输入用户名和密码。
1. 选择“确定”。
1. 选择 "**编辑** > **设置**"。

    ![FileZilla 设置窗格](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. 在 "**选择页**树" 中，展开 "**连接**"，然后选择 " **SFTP**"。
1. 选择 "**添加密钥文件**"，然后输入私钥文件（例如 *\<文件名 > .ppk*）。
1. 选择“确定”。

### <a name="open-your-connection"></a>打开连接

1. 打开 FileZilla。
1. 选择 "**文件** > **站点管理器**"。
1. 选择网站的名称，然后选择 "**连接**"。

## <a name="next-steps"></a>后续步骤

了解如何[在 Azure Stack Hub 中设置开发环境](azure-stack-dev-start.md)。
