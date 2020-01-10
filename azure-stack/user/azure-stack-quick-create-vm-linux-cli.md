---
title: 使用 Azure Stack 中心中的 Azure CLI 创建 Linux 虚拟机 |Microsoft Docs
description: 使用 Azure Stack 中心的 Azure CLI 创建 Linux 虚拟机。
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 2c716504dafe4ec76f53006b97e1623289b2c036
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75819448"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-cli-in-azure-stack-hub"></a>快速入门：使用 Azure Stack 中心的 Azure CLI 创建 Linux 服务器 VM

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

可以使用 Azure CLI 创建 Ubuntu Server 16.04 LTS 虚拟机（VM）。 本文介绍如何创建和使用虚拟机。 本文还介绍了如何执行以下操作：

* 使用远程客户端连接到虚拟机。
* 安装 NGINX web 服务器并查看默认主页。
* 清理未使用的资源。

## <a name="prerequisites"></a>必备组件

* Azure Stack 中心应用商店中的 Linux 映像

   默认情况下，Azure Stack 集线器 Marketplace 不包含 Linux 映像。 让 Azure Stack 集线器操作员提供所需的 Ubuntu Server 16.04 LTS 映像。 操作员可以使用将[Marketplace 项从 Azure 下载到 Azure Stack 中心](../operator/azure-stack-download-azure-marketplace-item.md)中的说明。

* Azure Stack 中心需要 Azure CLI 的特定版本来创建和管理其资源。 如果没有为 Azure Stack 中心配置 Azure CLI，请登录到[Azure Stack 开发工具包](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)（如果[通过 VPN 连接](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)，则登录到基于 Windows 的外部客户端），并按照说明[安装和配置 Azure CLI](azure-stack-version-profiles-azurecli2.md)。

* 名为*id_rsa*的公用安全外壳（SSH）密钥保存在 Windows 用户配置文件的*SSH*目录中。 有关创建 SSH 密钥的详细信息，请参阅[使用 ssh 公钥](azure-stack-dev-start-howto-ssh-public-key.md)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是可在其中部署和管理 Azure Stack 中心资源的逻辑容器。 从开发工具包或 Azure Stack 中心集成系统中，运行[az group create](/cli/azure/group#az-group-create)命令创建资源组。

> [!NOTE]
> 我们为以下代码示例中的所有变量分配了值。 不过，您可以分配自己的值。

以下示例在本地位置创建名为 myResourceGroup 的资源组： 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用[az vm create](/cli/azure/vm#az-vm-create)命令创建虚拟机。 以下示例创建名为 myVM 的 VM。 该示例使用*Demouser*作为管理员用户名，并 *Demouser@123* 作为管理员密码。 将这些值更改为适合你的环境的值。

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

公共 IP 地址在**PublicIpAddress**参数中返回。 记下该地址以便以后用于虚拟机。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

由于此虚拟机将运行 IIS web 服务器，因此需要打开端口80到 internet 的流量。 若要打开端口，请使用[az vm 打开端口](/cli/azure/vm)命令： 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>使用 SSH 连接到虚拟机

在已安装 SSH 的客户端计算机上，连接到虚拟机。 如果使用的是 Windows 客户端，请使用[PuTTY](https://www.putty.org/)创建连接。 若要连接到虚拟机，请使用以下命令：

```bash
ssh <publicIpAddress>
```

## <a name="install-the-nginx-web-server"></a>安装 NGINX web 服务器

若要更新包资源并安装最新的 NGINX 包，请运行以下脚本：

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>查看 NGINX 欢迎页

安装 NGINX web 服务器并在虚拟机上打开端口80后，可以使用虚拟机的公共 IP 地址访问 web 服务器。 为此，请打开浏览器，并中转到 ```http://<public IP address>```。

![NGINX web 服务器欢迎页](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>清理资源

清理不再需要的资源。 可以使用[az group delete](/cli/azure/group#az-group-delete)命令将其删除。 运行以下命令：

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了包含 web 服务器的基本 Linux 服务器虚拟机。 若要了解有关 Azure Stack 中心虚拟机的详细信息，请参阅[Azure Stack 集线器中虚拟机的注意事项](azure-stack-vm-considerations.md)。
