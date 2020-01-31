---
title: 使用 Azure CLI 在 Azure Stack 集线器上创建 Windows 虚拟机
description: 使用 Azure CLI 在 Azure Stack 集线器上创建 Windows 虚拟机
author: mattbriggs
ms.topic: quickstart
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: f3a895b2ec8245e95ed8482b84433f900e07dfab
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884473"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-using-azure-cli-in-azure-stack-hub"></a>快速入门：在 Azure Stack 中心使用 Azure CLI 创建 Windows Server 虚拟机

你可以使用 Azure CLI 创建 Windows Server 2016 虚拟机。 按照本文中的步骤创建和使用虚拟机。 本文还提供了以下步骤：

* 使用远程客户端连接到虚拟机。
* 安装 IIS web 服务器并查看默认主页。
* 清理资源。

## <a name="prerequisites"></a>必备组件

* 请确保 Azure Stack 中心操作员已将**Windows Server 2016**映像添加到 Azure Stack 中心市场。

* Azure Stack 中心需要 Azure CLI 的特定版本来创建和管理资源。 如果没有为 Azure Stack 中心配置 Azure CLI，请按照[安装和配置 Azure CLI](azure-stack-version-profiles-azurecli2.md)的步骤进行操作。

## <a name="create-a-resource-group"></a>创建资源组

资源组是可在其中部署和管理 Azure Stack 中心资源的逻辑容器。 从 Azure Stack 中心环境中，运行[az group create](/cli/azure/group#az-group-create)命令创建资源组。

> [!NOTE]
>  将为代码示例中的所有变量分配值。 不过，如果需要，可以指定新值。

以下示例在本地位置创建名为 myResourceGroup 的资源组：

```cli
az group create --name myResourceGroup --location local
```

## <a name="create-a-virtual-machine"></a>创建虚拟机

使用[az VM create](/cli/azure/vm#az-vm-create)命令创建虚拟机（VM）。 以下示例创建名为 myVM 的 VM。 此示例使用 Demouser 作为管理员用户名，并 Demouser@123 作为管理员密码。 将这些值更改为适合你的环境的值。

```cli
az vm create \
  --resource-group "myResourceGroup" \
  --name "myVM" \
  --image "Win2016Datacenter" \
  --admin-username "Demouser" \
  --admin-password "Demouser@123" \
  --location local
```

创建 VM 时，输出中的**PublicIPAddress**参数将包含虚拟机的公共 IP 地址。 记下此地址，因为你需要它来使用虚拟机。

## <a name="open-port-80-for-web-traffic"></a>为 Web 流量打开端口 80

由于此 VM 将运行 IIS web 服务器，因此需要打开端口80到 internet 的流量。

使用[az vm 打开端口](/cli/azure/vm)命令打开端口80：

```cli
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-the-virtual-machine"></a>连接到虚拟机

使用下一个命令创建到虚拟机的远程桌面连接。 将 "公共 IP 地址" 替换为虚拟机的 IP 地址。 系统提示时，输入用于虚拟机的用户名和密码。

```
mstsc /v <Public IP Address>
```

## <a name="install-iis-using-powershell"></a>使用 PowerShell 安装 IIS

现在，你已登录到虚拟机，可以使用 PowerShell 安装 IIS。 在虚拟机上启动 PowerShell 并运行以下命令：

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>查看 IIS 欢迎页

您可以使用所选的浏览器查看默认的 IIS 欢迎页。 使用上一节中列出的公共 IP 地址访问默认页面：

![IIS 默认站点](./media/azure-stack-quick-create-vm-windows-cli/default-iis-website.png)

## <a name="clean-up-resources"></a>清理资源

清理不再需要的资源。 使用[az group delete](/cli/azure/group#az-group-delete)命令删除资源组、虚拟机和所有相关资源。

```cli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了一个基本的 Windows Server 虚拟机。 若要详细了解 Azure Stack 集线器虚拟机，请继续参阅[Azure Stack 中心中的虚拟机的注意事项](azure-stack-vm-considerations.md)。
