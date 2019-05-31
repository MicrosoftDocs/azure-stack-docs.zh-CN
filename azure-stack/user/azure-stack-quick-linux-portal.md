---
title: 通过使用 Azure Stack 中创建 VM 的 Linux 服务器 |Microsoft Docs
description: 通过使用 Azure Stack 中创建的 Linux 服务器 VM。
services: azure-stack
cloud: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.custom: mvc
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: ce06ffbe48848a30de98025c42711e25ca9a312a
ms.sourcegitcommit: 2ee75ded704e8cfb900d9ac302d269c54a5dd9a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394444"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-stack-portal"></a>快速入门：使用 Azure Stack 门户创建 VM 的 Linux 服务器

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 Azure Stack 门户来创建 Ubuntu Server 16.04 LTS 虚拟机 (VM)。 在本文中，将创建和使用虚拟机。 本文还演示了如何为：

* 通过远程客户端连接到 VM。
* 安装 NGINX Web 服务器。
* 清理资源。

> [!NOTE]  
> 这篇文章中的映像更新以匹配 1808年的 Azure Stack 版本中引入的更改。 版本 1808年添加了对使用支持*托管磁盘*除了非托管磁盘。 如果使用早期版本，对于某些任务，例如磁盘选择映像不同于在 UI 中显示的内容。  

## <a name="prerequisites"></a>必备组件

* Azure Stack Marketplace 中的 Linux 映像

   Azure Stack Marketplace，默认情况下，不会有 Linux 映像。 具有 Azure Stack 操作员提供所需的 Ubuntu Server 16.04 LTS 映像。 操作员可以使用中的说明[下载 Marketplace 项从 Azure 到 Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md)。

* SSH 客户端访问

   如果使用的 Azure Stack 开发工具包 (ASDK)，您可能没有访问安全外壳 (SSH) 客户端。 如果你需要一个客户端，多个包包含 SSH 客户端。 例如，PuTTY 包括 SSH 客户端和 SSH 密钥生成器 (puttygen.exe)。 有关可用的包的详细信息，请参阅[如何使用 SSH 公钥](azure-stack-dev-start-howto-ssh-public-key.md)。

* 本快速入门使用 PuTTY 生成 SSH 密钥并将连接到 Linux 服务器 VM。 [下载并安装 PuTTY](https://www.putty.org)。

## <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对

若要完成本文中的所有步骤后，您需要的 SSH 密钥对。 如果已有一个 SSH 密钥对，则可以跳过此步骤。

若要创建的 SSH 密钥对：

1. 转到 PuTTY 安装文件夹 (默认位置是*C:\Program Files\PuTTY*) 并运行：

    `puttygen.exe`

1. 在中**PuTTY 密钥生成器**窗口中，将**要生成的密钥类型**到**RSA**，并设置**中生成的密钥的位数**到**2048年**。

   ![PuTTY 密钥生成器配置](media/azure-stack-quick-linux-portal/Putty01.PNG)

1. 然后选择“生成”  。

1. 若要在生成密钥，**密钥**框中，将指针移动到随机。

1. 完成密钥生成后，选择**保存公钥**，然后选择**保存私钥**将你的密钥保存到文件。

   ![PuTTY 密钥生成器结果](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>登录到 Azure Stack 门户

Azure Stack 门户的地址取决于要连接到的 Azure Stack 产品：

* 对于 ASDK 中，请转到 https://portal.local.azurestack.external。

* 对于 Azure Stack 集成系统，请转到 Azure Stack 运营商提供的 URL。

## <a name="create-the-vm"></a>创建 VM

1. 在 Azure Stack 门户的左上角，选择**创建资源**。

1. 选择“计算”，然后选择“Ubuntu Server 16.04 LTS”。  
   
   ![选择 Linux 服务器](media/azure-stack-quick-linux-portal/select.png)

1. 选择“创建”  。

1. 键入 VM 的信息。 有关**身份验证类型**，选择**SSH 公钥**，粘贴 SSH 公共密钥您保存，并选择**确定**。

   > [!NOTE]
   > 请确保删除密钥的任何前导或尾随空格。

   ![基础知识面板-配置 VM](media/azure-stack-quick-linux-portal/linux-01.PNG)

1. 选择**D1** vm。

   ![Size 窗格-选择 VM 大小](media/azure-stack-quick-linux-portal/linux-02.PNG)

1. 上**设置**页上，为默认设置进行更改。
   
   从 Azure Stack 版本 1808年，你可以配置**存储**，然后选择要使用*托管磁盘*。 在比 1808年的早期版本，可以使用仅非托管的磁盘。

   ![配置适用于托管磁盘存储](media/azure-stack-quick-linux-portal/linux-03.PNG)
    
   配置准备就绪后，选择“确定”  以继续。

1. 上**摘要**页上，选择**确定**以启动 VM 部署。  

   ![部署](media/azure-stack-quick-linux-portal/deploy.png)

## <a name="connect-to-the-vm"></a>连接到 VM

1. 选择**Connect**的 VM 上。 您可以找到需要连接到 VM 的 SSH 连接字符串。 

1. 上**PuTTY 配置**页上，在**类别**窗格中，向下滚动并展开**SSH**，然后选择**身份验证**。 

   ![连接 VM](media/azure-stack-quick-linux-portal/putty03.PNG)

1. 选择**浏览**，然后选择保存私钥文件。

1. 在中**类别**窗格中，向上滚动到并选择**会话**。

1. 在中**主机名 （或 IP 地址）** 框中，粘贴 Azure Stack 门户中显示的连接字符串。 在此示例中，该字符串是 *asadmin@192.168.102.34* 。

1. 选择**打开**vm 打开会话。

   ![Linux 会话](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>安装 NGINX Web 服务器

若要更新包源并在 VM 上安装最新的 NGINX 包，请输入以下 bash 命令：

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

当您完成安装 NGINX 时，请关闭 SSH 会话并打开 VM**概述**Azure Stack 门户中的页。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

网络安全组 (NSG) 保护入站和出站流量。 在 Azure Stack 门户中创建 VM，进行 SSH 连接端口 22 上创建入站的规则。 由于此 VM 托管的 web 服务器，需要创建以允许端口 80 上的 web 流量 NSG 规则。

1. 在 VM 上**概述**页上，选择的名称**资源组**。

1. 选择**网络安全组**vm。 可以通过使用标识 NSG**类型**列。

1. 在左窗格中下,**设置**，选择**入站安全规则**。

1. 选择 **添加** 。

1. 在中**名称**框中，键入**http**。 

1. 请确保**端口范围**设置为 80 和**操作**设置为**允许**。

1. 选择“确定”  。

## <a name="view-the-welcome-to-nginx-page"></a>查看 nginx 页欢迎

与安装 NGINX 并在 VM 上打开端口 80，可以使用 VM 的公共 IP 地址访问 web 服务器。 (在 VM 上显示的公共 IP 地址**概述**页。)

打开 web 浏览器并转到*http://\<公共 IP 地址 >* 。

![NGINX web 服务器的欢迎页](media/azure-stack-quick-linux-portal/linux-05.PNG)

## <a name="clean-up-resources"></a>清理资源

清理不再需要的资源。 若要删除 VM 及其资源，选择 VM 页上的资源组，然后选择**删除**。

## <a name="next-steps"></a>后续步骤

在此快速入门中，您已部署具有 web 服务器的基本 Linux 服务器 VM。 若要了解有关 Azure Stack Vm 的详细信息，继续[在 Azure Stack 中的 Vm 的注意事项](azure-stack-vm-considerations.md)。
