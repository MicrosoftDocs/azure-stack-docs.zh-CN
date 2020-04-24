---
title: 在 Azure Stack Hub 中使用 Azure CLI 创建 Linux 虚拟机
description: 在 Azure Stack Hub 中使用 Azure CLI 创建 Linux 虚拟机。
author: mattbriggs
ms.topic: quickstart
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 49b2a950b523ca76143f50784efb048880d1ac93
ms.sourcegitcommit: 4138a2a15f78e7db38b3a29acc963a71937146fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2020
ms.locfileid: "77701932"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-the-azure-cli-in-azure-stack-hub"></a>快速入门：在 Azure Stack Hub 中使用 Azure CLI 创建 Linux 服务器 VM

可以使用 Azure CLI 创建 Ubuntu Server 16.04 LTS 虚拟机 (VM)。 在本文中，我们将创建和使用虚拟机。 本文还介绍以下操作：

* 通过远程客户端连接到虚拟机。
* 安装 NGINX Web 服务器并查看默认主页。
* 清理未使用的资源。

## <a name="prerequisites"></a>先决条件

* Azure Stack Hub 市场中的 Linux 映像

   默认情况下，Azure Stack Hub 市场不包含 Linux 映像。 让 Azure Stack Hub 操作员提供你需要的 Ubuntu Server 16.04 LTS 映像。 操作员可以使用[将市场项从 Azure 下载到 Azure Stack Hub](../operator/azure-stack-download-azure-marketplace-item.md) 中的说明。

* Azure Stack Hub 需要使用特定版本的 Azure CLI 来创建和管理其资源。 如果尚未针对 Azure Stack Hub 配置 Azure CLI，请登录到 [Azure Stack 开发工具包](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)（或登录到基于 Windows 的外部客户端，前提是[已通过 VPN 建立了连接](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn)），按照说明[安装并配置 Azure CLI](azure-stack-version-profiles-azurecli2.md)。

* Windows 用户配置文件的 *.ssh* 目录中保存的名为 *id_rsa.pub* 的安全外壳 (SSH) 公钥。 有关如何创建 SSH 密钥的详细信息，请参阅[使用 SSH 公钥](azure-stack-dev-start-howto-ssh-public-key.md)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是一个逻辑容器，可以在其中部署和管理 Azure Stack Hub 资源。 在开发工具包或 Azure Stack Hub 集成系统中，运行 [az group create](/cli/azure/group#az-group-create) 命令创建资源组。

> [!NOTE]
> 我们在以下代码示例中为所有变量分配了值。 但是，你可以分配自己的值。

以下示例在本地位置创建名为 myResourceGroup 的资源组： 

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

可以使用 [az vm create](/cli/azure/vm#az-vm-create) 命令创建虚拟机。 以下示例创建名为 myVM 的 VM。 此示例使用 *Demouser* 作为管理员用户名，使用 *Demouser@123* 作为管理员密码。 将这些值更改为适合你的环境的值。

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "UbuntuLTS" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

公共 IP 地址在 **PublicIpAddress** 参数中返回。 请记下该地址，供以后与虚拟机配合使用。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

由于此虚拟机将用来运行 IIS Web 服务器，因此需要为 Internet 流量打开端口 80。 若要打开该端口，请使用 [az vm open-port](/cli/azure/vm) 命令： 

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="use-ssh-to-connect-to-the-virtual-machine"></a>使用 SSH 连接到虚拟机

从安装了 SSH 的客户端计算机连接到虚拟机。 如果在 Windows 客户端上操作，请使用 [PuTTY](https://www.putty.org/) 创建连接。 若要连接到虚拟机，请使用以下命令：

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

在虚拟机上安装 NGINX Web 服务器并打开端口 80 后，可通过虚拟机的公共 IP 地址访问 Web 服务器。 为此，请打开浏览器并转到 ```http://<public IP address>```。

![NGINX Web 服务器欢迎页](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>清理资源

清理不再需要的资源。 可以使用 [az group delete](/cli/azure/group#az-group-delete) 命令删除它们。 运行以下命令：

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已部署了一个带有 Web 服务器的基本 Linux 服务器虚拟机。 若要详细了解 Azure Stack Hub 虚拟机，请参阅 [Azure Stack Hub 中虚拟机的注意事项](azure-stack-vm-considerations.md)。
