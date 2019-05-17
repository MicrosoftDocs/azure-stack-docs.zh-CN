---
title: Azure Stack 快速入门 - 创建 Windows 虚拟机
description: Azure Stack 快速入门 - 使用门户创建 Windows VM
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
ms.openlocfilehash: a25af39c5fd055989b09e4cf065d0e78bf88fc25
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782815"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>快速入门：使用 Azure Stack 门户创建 Windows Server 虚拟机

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 Azure Stack 门户创建 Windows Server 2016 虚拟机。 请按照本文中的步骤创建和使用虚拟机。

> [!NOTE]  
> 本文中的屏幕图像已更新，以匹配 Azure Stack 版本 1808 中引入的用户界面。 除了非托管磁盘外，1808 版还添加了对使用*托管磁盘*的支持。 如果使用早期版本，则某些图像（如磁盘选择）将与本文中显示的不同。  


## <a name="sign-in-to-the-azure-stack-portal"></a>登录到 Azure Stack 门户

登录到 Azure Stack 门户。 Azure Stack 门户的地址取决于要连接到的 Azure Stack 产品：

* 对于 Azure Stack 开发工具包 (ASDK)，请转到： https://portal.local.azurestack.external。
* 对于 Azure Stack 集成系统，请转到 Azure Stack 运营商提供的 URL。

## <a name="create-a-virtual-machine"></a>创建虚拟机

1. 单击“+ 创建资源” > “计算” > “Windows Server 2016 Datacenter - 即用即付” > “创建”。 如果未看到“Windows Server 2016 Datacenter - 即用即付”项，请联系 Azure Stack 运营商。 根据[将 Windows Server 2016 VM 映像添加到 Azure Stack 市场](../operator/azure-stack-create-and-publish-marketplace-item.md)一文中所述，请求运营商将此映像添加到市场。

    ![在门户中创建 Windows 虚拟机的步骤](media/azure-stack-quick-windows-portal/image01.png)
2. 在“基本信息”下，键入**名称**、**用户名**和**密码**。 选择“订阅”。 创建一个**资源组**或选择现有资源组，选择一个**位置**，然后单击“确定”。

    ![配置基本设置](media/azure-stack-quick-windows-portal/image02.png)
3. 在“大小”下选择“D1 标准”，然后单击“选择”。  
    ![选择虚拟机大小](media/azure-stack-quick-windows-portal/image03.png)

4. 在“设置”页上，对默认设置进行任何所需的更改。
   - 从 Azure Stack 版本1808 开始，可以配置**存储**，可以在其中选择使用“托管磁盘”。 在版本 1808 之前，只能使用非托管磁盘。  
   ![配置虚拟机设置](media/azure-stack-quick-windows-portal/image04.png)  
   配置准备就绪后，选择“确定”以继续。

5. 在“摘要”下，单击“确定”创建虚拟机。
    ![查看摘要和创建虚拟机](media/azure-stack-quick-windows-portal/image05.png)

6. 若要查看新虚拟机，请单击“所有资源”，搜索该虚拟机名称，然后在搜索结果中单击其名称。
    ![查看虚拟机](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>清理资源

使用完虚拟机后，请删除虚拟机及其资源。 为此，请在虚拟机页上选择该资源组，并单击“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，已部署了一个基本 Windows Server 虚拟机。 有关 Azure Stack 虚拟机的详细信息，请转到 [Azure Stack 中虚拟机的注意事项](azure-stack-vm-considerations.md)。
