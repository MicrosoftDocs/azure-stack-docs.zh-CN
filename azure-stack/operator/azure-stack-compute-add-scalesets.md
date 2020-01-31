---
title: 使虚拟机规模集在 Azure Stack 中心可用
description: 了解云操作员如何将虚拟机规模集添加到 Azure Stack 中心市场。
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 7474b18a1d50c6ab8ed91be64f8f943a215ff7fd
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2020
ms.locfileid: "76889739"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack-hub"></a>使虚拟机规模集在 Azure Stack 中心可用

虚拟机规模集是一种 Azure Stack 集线器计算资源。 可以使用规模集来部署和管理一组相同的虚拟机（Vm）。 通过相同方式配置的所有 Vm，规模集无需预配 Vm。 构建面向大型计算、大数据和容器化工作负荷的大规模服务更容易。

本文将指导你完成在 Azure Stack Hub Marketplace 中提供规模集的过程。 完成此过程后，用户可以将虚拟机规模集添加到其订阅。

Azure Stack 集线器上的虚拟机规模集类似于 Azure 上的虚拟机规模集。 有关详细信息，请参阅以下视频：

* [Mark Russinovich talks Azure scale sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)（Mark Russinovich 谈论 Azure 规模集）
* [具有 Bowerman 的虚拟机规模集](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

在 Azure Stack 集线器上，虚拟机规模集不支持自动缩放。 可以使用资源管理器模板、CLI 或 PowerShell 将更多实例添加到规模集。

## <a name="prerequisites"></a>必备组件

* **Azure Stack 中心市场：** 向全球 Azure 注册 Azure Stack 集线器，以启用 Azure Stack 中心市场中的项的可用性。 按照将[Azure Stack 集线器注册到 Azure](azure-stack-registration.md)中的说明进行操作。
* **操作系统映像包：** 在可以创建虚拟机规模集之前，必须从[Azure Stack 中心市场](azure-stack-download-azure-marketplace-item.md)下载用于规模集的 VM 映像。 映像必须已存在，用户才能创建新的规模集。

## <a name="use-the-azure-stack-hub-portal"></a>使用 Azure Stack 中心门户

>[!IMPORTANT]  
> 本部分中的信息适用于使用 Azure Stack 集线器1808版或更高版本。

1. 登录到 Azure Stack 中心门户。 然后，依次指向 "**所有服务**"、"**虚拟机规模集**"，然后在 "**计算**" 下，选择 "**虚拟机规模集**"。
   ![选择 "虚拟机规模集"](media/azure-stack-compute-add-scalesets/all-services.png)

2. 选择 "***创建虚拟机规模集***"。
   ![创建虚拟机规模集](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. 填写空字段，从下拉列表中选择 "**操作系统磁盘映像**"、"**订阅**" 和 "**实例大小**"。 对于 "**使用托管磁盘**"，选择 **"是"** 。 然后单击“创建”。
    ![配置和创建虚拟机规模集](media/azure-stack-compute-add-scalesets/create.png)

4. 若要查看新的虚拟机规模集，请在 "**所有资源**" 中搜索虚拟机规模集名称，然后在搜索中选择其名称。
   ![查看虚拟机规模集](media/azure-stack-compute-add-scalesets/search.png)

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

2. 虚拟机规模集部署模板**未指定版本的最新** **版本**，请改为指定版本号：  

    如果 Azure Stack 运算符下载具有较新版本的映像（并删除旧版本），则规模集无法向上扩展。 这是由设计决定的，因为规模集模板中指定的映像版本必须可用。  

有关详细信息，请参阅[操作系统磁盘和映像](../user/azure-stack-compute-overview.md#operating-system-disks-and-images)。  

## <a name="scale-a-virtual-machine-scale-set"></a>缩放虚拟机规模集

可以缩放虚拟机规模集的大小，使其更大或更小。

1. 在门户中，选择规模集，然后选择 "**缩放**"。

2. 使用滑动条为此虚拟机规模集设置新的缩放级别，然后单击 "**保存**"。

     ![缩放虚拟机集](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="next-steps"></a>后续步骤

* [将 marketplace 项从 Azure 下载到 Azure Stack 中心](azure-stack-download-azure-marketplace-item.md)
