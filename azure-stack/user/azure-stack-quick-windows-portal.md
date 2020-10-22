---
title: 使用 Azure Stack Hub 门户创建 Windows VM
description: 了解如何使用 Azure Stack Hub 门户创建 Windows Server 2016 虚拟机 (VM)。
author: mattbriggs
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/06/2020
ms.openlocfilehash: efd182e35f8229ee171ec0e03c71829c17f7c32e
ms.sourcegitcommit: 8ffa29f71d69191534d42f86f49f719b4198a097
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2020
ms.locfileid: "92353904"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-hub-portal"></a>快速入门：使用 Azure Stack Hub 门户创建 Windows 服务器 VM

了解如何使用 Azure Stack Hub 门户创建 Windows Server 2016 虚拟机 (VM)。

> [!NOTE]  
> 本文中的屏幕截图已更新，以匹配 Azure Stack Hub 版本 1808 中引入的用户界面。 除了非托管磁盘外，1808 版还添加了对使用*托管磁盘*的支持。 如果使用早期版本，则某些图像（如磁盘选择）将与本文中显示的不同。  


## <a name="sign-in-to-the-azure-stack-hub-portal"></a>登录到 Azure Stack Hub 门户

登录到 Azure Stack Hub 门户。 Azure Stack Hub 门户的地址取决于要连接到的 Azure Stack Hub 产品：

* 对于 Azure Stack 开发工具包 (ASDK)，请转到 `https://portal.local.azurestack.external` 。
* 对于 Azure Stack Hub 集成系统，请转到 Azure Stack Hub 操作员提供的 URL。

## <a name="create-a-vm"></a>创建 VM

1. 选择“创建资源”   > “计算”  。 搜索 ` Windows Server 2016 Datacenter - Pay as you use`。
    如果看不到 " **Windows Server 2016 Datacenter-即用即付** " 条目，请与 Azure Stack 中心云操作员联系，要求将图像添加到 Azure Stack 中心市场。 有关说明，你的云操作员可以参阅 [创建和发布自定义 Azure Stack 中心市场项](../operator/azure-stack-create-and-publish-marketplace-item.md)。

    ![Windows Server 2016 Datacenter - 即用即付](./media/azure-stack-quick-windows-portal/image1a.png)

1. 选择“创建”  。

    ![创建资源](./media/azure-stack-quick-windows-portal/image2a.png)

1. 在“基本信息”下，输入“名称”、“磁盘类型”、“用户名”和“密码”。      选择“订阅”  。 创建一个**资源组**或选择现有资源组，选择一个**位置**，然后选择“确定”。 

    ![创建 VM - 基本信息](./media/azure-stack-quick-windows-portal/image3a.png)

1. 在“大小”下，选择“D1_v2”，然后选择“选择”。   

    ![创建 VM - 大小](./media/azure-stack-quick-windows-portal/image4a.png)

1. 在“设置”  页上，对默认设置进行任何所需的更改。 你必须从相关下拉框中配置所需的公共入站端口。 完成后，选择“确定”  。

    ![创建 VM - 设置](./media/azure-stack-quick-windows-portal/image5a.png)

1. 在“摘要”下，选择“确定”以创建 VM。  

    ![创建 VM - 摘要](./media/azure-stack-quick-windows-portal/image6a.png)

1. 选择“虚拟机”来查看你的新 VM。  搜索 VM 名称，然后在搜索结果中选择该 VM。

![创建 VM - 搜索 VM](./media/azure-stack-quick-windows-portal/image7a.png)

## <a name="clean-up-resources"></a>清理资源

使用完 VM 后，请删除 VM 及其资源。 为此，请选择 VM 页上的资源组，然后选择“删除”。 

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已部署了一台基本的 Windows Server VM。 若要了解 Azure Stack VM Hub 的详细信息，请继续阅读 [Azure Stack Hub 中 VM 的注意事项](azure-stack-vm-considerations.md)。
