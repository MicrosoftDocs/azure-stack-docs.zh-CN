---
title: 在 Azure Stack Hub 中提供虚拟机规模集
description: 了解云操作员如何向 Azure Stack Hub 市场中添加虚拟机规模集。
author: sethmanheim
ms.topic: article
ms.date: 05/04/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2019
ms.openlocfilehash: d4383a9848ff2fd3e5f5da1fb71d88c71056bbd2
ms.sourcegitcommit: 21cdab346fc242b8848a04a124bc16c382ebc6f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777825"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack-hub"></a>在 Azure Stack Hub 中提供虚拟机规模集

虚拟机规模集是一种 Azure Stack Hub 计算资源。 可以使用规模集部署和管理一组相同的虚拟机 (VM)。 由于所有 VM 的配置方式相同，因此规模集不需要预配 VM。 可以更轻松构建面向大型计算、大数据、容器化工作负荷的大规模服务。

本文将指导你完成在 Azure Stack Hub 市场中提供规模集的过程。 完成此过程之后，用户将可以将虚拟机规模集添加到其订阅。

Azure Stack Hub 上的虚拟机规模集与 Azure 上的虚拟机规模集类似。 有关详细信息，请参阅以下视频：

* [Mark Russinovich talks Azure scale sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)（Mark Russinovich 谈论 Azure 规模集）
* [Guy Bowerman 介绍虚拟机规模集](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

在 Azure Stack Hub 上，虚拟机规模集不支持自动缩放。 可以使用资源管理器模板、CLI 或 PowerShell 将更多实例添加到规模集。

## <a name="prerequisites"></a>先决条件

* **Azure Stack Hub 市场：** 将 Azure Stack Hub 注册到全球 Azure 以启用 Azure Stack Hub 市场中项目的可用性。 请遵照[将 Azure Stack Hub 注册到 Azure](azure-stack-registration.md) 中的说明操作。
* **操作系统映像：** 在创建虚拟机规模集之前，必须从 [Azure Stack Hub 市场](azure-stack-download-azure-marketplace-item.md)下载 VM 映像，以便在规模集中使用。 必须已存在映像，然后用户才能创建新的规模集。

## <a name="use-the-azure-stack-hub-portal"></a>使用 Azure Stack Hub 门户

>[!IMPORTANT]  
> 当使用的是 Azure Stack Hub 版本 1808 或更高版本时，本部分中的信息适用。

1. 登录到 Azure Stack Hub 门户。 然后，依次转到“所有服务”和“虚拟机规模集”，并在“计算”下选择“虚拟机规模集”。    
   ![选择虚拟机规模集](media/azure-stack-compute-add-scalesets/all-services.png)

2. 选择“创建虚拟机规模集”。
   ![创建虚拟机规模集](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. 填写空字段，对于“操作系统磁盘映像”、“订阅”和“实例大小”，请从下拉列表中进行选择。    对于“使用托管磁盘”，请选择“是”。   然后单击“创建”  。
    ![配置和创建虚拟机规模集](media/azure-stack-compute-add-scalesets/create.png)

4. 若要查看新的虚拟机规模集，请转到“所有资源”  ，搜索该虚拟机规模集名称，然后在搜索结果中单击其名称。
   ![查看虚拟机规模集](media/azure-stack-compute-add-scalesets/search.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>更新虚拟机规模集中的映像

创建虚拟机规模集之后，用户无需重新创建规模集即可更新规模集中的映像。 更新映像的过程取决于以下场景：

1. 虚拟机规模集部署模板为 **version** 指定了 **latest**：  

   如果规模集模板 `imageReference` 节中的 `version` 设置为 **latest**，则对规模集执行的纵向扩展操作将会针对规模集实例使用映像的最新可用版本。 完成纵向扩展后，可以删除旧的虚拟机规模集实例。 `publisher`、`offer` 和 `sku` 的值保持不变。

   以下 JSON 示例指定了 `latest`：  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

2. 虚拟机规模集部署模板不会为 **version** 指定 **latest**，而是改为指定版本号：  

    如果 Azure Stack 操作员下载版本较新的映像（并删除旧版本），则规模集无法纵向扩展。 这是设计使然，因为规模集模板中指定的映像版本必须可用。  

有关详细信息，请参阅[操作系统磁盘和映像](../user/azure-stack-compute-overview.md#operating-system-disks-and-images)。  

## <a name="scale-a-virtual-machine-scale-set"></a>缩放虚拟机规模集

你可以更改虚拟机规模集的大小，使其更大或更小。

1. 在门户中，选择你的规模集，然后选择“缩放”  。

2. 使用滑动条为此虚拟机规模集设置新的缩放级别，然后单击“保存”。 

     ![缩放虚拟机规模集](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="next-steps"></a>后续步骤

* [将市场项从 Azure 下载到 Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)
