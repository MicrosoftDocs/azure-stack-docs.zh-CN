---
title: 使用 Azure Stack 中心门户创建 Windows VM
description: 了解如何使用 Azure Stack 中心门户创建 Windows Server 2016 虚拟机（VM）。
author: mattbriggs
ms.topic: quickstart
ms.date: 1/10/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 1/10/2020
ms.openlocfilehash: 0792d0dffa8a61194b0f6725aba9222d69c8634e
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883294"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-hub-portal"></a>快速入门：使用 Azure Stack 中心门户创建 Windows server VM

了解如何使用 Azure Stack 中心门户创建 Windows Server 2016 虚拟机（VM）。

> [!NOTE]  
> 本文中的屏幕截图已更新，以匹配随 Azure Stack 集线器版本1808引入的用户界面。 1808添加了对使用*托管磁盘*和非托管磁盘的支持。 如果使用早期版本，则某些映像（如磁盘选择）将不同于本文中显示的内容。  


## <a name="sign-in-to-the-azure-stack-hub-portal"></a>登录到 Azure Stack 中心门户

登录到 Azure Stack 中心门户。 Azure Stack 中心门户的地址取决于要连接到的 Azure Stack 中心产品：

* 对于 Azure Stack 开发工具包（ASDK），请参阅： https://portal.local.azurestack.external 。
* 对于 Azure Stack 集线器集成系统，请参阅 Azure Stack 中心操作员提供的 URL。

## <a name="create-a-vm"></a>创建 VM

1. 选择 "**创建资源** > **计算**"。 搜索` Windows Server 2016 Datacenter – Pay as you use`。
    如果看不到 " **Windows Server 2016 Datacenter-即用即付**" 条目，请与 Azure Stack 中心云操作员联系，要求将图像添加到 Azure Stack 中心市场。 有关说明，你的云操作员可以参阅[创建和发布自定义 Azure Stack 中心市场项](../operator/azure-stack-create-and-publish-marketplace-item.md)。

    ![Windows Server 2016 Datacenter –按使用情况付费](./media/azure-stack-quick-windows-portal/image1.png)

1. 选择“创建”。

    ![创建资源](./media/azure-stack-quick-windows-portal/image2.png)

1. 在 "**基本**信息" 下输入**名称**、**磁盘类型**、**用户名**和**密码**。 选择“订阅”。 创建**资源组**或选择现有资源组，选择一个**位置**，然后选择 **"确定"** 。

    ![创建 VM-基础](./media/azure-stack-quick-windows-portal/image3.png)

1. 选择 "**大小**" 下**D1_v2** ，然后选择 "**选择**"。

    ![创建 VM-大小](./media/azure-stack-quick-windows-portal/image4.png)

1. 在 "**设置**" 页上，对默认值进行任何所需的更改。 您必须从相关的下拉配置所需的公用入站端口。 完成后，选择“确定”。

    ![创建 VM-设置](./media/azure-stack-quick-windows-portal/image5.png)

1. 选择 "**摘要**" 下的 **"确定"** 以创建 VM。

    ![创建 VM-摘要](./media/azure-stack-quick-windows-portal/image6.png)

1. 选择 "**虚拟机**" 以查看新 VM。 搜索 VM 名称，然后在搜索结果中选择该 VM。

![为 VM 创建 VM 搜索](./media/azure-stack-quick-windows-portal/image7.png)

## <a name="clean-up-resources"></a>清理资源

使用完 VM 后，请删除 VM 及其资源。 为此，请在 "VM" 页上选择资源组，然后选择 "**删除**"。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了一个基本的 Windows Server VM。 若要详细了解 Azure Stack 集线器 Vm，请继续[考虑 Azure Stack 中心的 vm 的注意事项](azure-stack-vm-considerations.md)。
