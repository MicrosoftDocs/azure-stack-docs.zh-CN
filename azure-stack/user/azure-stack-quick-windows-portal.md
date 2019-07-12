---
title: 使用 Azure Stack 门户创建 Windows VM |Microsoft Docs
description: 了解如何使用 Azure Stack 门户创建 Windows Server 2016 虚拟机 (VM)。
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: cc9a6baa3c71e58c2671b1f1b221e18a0c4f38c1
ms.sourcegitcommit: b36d078e699c7924624b79641dbe9021af9606ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67816173"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-portal"></a>快速入门：使用 Azure Stack 门户创建 Windows server VM

适用对象：*Azure Stack 集成系统和 Azure Stack 开发工具包*

了解如何使用 Azure Stack 门户创建 Windows Server 2016 虚拟机 (VM)。

> [!NOTE]  
> 这篇文章中的屏幕截图更新以匹配随 Azure Stack 版本 1808年的用户界面。 除了非托管磁盘外，1808 版还添加了对使用*托管磁盘*的支持。 如果使用早期版本，某些映像，如磁盘选择将不同于在本文中显示的内容。  


## <a name="sign-in-to-the-azure-stack-portal"></a>登录到 Azure Stack 门户

登录到 Azure Stack 门户。 Azure Stack 门户的地址取决于要连接到的 Azure Stack 产品：

* 为 Azure Stack 开发工具包 (ASDK)，请转到： https://portal.local.azurestack.external 。
* 对于 Azure Stack 集成系统，请转到 Azure Stack 运营商提供的 URL。

## <a name="create-a-vm"></a>创建 VM

1. 单击“+ 创建资源”   > “计算”   > “Windows Server 2016 Datacenter - 即用即付”   > “创建”  。 <br> 如果没有看到**Windows Server 2016 Datacenter-为您的使用付费**条目，请联系 Azure Stack 操作员，并要求他们将其添加到应用商店中所述[添加到 Windows Server 2016 VM 映像Azure Stack marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md)一文。

    ![在门户中创建 Windows VM 的步骤](media/azure-stack-quick-windows-portal/image01.png)

2. 在“基本信息”  下，键入**名称**、**用户名**和**密码**。 选择“订阅”  。 创建一个**资源组**或选择现有资源组，选择一个**位置**，然后单击“确定”。 

    ![配置基本设置](media/azure-stack-quick-windows-portal/image02.png)

3. 下**大小**，选择**D1 标准**，然后单击**选择**。  

    ![选择 VM 的大小](media/azure-stack-quick-windows-portal/image03.png)

4. 在“设置”  页上，对默认设置进行任何所需的更改。
   - 从 Azure Stack 版本1808 开始，可以配置**存储**，可以在其中选择使用“托管磁盘”  。 在 1808年之前的版本，可以使用仅非托管的磁盘。  

   ![配置 VM 设置](media/azure-stack-quick-windows-portal/image04.png)  

   配置准备就绪后，选择“确定”  以继续。

5. 下**摘要**，单击**确定**以创建 VM。
    ![查看摘要和创建 VM](media/azure-stack-quick-windows-portal/image05.png)

6. 若要查看新的 VM，请单击**的所有资源**，搜索 VM 名称，并选择在搜索结果中。

    ![请参阅 VM](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>清理资源

完成使用 VM 后，将删除 VM 及其资源。 为此，请选择 VM 页上的资源组，然后单击**删除**。

## <a name="next-steps"></a>后续步骤

在此快速入门中，您已部署基本的 Windows Server VM。 若要了解有关 Azure Stack Vm 的详细信息，继续[在 Azure Stack 中的 Vm 的注意事项](azure-stack-vm-considerations.md)。
