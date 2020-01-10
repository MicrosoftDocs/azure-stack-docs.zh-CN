---
title: 使用 Azure Stack 中心门户创建 Windows VM |Microsoft Docs
description: 了解如何使用 Azure Stack 中心门户创建 Windows Server 2016 虚拟机（VM）。
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: bd90937d310b07c0d16497a3e00917f05b6ff0d1
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75815878"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-hub-portal"></a>快速入门：使用 Azure Stack 中心门户创建 Windows server VM

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

了解如何使用 Azure Stack 中心门户创建 Windows Server 2016 虚拟机（VM）。

> [!NOTE]  
> 本文中的屏幕截图已更新，以匹配随 Azure Stack 集线器版本1808引入的用户界面。 1808添加了对使用*托管磁盘*和非托管磁盘的支持。 如果使用早期版本，则某些映像（如磁盘选择）将不同于本文中显示的内容。  


## <a name="sign-in-to-the-azure-stack-hub-portal"></a>登录到 Azure Stack 中心门户

登录到 Azure Stack 中心门户。 Azure Stack 中心门户的地址取决于要连接到的 Azure Stack 中心产品：

* 对于 Azure Stack 开发工具包（ASDK），请参阅： https://portal.local.azurestack.external 。
* 对于 Azure Stack 集线器集成系统，请参阅 Azure Stack 中心操作员提供的 URL。

## <a name="create-a-vm"></a>创建 VM

1. 单击 " **+ 创建资源**" > **计算** > **Windows Server 2016 Datacenter-即用即付** > **创建**"。 <br> 如果看不到 " **Windows server 2016 Datacenter-即用即付**" 条目，请与 Azure Stack 中心操作员联系，并要求他们将其添加到 marketplace，如将[WINDOWS Server 2016 VM 映像添加到 Azure Stack 中心市场](../operator/azure-stack-create-and-publish-marketplace-item.md)文章中所述。

    ![在门户中创建 Windows VM 的步骤](media/azure-stack-quick-windows-portal/image01.png)

2. 在 "**基本**信息" 下，键入**名称**、**用户名**和**密码**。 选择“订阅”。 创建**资源组**或选择现有资源组，选择一个**位置**，然后单击 **"确定"** 。

    ![配置基本设置](media/azure-stack-quick-windows-portal/image02.png)

3. 在 "**大小**" 下，选择 " **D1 标准**"，然后单击 "**选择**"。  

    ![选择 VM 的大小](media/azure-stack-quick-windows-portal/image03.png)

4. 在 "**设置**" 页上，对默认值进行任何所需的更改。
   - 从 Azure Stack 集线器版本1808开始，你可以配置**存储**，你可以在其中选择使用*托管磁盘*。 在1808之前的版本中，仅可使用非托管磁盘。  

   ![配置 VM 设置](media/azure-stack-quick-windows-portal/image04.png)  

   配置准备就绪后，请选择 **"确定"** 继续。

5. 在 "**摘要**" 下，单击 **"确定"** 以创建 VM。
    ![查看摘要和创建 VM](media/azure-stack-quick-windows-portal/image05.png)

6. 若要查看新 VM，请单击 "**所有资源**"，搜索 VM 名称，然后在搜索结果中选择它。

    ![请参阅 VM](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>清理资源

使用完 VM 后，请删除 VM 及其资源。 为此，请在 VM 页面上选择资源组，并单击 "**删除**"。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你部署了一个基本的 Windows Server VM。 若要详细了解 Azure Stack 集线器 Vm，请继续[考虑 Azure Stack 中心的 vm 的注意事项](azure-stack-vm-considerations.md)。
