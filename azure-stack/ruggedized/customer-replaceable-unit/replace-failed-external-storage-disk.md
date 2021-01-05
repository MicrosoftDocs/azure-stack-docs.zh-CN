---
title: 替换发生故障的外部存储磁盘
description: 了解如何更换发生故障的外部存储磁盘
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 476801acccecb463e97d42f38067b2463cfbd86c
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874636"
---
# <a name="replacing-a-failed-external-storage-disk"></a>替换发生故障的外部存储磁盘

使用以下过程来替换失败的外部磁盘。

## <a name="prerequisites"></a>先决条件

1.  查看本指南开头的 *注释、注意事项和警告*

2.  查看处理预防措施。

3.  检查

    -   如果使用缩放单位节点，则使用战术性云设备中的缩放单位节点所需的知识

    -   如果使用的是硬件生命周期主机，则使用硬件生命周期主机所需的知识

4.  完成

    -   验证缩放单位节点的访问和运行状况（如果使用缩放单位节点）

    -   验证硬件生命周期主机访问和运行状况（如果使用的是硬件生命周期主机）

5.  完成

    -   如果使用缩放单位节点，请关闭缩放单位节点

    -   如果使用的是硬件生命周期主机，请关闭硬件生命周期主机

## <a name="steps"></a>步骤

1.  找到机架中的物理节点。

2.  验证磁盘型号。

    验证新磁盘型号是否在[Microsoft Azure Stack Hub 的集成系统14G 支持](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs#q%3Dsupport%20matrix%26sort%3Ddate%20descending%26f%3Alang%3D%5Ben%5D) 
     [矩阵](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs#q%3Dsupport%20matrix%26sort%3Ddate%20descending%26f%3Alang%3D%5Ben%5D)* 中列出为受支持。
    如果磁盘不在支持矩阵中，则必须请求其他替换项。
    
    > [!CAUTION]
    > 插入不在支持矩阵中的磁盘会导致新磁盘被隔离。
        
    若要检查模型，请找到标签，并验证组件是否在支持矩阵中。
    
3.  替换发生故障的外部存储磁盘。

    按照适用于规模单元节点或硬件生命周期主机的 [DELL EMC PowerEdge R640 安装和服务手册](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) 中的磁盘驱动器托架更换过程进行操作。
    
    还必须参阅删除磁盘驱动器的相关部分，并将磁盘驱动器安装到磁盘驱动器托架或磁盘驱动器适配器。
    
4.  更换磁盘后，根据支持矩阵验证固件是否受支持。 若要检索磁盘 \' s 固件版本，请执行以下操作：

    1.  使用可路由的 IP 地址或直接通过直接 USB 连接访问 iDRAC 接口登录到 iDRAC。

    1.  在顶部菜单中，选择 " **存储**"：

        ![](media/image-30.png)
    
    1.  找到被替换的磁盘并展开该项目。 将磁盘 **修订** 与支持矩阵中的修订版本进行比较。 如果磁盘不在支持矩阵中，则请与支持部门联系以解决问题。

        ![](media/image-31.png)
        
## <a name="next-steps"></a>后续步骤

如果使用缩放单位节点：

1.  完成验证缩放单位节点的运行状况

2.  如果使用的是硬件生命周期主机，请完成验证缩放单位节点磁盘运行状况：

    -   完成验证硬件生命周期主机运行状况
    
