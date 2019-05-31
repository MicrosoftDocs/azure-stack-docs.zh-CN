---
title: 在 Azure Stack 中使用 Azure CLI 创建 Linux 虚拟机 |Microsoft Docs
description: 在 Azure Stack 中使用 Azure CLI 创建 Linux 虚拟机。
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
ms.date: 05/16/2019
ms.author: mabrigg
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d47e5908e674a8b57b9e6d686e4596e1002b67c9
ms.sourcegitcommit: 2ee75ded704e8cfb900d9ac302d269c54a5dd9a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394403"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-cli-in-azure-stack"></a>快速入门：在 Azure Stack 中使用 Azure CLI 创建 Linux 服务器 VM

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 Azure CLI 创建 Ubuntu Server 16.04 LTS 虚拟机 (VM)。 在本文中，将创建和使用虚拟机。 本文还演示了如何为：

* 通过远程客户端连接到虚拟机。
* 安装 NGINX web 服务器，并查看默认的主页。
* 清理未使用的资源。

## <a name="prerequisites"></a>必备组件

* Azure Stack Marketplace 中的 Linux 映像

   默认情况下，Azure Stack Marketplace 不包含 Linux 映像。 具有 Azure Stack 操作员提供所需的 Ubuntu Server 16.04 LTS 映像。 操作员可以使用中的说明[下载 Marketplace 项从 Azure 到 Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md)。

* Azure Stack 需要 Azure CLI 创建和管理其资源的特定版本。 如果你没有针对 Azure Stack 配置 Azure CLI，登录到[Azure Stack 开发工具包](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)(或基于 Windows 的外部客户端如果[通过 VPN 建立连接](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn))，并按照说明进行操作有关[安装和配置 Azure CLI](azure-stack-version-profiles-azurecli2.md)。

* 名称的公共安全外壳 (SSH) 密钥*id_rsa.pub*以保存 *.ssh* Windows 用户配置文件的目录。 有关创建 SSH 密钥的详细信息，请参阅[使用 SSH 公钥](azure-stack-dev-start-howto-ssh-public-key.md)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是一个逻辑容器，可以在其中部署和管理 Azure Stack 资源。 在开发工具包或 Azure Stack 集成系统中，运行 [az group create](/cli/azure/group#az-group-create) 命令创建资源组。

> [!NOTE]
> 我们已将分配下面的代码示例中的所有变量的值。 但是，可以分配你自己的值。

以下示例创建名为 myResourceGroup 中的本地位置的资源组： 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

可以使用 [az vm create](/cli/azure/vm#az-vm-create) 命令创建虚拟机。 以下示例创建名为 myVM 的 VM。 该示例使用*Demouser*作为管理员用户名和 *Demouser@123* 作为管理员密码。 将这些值更改为可适用于你的环境。

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

公共 IP 地址在 **PublicIpAddress** 参数中返回。 请注意地址供稍后使用与虚拟机。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

因为此虚拟机将运行 IIS 的 web 服务器，您需要为 internet 流量打开端口 80。 若要打开端口，请使用[az vm 打开端口](/cli/azure/vm)命令： 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>使用 SSH 连接到虚拟机

从安装了 SSH 的客户端计算机连接到虚拟机。 如果您正在 Windows 客户端上，使用[PuTTY](https://www.putty.org/)以创建连接。 若要连接到虚拟机，请使用以下命令：

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

安装 NGINX web 服务器和虚拟机上打开端口 80，可以通过使用虚拟机的公共 IP 地址访问 web 服务器。 为此，请打开浏览器并转到```http://<public IP address>```。

![NGINX web 服务器的欢迎页](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>清理资源

清理不再需要的资源。 可以使用[az 组删除](/cli/azure/group#az-group-delete)命令将其删除。 运行以下命令：

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已部署了一个带有 Web 服务器的基本 Linux 服务器虚拟机。 若要了解有关 Azure Stack 虚拟机的详细信息，请参阅[在 Azure Stack 中的虚拟机的注意事项](azure-stack-vm-considerations.md)。
