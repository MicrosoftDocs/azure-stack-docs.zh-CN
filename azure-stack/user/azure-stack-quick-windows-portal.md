---
title: 使用 Azure Stack 门户创建 Windows VM | Microsoft Docs
description: 了解如何使用 Azure Stack 门户创建 Windows Server 2016 虚拟机 (VM)。
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
ms.openlocfilehash: 95fef782ca7efe09f7c93fbf0e28e81ed34d8166
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71823929"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-portal"></a>快速入门：使用 Azure Stack 门户创建 Windows 服务器 VM

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

了解如何使用 Azure Stack 门户创建 Windows Server 2016 虚拟机 (VM)。

> [!NOTE]  
> 本文中的屏幕截图已更新，以匹配 Azure Stack 版本 1808 中引入的用户界面。 除了非托管磁盘外，1808 版还添加了对使用*托管磁盘*的支持。 如果使用早期版本，则某些图像（如磁盘选择）将与本文中显示的不同。  


## <a name="sign-in-to-the-azure-stack-portal"></a>登录到 Azure Stack 门户

登录到 Azure Stack 门户。 Azure Stack 门户的地址取决于要连接到的 Azure Stack 产品：

* 对于 Azure Stack 开发工具包 (ASDK)，请转到 https://portal.local.azurestack.external 。
* 对于 Azure Stack 集成系统，请转到 Azure Stack 运营商提供的 URL。

## <a name="create-a-vm"></a>创建 VM

1. 单击“+ 创建资源” > “计算” > “Windows Server 2016 Datacenter - 即用即付” > “创建”。 <br> 如果未看到“Windows Server 2016 Datacenter - 即用即付”项，请联系 Azure Stack 运营商，并根据[将 Windows Server 2016 VM 映像添加到 Azure Stack 市场](../operator/azure-stack-create-and-publish-marketplace-item.md)一文中所述，请求运营商将此映像添加到市场。

    ![在门户中创建 Windows VM 的步骤](media/azure-stack-quick-windows-portal/image01.png)

2. 在“基本信息”下，键入**名称**、**用户名**和**密码**。 选择“订阅”。 创建一个**资源组**或选择现有资源组，选择一个**位置**，然后单击“确定”。

    ![配置基本设置](media/azure-stack-quick-windows-portal/image02.png)

3. 在“大小”下选择“D1 标准”，然后单击“选择”。  

    ![选择 VM 大小](media/azure-stack-quick-windows-portal/image03.png)

4. 在“设置”页上，对默认设置进行任何所需的更改。
   - 从 Azure Stack 版本1808 开始，可以配置**存储**，可以在其中选择使用“托管磁盘”。 在 1808 之前的版本中，只能使用非托管磁盘。  

   ![配置 VM 设置](media/azure-stack-quick-windows-portal/image04.png)  

   配置准备就绪后，选择“确定”以继续。

5. 在“摘要”下，单击“确定”创建 VM。
    ![查看摘要和创建 VM](media/azure-stack-quick-windows-portal/image05.png)

6. 若要查看新 VM，请单击“所有资源”，搜索该 VM 名称，然后在搜索结果中将其选中。

    ![查看 VM](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>清理资源

使用完 VM 后，请删除 VM 及其资源。 为此，请选择 VM 页上的资源组，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已部署了一台基本的 Windows Server VM。 若要了解 Azure Stack VM 的详细信息，请继续阅读 [Azure Stack 中 VM 的注意事项](azure-stack-vm-considerations.md)。
