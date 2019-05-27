---
title: 使用 Azure Stack 中创建 VM 的 Linux 服务器 |Microsoft Docs
description: 使用 Azure Stack 中创建的 Linux 服务器 VM。
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
ms.openlocfilehash: 379c473080fdbddec811d7982a571cd00e6e1e62
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/24/2019
ms.locfileid: "66197426"
---
# <a name="quickstart-create-a-linux-server-vm-with-the-azure-stack-portal"></a>快速入门：使用 Azure Stack 门户创建 VM 的 Linux 服务器

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 Azure Stack 门户来创建 Ubuntu Server 16.04 LTS 虚拟机 (VM)。 请按照本文中的步骤创建和使用 VM。 本文还提供了执行以下操作的步骤：

* 通过远程客户端连接到 VM。
* 安装 NGINX Web 服务器。
* 清理资源。

> [!NOTE]  
> 这篇文章中的屏幕截图更新以匹配 1808年的 Azure Stack 版本中引入的更改。 除了非托管磁盘外，1808 版还添加了对使用*托管磁盘*的支持。 如果使用早期版本，任务，例如磁盘选择一些屏幕截图将显示不同于在 UI 中看到的内容。  


## <a name="prerequisites"></a>必备组件

* **Azure Stack 市场中的 Linux 映像**

   默认情况下，Azure Stack 市场中没有 Linux 映像。 获取 Azure Stack 操作员提供**Ubuntu Server 16.04 LTS**在 marketplace 中的映像。 操作员可以使用[将市场项从 Azure 下载到 Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md) 一文中介绍的步骤。

* **可以访问 SSH 客户端**

   如果使用的 Azure Stack 开发工具包 (ASDK)，您可能没有访问 SSH 客户端。 如果需要一个客户端，有多个包含 SSH 客户端的包可供使用。 例如，PuTTY 包含 SSH 客户端和 SSH 密钥生成器 (puttygen.exe)。 有关可用的包的详细信息，请阅读[如何使用 SSH 公钥](azure-stack-dev-start-howto-ssh-public-key.md)一文。

   本快速入门使用 PuTTY 生成 SSH 密钥并将连接到 Linux 服务器 VM。 若要下载并安装 PuTTY，请转到 [https://www.putty.org/](https://www.putty.org)。

## <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对

需要一个 SSH 密钥对来完成本文中的所有步骤。 如果有现成的 SSH 密钥对，则可跳过此步骤。

1. 导航到 PuTTY 安装文件夹（默认位置为 `C:\Program Files\PuTTY`）并运行 `puttygen.exe`。
2. 在 PuTTY 密钥生成器窗口中，设置**要生成的密钥类型**到**RSA**，并**中生成的密钥的位数**到**2048年**。 准备就绪时，单击“生成”。

   ![PuTTY 密钥生成器配置](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. 若要生成密钥，请在 PuTTY 密钥生成器窗口中随机移动鼠标指针。
4. 完成密钥生成过程后，单击“保存公钥”和“保存私钥”来将密钥保存到文件中。

   ![PuTTY 密钥生成器结果](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>登录到 Azure Stack 门户

Azure Stack 门户的地址取决于要连接到的 Azure Stack 产品：

* 为 Azure Stack 开发工具包 (ASDK)，请转到： https://portal.local.azurestack.external。
* 对于 Azure Stack 集成系统，请转到 Azure Stack 运营商提供的 URL。

## <a name="create-the-vm"></a>创建 VM

1. 在 Azure Stack 门户的左上角单击“创建资源”。

2. 选择“计算”，然后选择“Ubuntu Server 16.04 LTS”。
   
   ![选择 Linux 服务器](media/azure-stack-quick-linux-portal/select.png)
1. 单击**创建**。

4. 键入 VM 的信息。 对于“身份验证类型”，请选择“SSH 公钥”。 粘贴保存的 SSH 公钥，然后单击“确定”。

   > [!NOTE]
   > 请确保删除密钥的任何前导或尾随空格。

   ![基础知识面板-配置 VM](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. 选择**D1** vm。

   ![Size 面板-选择 VM 大小](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. 在“设置”页上，对默认设置进行任何所需的更改。
   
   - 从 Azure Stack 版本 1808年，你可以配置**存储**，然后选择要使用*托管磁盘*。 在 1808年之前版本中，可以使用仅非托管的磁盘。
     ![为托管磁盘配置存储](media/azure-stack-quick-linux-portal/linux-03.PNG)
    
     配置准备就绪后，选择“确定”以继续。

7. 上**摘要**页上，单击**确定**以启动 VM 部署。  
   ![部署](media/azure-stack-quick-linux-portal/deploy.png)

## <a name="connect-to-the-vm"></a>连接到 VM

1. 单击**Connect**的 VM 上。 您可以找到需要连接到 VM 的 SSH 连接字符串。 

2. 打开 PuTTY。

3. 在 PuTTY 配置屏幕上，将使用**类别**窗口中向上或向下滚动。 向下滚动到“SSH”，展开“SSH”，然后单击“身份验证”。单击“浏览”，选择已保存的私钥文件。
   ![连接 VM](media/azure-stack-quick-linux-portal/putty03.PNG)

4. 在“类别”窗口中向上滚动，然后单击“会话”。
5. 在“主机名(或 IP 地址)”框中，粘贴 Azure Stack 门户中显示的连接字符串。 在本示例中，该字符串为 `asadmin@192.168.102.34`。

   ![PuTTY 配置连接字符串](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. 单击**打开**vm 打开会话。

   ![Linux 会话](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>安装 NGINX Web 服务器

使用以下 bash 命令更新包源并在 VM 上安装最新的 NGINX 包。

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完成安装 NGINX 后，关闭 SSH 会话，并在 Azure Stack 门户中打开 VM 概述页。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

网络安全组 (NSG) 保护入站和出站流量。 在 Azure Stack 门户中创建 VM，进行 SSH 连接端口 22 上创建入站的规则。 由于此 VM 托管的 web 服务器，需要创建以允许端口 80 上的 web 流量 NSG 规则。

1. 在 VM 上**概述**页上，单击名称**资源组**。
2. 选择**网络安全组**vm。 可以通过“类型”列来标识 NSG。
3. 在左侧菜单的“设置”下，单击“入站安全规则”。
4. 单击“添加”。
5. 在“名称”中，键入 **http**。 请确保将“端口范围”设置为 80，将“操作”设置为“允许”。
6. 单击“确定”。

## <a name="view-the-nginx-welcome-page"></a>查看 NGINX 欢迎页

与安装 NGINX 并在 VM 上打开端口 80，可以访问 web 服务器使用 VM 的公共 IP 地址。 （公共 IP 地址在 VM 的概述页中显示）。

打开浏览器并转到`http://<public IP address>`。

![NGINX Web 服务器欢迎页](media/azure-stack-quick-linux-portal/linux-05.PNG)

## <a name="clean-up-resources"></a>清理资源

清理不再需要的资源。 若要删除 VM 及其资源，选择 VM 页上的资源组，然后单击**删除**。

## <a name="next-steps"></a>后续步骤

在此快速入门中，您已部署具有 web 服务器的基本 Linux 服务器 VM。 若要了解有关 Azure Stack Vm 的详细信息，继续[在 Azure Stack 中的 Vm 的注意事项](azure-stack-vm-considerations.md)。
