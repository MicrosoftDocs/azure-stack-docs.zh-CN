---
title: 创建 Linux 虚拟机在 Azure Stack 中使用 Azure CLI |Microsoft Docs
description: 创建在 Azure Stack 中使用 CLI 为 Linux 虚拟机。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d31ee453dda8bb83ff24c07902d7a1250490bb08
ms.sourcegitcommit: 87d93cdcdb6efb06e894f56c2f09cad594e1a8b3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65712394"
---
# <a name="quickstart-create-a-linux-server-virtual-machine-using-azure-cli-in-azure-stack"></a>快速入门：创建在 Azure Stack 中使用 Azure CLI 为 Linux server 虚拟机

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 Azure CLI 创建 Ubuntu Server 16.04 LTS 虚拟机。 请按照本文中的步骤创建和使用虚拟机。 本文还提供了执行以下操作的步骤：

* 通过远程客户端连接到虚拟机。
* 安装 NGINX Web 服务器并查看默认主页。
* 清理未使用的资源。

## <a name="prerequisites"></a>必备组件

* **Azure Stack 市场中的 Linux 映像**

   默认情况下，Azure Stack 市场不包含 Linux 映像。 让 Azure Stack 操作员提供你需要的 **Ubuntu Server 16.04 LTS** 映像。 操作员可以使用[将市场项从 Azure 下载到 Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md) 一文中介绍的步骤。

* Azure Stack 需要使用特定版本的 Azure CLI 来创建和管理资源。 如果你没有针对 Azure Stack 配置 Azure CLI，登录到[开发工具包](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)(或基于 Windows 的外部客户端如果[通过 VPN 建立连接](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn))，遵循的步骤[安装和配置 Azure CLI](azure-stack-version-profiles-azurecli2.md)。

* Windows 用户配置文件的 .ssh 目录中保存的名为 id_rsa.pub 的 SSH 公钥。 有关创建 SSH 密钥的详细信息，请参阅[如何使用 SSH 公钥](azure-stack-dev-start-howto-ssh-public-key.md)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是一个逻辑容器，可以在其中部署和管理 Azure Stack 资源。 在开发工具包或 Azure Stack 集成系统中，运行 [az group create](/cli/azure/group#az-group-create) 命令创建资源组。

> [!NOTE]
> 有关代码示例中的所有变量分配值。 但是，如果愿意，也可以分配新值。

以下示例创建名为 myResourceGroup 中的本地位置的资源组： 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

可以使用 [az vm create](/cli/azure/vm#az-vm-create) 命令创建虚拟机。 以下示例创建名为 myVM 的 VM。 此示例使用 Demouser 作为管理员用户名和Demouser@123作为管理员密码。 将这些值更改为适合你的环境的值。

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

公共 IP 地址在 **PublicIpAddress** 参数中返回。 因为您需要它来使用虚拟机，请记下此地址。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

因为此虚拟机将运行 IIS 的 web 服务器，您需要为 internet 流量打开端口 80。 使用[az vm 打开端口](/cli/azure/vm)命令打开所需的端口： 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>使用 SSH 连接到虚拟机

从安装了 SSH 的客户端计算机连接到虚拟机。 如果在 Windows 客户端上操作，请使用 [Putty](https://www.putty.org/) 创建连接。 若要连接到虚拟机，请使用以下命令：

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>安装 NGINX Web 服务器

若要更新包源并安装最新的 NGINX 包，请运行以下脚本：

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>查看 NGINX 欢迎页

在虚拟机上安装 NGINX 并打开端口 80 后，可通过虚拟机的公共 IP 地址访问 Web 服务器。 打开浏览器并转到```http://<public IP address>```。

![NGINX Web 服务器欢迎页](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>清理资源

清理不再需要的资源。 可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令删除这些资源。 若要删除资源组及其所有资源，请运行以下命令：

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已部署了一个带有 Web 服务器的基本 Linux 服务器虚拟机。 有关 Azure Stack 虚拟机的详细信息，请转到 [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)。
