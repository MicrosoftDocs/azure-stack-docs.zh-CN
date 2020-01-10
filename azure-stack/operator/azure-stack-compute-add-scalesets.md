---
title: 使虚拟机规模集在 Azure Stack Hub 中可用 |Microsoft Docs
description: 了解云操作员如何将虚拟机规模集添加到 Azure Stack 中心市场。
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 906c004754bb5bd83283a2be738e812d96ee34bf
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75803783"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack-hub"></a>使虚拟机规模集在 Azure Stack 中心可用

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*
  
虚拟机规模集是一种 Azure Stack 集线器计算资源。 你可以使用它们来部署和管理一组相同的虚拟机（Vm）。 通过相同方式配置的所有 Vm，规模集无需预配 Vm。 构建面向大型计算、大数据和容器化工作负荷的大规模服务更容易。

本文将指导你完成在 Azure Stack Hub Marketplace 中提供规模集的过程。 完成此过程后，用户可以将虚拟机规模集添加到其订阅。

Azure Stack 集线器上的虚拟机规模集类似于 Azure 上的虚拟机规模集。 有关详细信息，请参阅下列视频：

* [Mark Russinovich talks Azure scale sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)（Mark Russinovich 谈论 Azure 规模集）
* [具有 Bowerman 的虚拟机规模集](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

在 Azure Stack 集线器上，虚拟机规模集不支持自动缩放。 可以使用资源管理器模板、CLI 或 PowerShell 将更多实例添加到规模集。

## <a name="prerequisites"></a>必备组件

* **Azure Stack 中心市场：** 向全球 Azure 注册 Azure Stack 集线器，以便在 Azure Stack 中心市场中实现项目的可用性。 按照将[Azure Stack 集线器注册到 Azure](azure-stack-registration.md)中的说明进行操作。
* **操作系统映像包：** 在可以创建虚拟机规模集之前，必须从[Azure Stack 中心市场](azure-stack-download-azure-marketplace-item.md)下载用于规模集的 VM 映像。 映像必须已存在，用户才能创建新的规模集。

## <a name="use-the-azure-stack-hub-portal"></a>使用 Azure Stack 中心门户

>[!IMPORTANT]  
> 本部分中的信息适用于使用 Azure Stack 集线器1808版或更高版本。 如果版本为1807或更早版本，请参阅[添加虚拟机规模集（在1808之前）](#add-the-virtual-machine-scale-set-prior-to-version-1808)。

1. 登录到 Azure Stack 中心门户。 然后，依次指向 "**所有服务**"、"**虚拟机规模集**"，然后在 "**计算**" 下，选择 "**虚拟机规模集**"。
   ![选择 "虚拟机规模集"](media/azure-stack-compute-add-scalesets/all-services.png)

2. 选择 "***创建虚拟机规模集***"。
   ![创建虚拟机规模集](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. 填写空字段，从下拉列表中选择 "**操作系统磁盘映像**"、"**订阅**" 和 "**实例大小**"。 对于 "**使用托管磁盘**"，选择 **"是"** 。 然后单击“创建”。
    ![配置和创建虚拟机规模集](media/azure-stack-compute-add-scalesets/create.png)

4. 若要查看新的虚拟机规模集，请在 "**所有资源**" 中搜索虚拟机规模集名称，然后在搜索中选择其名称。
   ![查看虚拟机规模集](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>添加虚拟机规模集（1808版之前）

>[!IMPORTANT]  
> 本部分中的信息适用于在1808之前使用 Azure Stack 集线器的版本。 如果使用版本1808或更高版本，请参阅[使用 Azure Stack 集线器门户](#use-the-azure-stack-hub-portal)。

1. 打开 Azure Stack 中心 Marketplace 并连接到 Azure。 选择 " **Marketplace 管理**"，然后单击 "**从 Azure 添加**"。

    ![Azure Stack 中心市场管理](media/azure-stack-compute-add-scalesets/image01.png)

2. 添加并下载虚拟机规模集 marketplace 项。

    ![虚拟机规模集 Marketplace 项](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>更新虚拟机规模集中的映像

创建虚拟机规模集后，用户可以在不需要重新创建规模集的情况下更新规模集中的图像。 更新映像的过程取决于以下方案：

1. 虚拟机规模集部署模板为版本指定**最新** **版本**：  

   当规模集模板的模板的 `imageReference` 部分中 `version` 设置为 "**最新**" 时，规模集的扩展操作将为规模集实例使用映像的最新可用版本。 向上扩展完成后，可以删除旧的虚拟机规模集实例。 `publisher`、`offer`和 `sku` 的值将保持不变。

   下面的 JSON 示例指定 `latest`：  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   在向上扩展可以使用新映像之前，必须下载该新映像：  

   * 当 Azure Stack 集线器 Marketplace 上的图像是比规模集中的映像更新的版本时，下载替换旧映像的新映像。 替换映像后，用户可以继续扩展。

   * 如果 Azure Stack 中心市场上的映像版本与规模集中的映像相同，请删除规模集中使用的映像，然后再下载新映像。 在删除原始映像和下载新映像之间的时间内，无法纵向扩展。

   此过程是使用1803版本引入的稀疏文件格式 resyndicate 映像所必需的。

2. 虚拟机规模集部署模板**未指定版本的最新** **版本**，请改为指定版本号：  

    如果下载的映像的版本较新（更改了可用的版本），则规模集无法向上扩展。 这是由设计决定的，因为规模集模板中指定的映像版本必须可用。  

有关详细信息，请参阅[操作系统磁盘和映像](../user/azure-stack-compute-overview.md#operating-system-disks-and-images)。  

## <a name="scale-a-virtual-machine-scale-set"></a>缩放虚拟机规模集

可以缩放虚拟机规模集的大小，使其更大或更小。

1. 在门户中，选择规模集，然后选择 "**缩放**"。

2. 使用滑动条为此虚拟机规模集设置新的缩放级别，然后单击 "**保存**"。

     ![缩放虚拟机集](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>删除虚拟机规模集

若要删除虚拟机规模集库项，请运行以下 PowerShell 命令：

```powershell  
Remove-AzsGalleryItem
```

> [!NOTE]
> 可能不会立即删除库项。 可能需要多次刷新门户，然后从 Azure Stack 中心市场将项目显示为已删除。

## <a name="next-steps"></a>后续步骤

* [将 marketplace 项从 Azure 下载到 Azure Stack 中心](azure-stack-download-azure-marketplace-item.md)
