---
title: Azure Stack 快速入门-创建 VM 门户
description: Azure Stack 快速入门-创建 Linux VM 使用门户
services: azure-stack
cloud: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.custom: mvc
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 37ff24258b12c9b042c7b0dc5a113a62d1d1ec60
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2019
ms.locfileid: "64477350"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-with-the-azure-stack-portal"></a>快速入门：使用 Azure Stack 门户创建 Linux 服务器虚拟机

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 Azure Stack 门户创建 Ubuntu Server 16.04 LTS 虚拟机。 请按照本文中的步骤创建和使用虚拟机。 本文还提供了执行以下操作的步骤：

* 通过远程客户端连接到虚拟机。
* 安装 NGINX Web 服务器。
* 清理资源。

> [!NOTE]  
> 本文中的屏幕图像已更新，以匹配 Azure Stack 版本 1808 中引入的更改。 除了非托管磁盘外，1808 版还添加了对使用*托管磁盘*的支持。 如果使用早期版本，则磁盘选择等任务的某些图像将与本文中显示的不同。  


## <a name="prerequisites"></a>必备组件

* **Azure Stack 市场中的 Linux 映像**

   默认情况下，Azure Stack 市场中没有 Linux 映像。 请检查是否已经让 Azure Stack 操作员在市场中提供了 **Ubuntu Server 16.04 LTS** 映像。 操作员可以使用[将市场项从 Azure 下载到 Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md) 一文中介绍的步骤。

* **可以访问 SSH 客户端**

   如果使用 Azure Stack 开发工具包 (ASDK)，可能无法访问 SSH 客户端。 如果需要一个客户端，有多个包含 SSH 客户端的包可供使用。 例如，PuTTY 包含 SSH 客户端和 SSH 密钥生成器 (puttygen.exe)。 有关可用的包的详细信息，请阅读以下 Azure 文章：[如何使用 SSH 公钥](azure-stack-dev-start-howto-ssh-public-key.md)。

   本快速入门使用 PuTTY 生成 SSH 密钥并连接到 Linux 服务器虚拟机。 若要下载并安装 PuTTY，请转到 [https://www.putty.org/](https://www.putty.org)。

## <a name="create-an-ssh-key-pair"></a>创建 SSH 密钥对

需要一个 SSH 密钥对来完成本文中的所有步骤。 如果有现成的 SSH 密钥对，则可跳过此步骤。

1. 导航到 PuTTY 安装文件夹（默认位置为 ```C:\Program Files\PuTTY```）并运行 ```puttygen.exe```。
2. 在“PuTTY 密钥生成器”窗口中，确保“要生成的密钥类型”已设置为 **RSA**，并且“所生成密钥中的位数”已设置为 **2048**。 准备就绪时，单击“生成”。

   ![PuTTY 密钥生成器配置](media/azure-stack-quick-linux-portal/Putty01.PNG)

3. 若要生成密钥，请在 PuTTY 密钥生成器窗口中随机移动鼠标指针。
4. 完成密钥生成过程后，单击“保存公钥”和“保存私钥”来将密钥保存到文件中。

   ![PuTTY 密钥生成器结果](media/azure-stack-quick-linux-portal/Putty02.PNG)

## <a name="sign-in-to-the-azure-stack-portal"></a>登录到 Azure Stack 门户

登录到 Azure Stack 门户。 Azure Stack 门户的地址取决于连接到的 Azure Stack 产品：

* 对于 Azure Stack 开发工具包 (ASDK)，请转到： https://portal.local.azurestack.external。
* 对于 Azure Stack 集成系统，请转到 Azure Stack 运营商提供的 URL。

## <a name="create-the-virtual-machine"></a>创建虚拟机

1. 在 Azure Stack 门户的左上角单击“创建资源”。

2. 选择“计算”，然后选择“Ubuntu Server 16.04 LTS”。
   
   ![选择 Linux 服务器](media/azure-stack-quick-linux-portal/select.png)
1. 单击**创建**。

4. 键入虚拟机信息。 对于“身份验证类型”，请选择“SSH 公钥”。 粘贴保存的 SSH 公钥，然后单击“确定”。

   > [!NOTE]
   > 请确保删除密钥中的所有前导和尾随空格。

   ![基本信息面板 - 配置虚拟机](media/azure-stack-quick-linux-portal/linux-01.PNG)

5. 为虚拟机选择“D1”。

   ![大小面板 - 选择虚拟机大小](media/azure-stack-quick-linux-portal/linux-02.PNG)

6. 在“设置”页上，对默认设置进行任何所需的更改。
   
   - 从 Azure Stack 版本1808 开始，可以配置**存储**，可以在其中选择使用“托管磁盘”。 在版本 1808 之前，只能使用非托管磁盘。    
     ![为托管磁盘配置存储](media/azure-stack-quick-linux-portal/linux-03.PNG)
    
     配置准备就绪后，选择“确定”以继续。

7. 在“摘要”页上，单击“确定”开始部署虚拟机。  
   ![部署](media/azure-stack-quick-linux-portal/deploy.png)

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

1. 在虚拟机页上，单击“连接”。 可以发现连接到虚拟机时所需的 SSH 连接字符串。 

2. 打开 PuTTY。

3. 在“PuTTY 配置”屏幕上，你将使用“类别”窗口上下滚动。 向下滚动到“SSH”，展开“SSH”，然后单击“身份验证”。单击“浏览”，选择已保存的私钥文件。
   ![连接虚拟机](media/azure-stack-quick-linux-portal/putty03.PNG)

4. 在“类别”窗口中向上滚动，然后单击“会话”。
5. 在“主机名(或 IP 地址)”框中，粘贴 Azure Stack 门户中显示的连接字符串。 在本示例中，该字符串为 ```asadmin@192.168.102.34```。

   ![PuTTY 配置连接字符串](media/azure-stack-quick-linux-portal/Putty04.PNG)

6. 单击“打开”来打开虚拟机的会话。

   ![Linux 会话](media/azure-stack-quick-linux-portal/Putty05.PNG)

## <a name="install-the-nginx-web-server"></a>安装 NGINX Web 服务器

使用以下 bash 命令在虚拟机上更新包源并安装最新的 NGINX 包。

```bash
#!/bin/bash

# update package source
sudo apt-get -y update

# install NGINX
sudo apt-get -y install nginx
```

完成 NGINX 安装后，关闭 SSH 会话并打开 Azure Stack 门户中的虚拟机“概述”页。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

网络安全组 (NSG) 保护入站和出站流量的安全。 在 Azure Stack 门户中创建虚拟机时，会在用于 SSH 连接的端口 22 上创建入站规则。 由于此虚拟机托管着 Web 服务器，因此需要创建一个 NSG 规则来允许端口 80 上的 Web 流量。

1. 在虚拟机的“概述”页上，单击**资源组**的名称。
2. 选择虚拟机的**网络安全组**。 可以通过“类型”列来标识 NSG。
3. 在左侧菜单的“设置”下，单击“入站安全规则”。
4. 单击“添加”。
5. 在“名称”中，键入 **http**。 请确保将“端口范围”设置为 80，将“操作”设置为“允许”。
6. 单击 **“确定”**

## <a name="view-the-nginx-welcome-page"></a>查看 NGINX 欢迎页

在虚拟机上安装 NGINX 并打开端口 80 后，可通过虚拟机的公共 IP 地址访问 Web 服务器。 （该公用 IP 地址显示在虚拟机的“概述”页上。）

打开 Web 浏览器，并浏览到 ```http://<public IP address>```。

![NGINX Web 服务器欢迎页](media/azure-stack-quick-linux-portal/linux-05.PNG)

## <a name="clean-up-resources"></a>清理资源

清理不再需要的资源。 若要删除虚拟机及其资源，请在虚拟机页面上选择资源组，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已部署了一个带有 Web 服务器的基本 Linux 服务器虚拟机。 有关 Azure Stack 虚拟机的详细信息，请转到 [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)。
