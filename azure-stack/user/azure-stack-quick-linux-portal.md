---
title: 使用 Azure Stack 创建 Linux VM |Microsoft Docs
description: 使用 Azure Stack 创建 Linux 服务器 VM。
services: azure-stack
cloud: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.custom: mvc
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 5c3b8d85f5dca0eeb439ca475d4396848d316366
ms.sourcegitcommit: 0d27456332031ab98ba2277117395ae5ffcbb79f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73047263"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-stack-portal"></a>快速入门：使用 Azure Stack 门户创建 Linux 服务器 VM

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 Azure Stack 门户创建 Ubuntu Server 16.04 LTS 虚拟机（VM）。 本文介绍如何创建和使用虚拟机。 本文还介绍了如何执行以下操作：

* 使用远程客户端连接到 VM。
* 安装 NGINX web 服务器。
* 清理资源。

> [!NOTE]  
> 本文中的图像已更新，以匹配 Azure Stack 版本1808中引入的更改。 除非托管磁盘外，版本1808添加了对使用*托管磁盘*的支持。 如果使用的是早期版本，则某些任务（如磁盘选择）的图像不同于 UI 中显示的内容。  

## <a name="prerequisites"></a>必备组件

* Azure Stack Marketplace 中的 Linux 映像

   默认情况下，Azure Stack Marketplace 没有 Linux 映像。 让 Azure Stack 运算符提供所需的 Ubuntu Server 16.04 LTS 映像。 操作员可以使用将[Marketplace 项从 Azure 下载到 Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md)中的说明进行操作。

* 访问 SSH 客户端

   如果使用的是 Azure Stack 开发工具包（ASDK），则可能无法访问安全外壳（SSH）客户端。 如果需要客户端，则多个包包含 SSH 客户端。 例如，PuTTY 包括 SSH 客户端和 SSH 密钥生成器（puttygen）。 有关可用包的详细信息，请参阅[如何使用 SSH 公钥](azure-stack-dev-start-howto-ssh-public-key.md)。

* 此快速入门使用 PuTTY 生成 SSH 密钥并连接到 Linux 服务器 VM。 [下载并安装 PuTTY](https://www.putty.org)。

## <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对

若要完成本文中的所有步骤，需要一个 SSH 密钥对。 如果已有一个 SSH 密钥对，则可以跳过此步骤。

创建 SSH 密钥对：

1. 中转到 PuTTY 安装文件夹（默认位置是*C:\Program Files\PuTTY*），然后运行：

    `puttygen.exe`

1. 在 " **PuTTY 密钥生成器**" 窗口中，将 "**要生成的密钥类型**" 设置为 " **RSA**"，并将**生成的密钥中的位数**设置为**2048**。

   ![PuTTY 密钥生成器配置](media/azure-stack-quick-linux-portal/Putty01.PNG)

1. 然后选择“生成”。

1. 若要生成密钥，请在 "**密钥**" 框中，随机移动指针。

1. 密钥生成完成后，选择 "**保存公钥**"，然后选择 "**保存私钥**"，将密钥保存到文件。

   ![PuTTY 密钥生成器结果](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>登录到 Azure Stack 门户

Azure Stack 门户的地址取决于要连接到的 Azure Stack 产品：

* 对于 ASDK，请参阅 https://portal.local.azurestack.external 。

* 对于 Azure Stack 集成系统，请参阅 Azure Stack 操作员提供的 URL。

## <a name="create-the-vm"></a>创建 VM

1. 在 Azure Stack 门户的左上角，选择 "**创建资源**"。

1. 选择“计算”，然后选择“Ubuntu Server 16.04 LTS”。
   
   ![选择 Linux 服务器](media/azure-stack-quick-linux-portal/select.png)

1. 选择**创建**。

1. 键入 VM 信息。 对于 "**身份验证类型**"，请选择 " **ssh 公钥**"，粘贴保存的 ssh 公钥，然后选择 **"确定"** 。

   > [!NOTE]
   > 请确保删除键的所有前导或尾随空格。

   ![基本信息面板-配置 VM](media/azure-stack-quick-linux-portal/linux-01.PNG)

1. 为 VM 选择**D1** 。

   !["大小" 窗格-选择 VM 大小](media/azure-stack-quick-linux-portal/linux-02.PNG)

1. 在 "**设置**" 页上，对默认值进行更改。
   
   从 Azure Stack 版本1808开始，你可以配置**存储**并选择使用*托管磁盘*。 在早于1808的版本中，仅可使用非托管磁盘。

   ![为托管磁盘配置存储](media/azure-stack-quick-linux-portal/linux-03.PNG)
    
   配置准备就绪后，请选择 **"确定"** 继续。

1. 在 "**摘要**" 页上，选择 **"确定"** 以启动 VM 部署。  

   ![部署](media/azure-stack-quick-linux-portal/deploy.png)

## <a name="connect-to-the-vm"></a>连接到 VM

1. 在 VM 页面上选择 "**连接**"。 你可以找到连接到 VM 所需的 SSH 连接字符串。 

1. 在 " **PuTTY 配置**" 页上的 "**类别**" 窗格中，向下滚动并展开 " **SSH**"，然后选择 "**身份验证**"。 

   ![连接 VM](media/azure-stack-quick-linux-portal/putty03.PNG)

1. 选择 "**浏览**"，然后选择已保存的私钥文件。

1. 在 "**类别**" 窗格中，向上滚动到 "会话" 并选择 "**会话**"。

1. 在 "**主机名（或 IP 地址）** " 框中，粘贴 Azure Stack 门户中显示的连接字符串。 在此示例中，字符串 *asadmin@192.168.102.34* 。

1. 选择 "**打开**"，为 VM 打开会话。

   ![Linux 会话](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>安装 NGINX web 服务器

若要更新包源并在 VM 上安装最新的 NGINX 包，请输入以下 bash 命令：

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完成安装 NGINX 后，请关闭 SSH 会话，并在 Azure Stack 门户中打开 "VM**概述**" 页。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

网络安全组（NSG）保护入站和出站流量。 在 Azure Stack 门户中创建 VM 时，会在用于 SSH 连接的端口22上创建入站规则。 由于此 VM 托管 web 服务器，因此需要创建 NSG 规则以允许端口80上的 web 流量。

1. 在 "VM**概述**" 页上，选择**资源组**的名称。

1. 选择 VM 的**网络安全组**。 您可以通过使用 "**类型**" 列来标识 NSG。

1. 在左窗格中的 "**设置**" 下，选择 "**入站安全规则**"。

1. 选择 **添加** 。

1. 在 "**名称**" 框中，键入**http**。 

1. 请确保将 "**端口范围**" 设置为 "80"，并将 "**操作**" 设置为 "**允许**"。

1. 选择“确定”。

## <a name="view-the-welcome-to-nginx-page"></a>查看 "欢迎使用 nginx" 页

安装 NGINX 并在 VM 上打开端口80后，可以使用 VM 的公共 IP 地址访问 web 服务器。 （公共 IP 地址显示在 VM 的 "**概述**" 页上。）

打开 web 浏览器，并中转到*http://\<"公共 IP 地址" >* 。

![NGINX web 服务器欢迎页](media/azure-stack-quick-linux-portal/linux-05.PNG)

## <a name="clean-up-resources"></a>清理资源

清理不再需要的资源。 要删除 VM 及其资源，请在 "VM" 页上选择资源组，然后选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

在本快速入门中，已部署了包含 web 服务器的基本 Linux 服务器虚拟机。 若要详细了解 Azure Stack Vm，请继续参阅[Azure Stack 中 vm 的注意事项](azure-stack-vm-considerations.md)。
