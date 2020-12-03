---
title: 如何在 Azure Stack Hub 中使用 SSH 公钥
description: 如何使用 SSH 公钥
author: mattbriggs
ms.topic: overview
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 20e8afdae36f7a182f20aff1f4162d36378ca313
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525449"
---
# <a name="use-an-ssh-public-key"></a>使用 SSH 公钥

若要使用从开发计算机到 Azure Stack Hub 实例中服务器 VM（用于托管 Web 应用）的开放 SSH 连接，可能需要创建安全外壳 (SSH) 公钥和私钥对。 

在本文中，你将创建密钥，然后使用它们连接到服务器。 可以使用 SSH 客户端在 Linux 服务器上获取 bash 提示符，或使用安全 FTP (SFTP) 客户端将文件移入和移出服务器。

## <a name="create-an-ssh-public-key-on-windows"></a>在 Windows 上创建 SSH 公钥

在本部分中，你将使用 PuTTY 密钥生成器创建 SSH 公钥和私钥对，以便在 Azure Stack Hub 实例中与 Linux 计算机建立安全连接时使用。 PuTTY 是免费的终端仿真器，可用于通过 SSH 和 Telnet 连接到服务器。

1. [下载并安装适用于你的计算机的 PuTTY。](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. 打开 PuTTY 密钥生成器。

    ![密钥框为空白的 PuTTY 密钥生成器](media/azure-stack-dev-start-howto-ssh-public-key/001-putty-key-gen-start.png)

1. 在“参数”下选择“RSA”。  

1. 在“已生成密钥中的位数”中，输入 **2048**。  

1. 然后选择“生成”  。

1. 在“密钥”区域中，将光标移到空白区域上以生成一些随机字符。 

    ![已填充密钥框的 PuTTY 密钥生成器](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-key-gen-result.png)

1. 输入一个 **密钥通行短语**，并在“确认通行短语”框中确认。  请记下该通行短语供稍后使用。

1. 选择“保存公钥”，并将公钥保存到可访问的位置。 

1. 选择“保存私钥”，并将私钥保存到可访问的位置。  请记住它属于公钥。

公钥已存储在保存的文本文件中。 文本如下所示：

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

当应用程序请求密钥时，你将复制并粘贴文本文件的整个内容。

## <a name="connect-with-ssh-by-using-putty"></a>使用 PuTTY 通过 SSH 进行连接

安装 PuTTY 时，你已获得 PuTTY 密钥生成器和 SSH 客户端。 在本部分，你将打开 SSH 客户端、PuTTY，并配置连接值和 SSH 密钥。 如果你在 Azure Stack Hub 实例所在的同一网络中操作，请连接到 VM。

连接之前，需要：
- PuTTY
- Azure Stack Hub 实例中使用 SSH 公钥作为身份验证类型的 Linux 计算机的 IP 地址和用户名。
- 为计算机打开端口 22。
- 创建该计算机时使用的 SSH 公钥。
- 运行 PuTTY 的客户端计算机与 Azure Stack Hub 实例位于同一网络上。

1. 打开 PuTTY。

    ![PuTTY 配置窗格](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-connect.png)

2. 在“主机名(或 IP 地址)”框中，输入用户名和计算机的公共 IP 地址（例如 **username@192.XXX.XXX.XX** ）。 
3. 检查“端口”是否为“22”，“连接类型”是否为“SSH”。    
4. 在“类别”树中，展开“SSH”和“身份验证”。   

    ![PuTTY 配置窗格 - SSH 私钥](media/azure-stack-dev-start-howto-ssh-public-key/002-putty-set-private-key.png)

5. 在 "**身份验证的私钥文件**" 框旁边，选择 "**浏览**"，然后搜索公钥文件 (公钥和私钥对的 *\<filename> .ppk*) 。
6. 在“类别”树中选择“会话”。  

    ![PuTTY 配置窗格 -“保存的会话”框](media/azure-stack-dev-start-howto-ssh-public-key/003-puTTY-save-session.png)

7. 在“保存的会话”下输入会话的名称，然后选择“保存”。  
8. 在“保存的会话”列表中选择会话名称，然后选择“加载”。  
9. 选择“打开”  。 此时会打开 SSH 会话。

## <a name="connect-with-sftp-with-filezilla"></a>使用 FileZilla 通过 SFTP 进行连接

若要在 Linux 计算机中移入和移出文件，可以使用 FileZilla，它是支持安全 FTP (SFTP) 的 FTP 客户端。 FileZilla 可在 Windows 10、Linux 和 macOS 上运行。 FileZilla 客户端支持 FTP、基于 TLS 的 FTP (FTPS) 和 SFTP。 它是根据 GNU 常规公共许可条款免费分发的开源软件。

### <a name="set-your-connection"></a>设置连接

1. [下载并安装 FileZilla](https://filezilla-project.org/download.php)。
1. 打开 FileZilla。
1. 选择“文件” > “站点管理器”。 

    ![FileZilla 站点管理器窗格](media/azure-stack-dev-start-howto-ssh-public-key/005-filezilla-file-manager.png)

1. 在“协议”下拉列表中，选择“SFTP - SSH 文件传输协议”。  
1. 在“主机”框中，输入计算机的公共 IP 地址。 
1. 在“登录类型”框中，选择“正常”。  
1. 输入用户名和密码。
1. 选择“确定”  。
1. 选择“编辑” > “设置”。 

    ![FileZilla 设置窗格](media/azure-stack-dev-start-howto-ssh-public-key/006-filezilla-add-private-key.png)

1. 在“选择页”树中展开“连接”，然后选择“SFTP”。   
1. 选择 "**添加密钥文件**"，然后输入私钥文件 (例如 *\<filename> .ppk*) 。
1. 选择“确定”  。

### <a name="open-your-connection"></a>打开连接

1. 打开 FileZilla。
1. 选择“文件” > “站点管理器”。 
1. 选择站点名称，然后选择“连接”。 

## <a name="next-steps"></a>后续步骤

了解如何[在 Azure Stack Hub 中设置开发环境](azure-stack-dev-start.md)。
