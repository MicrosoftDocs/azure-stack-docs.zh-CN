---
title: 在 Azure Stack 中提供虚拟机规模集 | Microsoft Docs
description: 了解云操作员如何向 Azure Stack 市场中添加虚拟机规模集
services: azure-stack
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 06/04/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 98aae8378e4bd516181cae95b153e6ee445ce4bf
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692001"
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>在 Azure Stack 中提供虚拟机规模集

*适用于：* Azure Stack 集成系统和 Azure Stack 开发工具包
  
虚拟机规模集是一种 Azure Stack 计算资源。 可以使用它们部署和管理一组相同的虚拟机。 由于所有虚拟机的配置方式相同，因此规模集不需要预配虚拟机。 可以更方便地构建面向大型计算、大数据、容器化工作负荷的大规模服务。

本文将指导您完成使规模集在 Azure Stack marketplace 中可用的过程。 完成此过程之后，用户将可以将虚拟机规模集添加到其订阅。

Azure Stack 上的虚拟机规模集与 Azure 上的虚拟机规模集类似。 有关详细信息，请参阅以下视频：

* [Mark Russinovich talks Azure scale sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)（Mark Russinovich 谈论 Azure 规模集）
* [虚拟机规模集 Guy bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

在 Azure Stack 上，虚拟机规模集不支持自动缩放。 可以使用资源管理器模板、CLI 或 PowerShell 将更多实例添加到规模集。

## <a name="prerequisites"></a>必备组件

* **市场：** 将 Azure Stack 注册到全球 Azure 以启用市场中项目的可用性。 请遵照[将 Azure Stack 注册到 Azure](azure-stack-registration.md) 中的说明操作。
* **操作系统映像：** 可以创建虚拟机规模集之前，你必须下载在规模集从中使用的 VM 映像[Azure Stack marketplace](azure-stack-download-azure-marketplace-item.md)。 映像必须已经存在之前用户可以创建新的规模集。

## <a name="use-the-azure-stack-portal"></a>使用 Azure Stack 门户

>[!IMPORTANT]  
> 当使用的是 Azure Stack 版本 1808 或更高版本时，本部分中的信息适用。 如果版本为 1807 或更低版本，请参阅[添加虚拟机规模集（1808 之前的版本）](#add-the-virtual-machine-scale-set-prior-to-version-1808)。

1. 登录到 Azure Stack 门户。 然后，依次转到“所有服务”和“虚拟机规模集”，并在“计算”下选择“虚拟机规模集”。    
   ![选择虚拟机规模集](media/azure-stack-compute-add-scalesets/all-services.png)

2. 选择“创建虚拟机规模集”。
   ![创建虚拟机规模集](media/azure-stack-compute-add-scalesets/create-scale-set.png)

3. 填写空字段，对于“操作系统磁盘映像”、“订阅”和“实例大小”，请从下拉列表中进行选择。    对于“使用托管磁盘”，请选择“是”。   然后单击“创建”  。
    ![配置和创建](media/azure-stack-compute-add-scalesets/create.png)

4. 若要查看新的虚拟机规模集，请转到“所有资源”  ，搜索该虚拟机规模集名称，然后在搜索结果中单击其名称。
   ![查看规模集](media/azure-stack-compute-add-scalesets/search.png)

## <a name="add-the-virtual-machine-scale-set-prior-to-version-1808"></a>添加虚拟机规模集 （在之前版本 1808年）

>[!IMPORTANT]  
> 当使用的是 1808 之前的 Azure Stack 版本时，本部分中的信息适用。 如果使用的是版本 1808 或更高版本，请参阅[使用 Azure Stack 门户](#use-the-azure-stack-portal)。

1. 打开 Azure Stack marketplace，并连接到 Azure。 选择“市场管理”，然后单击“+ 从 Azure 添加”。  

    ![市场管理](media/azure-stack-compute-add-scalesets/image01.png)

2. 添加并下载虚拟机规模集 marketplace 项。

    ![虚拟机规模集](media/azure-stack-compute-add-scalesets/image02.png)

## <a name="update-images-in-a-virtual-machine-scale-set"></a>更新虚拟机规模集中的映像

创建虚拟机规模集之后，用户无需重新创建规模集即可更新规模集中的映像。 更新映像的过程取决于以下场景：

1. 虚拟机规模集部署模板为 **version** 指定了 **latest**：  

   当`version`设置为**最新**中`imageReference`规模模板部分设置，请在规模集上的向上扩展操作使用的图像的最新可用版本为规模集实例。 完成纵向扩展后，可以删除旧的虚拟机规模集实例。 `publisher`、`offer` 和 `sku` 的值保持不变。

   以下 JSON 示例指定了 `latest`：  

    ```json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   必须先下载新映像，然后纵向扩展才能使用新映像：  

   * 当在 marketplace 映像是较新版本比规模集中的映像时，下载新映像来替换旧映像。 替换映像后，用户可以继续纵向扩展。

   * 如果必须在规模集中的映像相同 marketplace 的映像版本，删除在规模集中使用的映像，然后下载新映像。 在删除原始映像之后、下载新映像之前，无法执行纵向扩展。

   需要执行此过程以重新合成利用稀疏文件格式（在版本 1803 中引入）的映像。

2. 虚拟机规模集部署模板不会为 **version** 指定 **latest**，而是改为指定版本号：  

    如果下载较新版本的映像（会更改可用版本），则无法纵向扩展规模集。 这是设计使然，因为规模集模板中指定的映像版本必须可用。  

有关详细信息，请参阅[操作系统磁盘和映像](../user/azure-stack-compute-overview.md#operating-system-disks-and-images)。  

## <a name="scale-a-virtual-machine-scale-set"></a>缩放虚拟机规模集

可缩放的虚拟机规模集，使其更大或较小的大小。

1. 在门户中，选择你的规模集，然后选择“缩放”  。

2. 使用滑动条设置新级别的扩展对于此虚拟机规模集，然后依次**保存**。

     ![缩放集](media/azure-stack-compute-add-scalesets/scale.png)

## <a name="remove-a-virtual-machine-scale-set"></a>删除虚拟机规模集

若要删除虚拟机规模集库项，请运行以下 PowerShell 命令：

```powershell  
Remove-AzsGalleryItem
```

> [!NOTE]
> 库项可能不会立即删除。 可能需要多次刷新门户，该项才会显示为已从市场中删除。

## <a name="next-steps"></a>后续步骤

* [将市场项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md)
