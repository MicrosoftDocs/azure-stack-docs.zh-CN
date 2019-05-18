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
ms.openlocfilehash: c0b55579c5103c7bb1073546243dbfcc0b700b4a
ms.sourcegitcommit: 05a16552569fae342896b6300514c656c1df3c4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65838393"
---
# <a name="use-an-ssh-public-key"></a>使用 SSH 公钥

若要使用 Azure Stack 实例的服务器 VM 的打开 SSH 连接从开发计算机承载你的 web 应用，你可能需要创建安全外壳 (SSH) 公钥和私钥对。 

在本文中，创建你的密钥，然后将其用于连接到服务器。 可以使用 SSH 客户端要获取 Linux 服务器上的 bash 提示符或 Secure FTP (SFTP) 客户端用于将文件移到和从服务器。

## <a name="create-an-ssh-public-key-on-windows"></a>在 Windows 上创建 SSH 公钥

在本部分中，使用 PuTTY 密钥生成器来创建 SSH 公钥和私钥对要在 Azure Stack 实例中创建安全连接到 Linux 计算机时使用。 PuTTY 是一个免费的终端仿真程序，可允许你连接到通过 SSH 和 Telnet 服务器。

1. [下载并安装 PuTTY 适用于你的计算机。](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. 打开 PuTTY 密钥生成器。

    ![使用空白的密钥框的 puTTY 密钥生成器](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. 下**参数**，选择**RSA**。

1. 在中**中生成的密钥的位数**框中，输入**2048年**。  

1. 然后选择“生成”。

1. 在中**密钥**区域中，将光标移动的空白区域来生成某些随机字符。

    ![有一个填充的密钥框的 puTTY 密钥生成器](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. 输入**密钥通行短语**并确认它在**确认通行短语**框。 请注意你的密码以供将来使用。

1. 选择**保存公钥**，并将其保存到可以访问的位置。

1. 选择**保存私钥**，并将其保存到可以访问的位置。 请记住它所属的公钥。

你的公钥存储在您保存的文本文件。 文本看起来如下所示：

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

当应用程序请求密钥时，则复制并粘贴文本文件的全部内容。

## <a name="connect-with-ssh-by-using-putty"></a>使用 PuTTY 通过 ssh 连接

在安装 PuTTY，可以 PuTTY 密钥生成器以及 SSH 客户端。 在本部分中，您可以打开 SSH 客户端，PuTTY，并配置你的连接值和 SSH 密钥。 如果要将 Azure Stack 实例所在的同一网络上，则连接到 VM。

在连接之前，你将需要：
- PuTTY
- IP 地址和使用 SSH 公钥作为身份验证类型在 Azure Stack 实例中的 Linux 计算机的用户名。
- 端口 22，若要执行在计算机处于打开状态。
- 创建计算机时使用的公共 SSH 密钥。
- 运行 PuTTY 是作为 Azure Stack 实例在同一网络上的客户端计算机。

1. 打开 PuTTY。

    ![在 PuTTY 配置窗格](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. 在中**主机名 （或 IP 地址）** 框中，输入用户名和计算机的公共 IP 地址 (例如， **username@192.XXX.XXX.XX**)。 
3. 验证**端口**是**22**并**连接类型**是**SSH**。
4. 在中**类别**树中，展开**SSH**并**身份验证**。

    ![PuTTY 配置窗格中的 SSH 私钥](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. 下一步**进行身份验证的私钥文件**框中，选择**浏览**，，然后搜索私钥文件 (*\<文件名 >.ppk*) 的公用和私有密钥对。
6. 在中**类别**树中，选择**会话**。

    ![PuTTY 配置窗格中"保存的会话"框](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. 下**保存的会话**，为会话中，输入一个名称，然后选择**保存**。
8. 在中**保存的会话**列表中，选择你的会话的名称，然后选择**负载**。
9. 选择“打开”。 SSH 会话中打开。

## <a name="connect-with-sftp-with-filezilla"></a>使用 FileZilla 与 SFTP 连接

若要将文件移到和从 Linux 计算机，可以使用 FileZilla，这是支持安全 FTP (SFTP) 的 FTP 客户端。 FileZilla 在 Windows 10、 Linux 和 macOS 上运行。 FileZilla 客户端支持 FTP，FTP over TLS (FTPS) 和 SFTP。 它是开放源代码软件分发的 GNU 通用公共许可条款下的免费。

### <a name="set-your-connection"></a>设置你的连接

1. [下载并安装 FileZilla](https://filezilla-project.org/download.php)。
1. 打开 FileZilla。
1. 选择**文件** > **站点管理器**。

    ![FileZilla 站点管理器窗格](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. 在中**协议**下拉列表中，选择**SFTP-SSH 文件传输协议**。
1. 在中**主机**中，输入你的计算机的公共 IP 地址。
1. 在中**登录类型**框中，选择**正常**。
1. 输入用户名和密码。
1. 选择“确定”。
1. 选择**编辑** > **设置**。

    ![FileZilla 设置窗格](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. 在中**页上选择**树中，展开**连接**，然后选择**SFTP**。
1. 选择**添加密钥文件**，然后输入私钥文件 (例如， *\<文件名 >.ppk*)。
1. 选择“确定”。

### <a name="open-your-connection"></a>打开你的连接

1. 打开 FileZilla。
1. 选择**文件** > **站点管理器**。
1. 选择你的站点的名称，然后选择**Connect**。

## <a name="next-steps"></a>后续步骤

了解如何[设置 Azure Stack 中的开发环境](azure-stack-dev-start.md)。
