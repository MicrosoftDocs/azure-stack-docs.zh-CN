---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/16/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 8d92b379a2c3c0e77fc3e682121963afcc8be31d
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867262"
---
1. 登录到 Azure Stack Hub 用户门户。

    如果你是创建平台磁盘的云操作员，请按照[添加平台映像](../operator/azure-stack-add-vm-image.md#add-a-platform-image)中的说明操作，通过管理员门户或管理员终结点添加 VHD。

2. 在用户门户中，选择“所有服务” > “磁盘” > “添加”。

3. 在“创建托管磁盘”中：

    1. 键入映像的“名称”。
    2. 选择 **订阅**。
    3. 创建映像，或将映像添加到资源组。
    4. 选择 ASDK 的“位置”（也称为区域）。
    5. 选择“帐户类型”。
        - 高级磁盘 (SSD) 基于固态硬盘，提供一致的低延迟性能。 高级磁盘可在价格与性能之间实现最佳平衡，非常适合用于 I/O 密集型应用程序和生产工作负荷。  
        - 标准磁盘 (HDD) 基于磁驱动器，适用于不经常访问数据的应用程序。 区域冗余存储 (ZRS) 支持区域冗余存储，可跨多个区域复制数据，即使单个区域关闭也可用。

    6. 对“源类型”选择“存储 blob” 。 会通过存储帐户中的 blob 创建磁盘。
    7. 对于 VHD 源，请选择：
        1. 存储帐户所在的源订阅。
        1. 选择“浏览”，然后导航到存储帐户、容器和 VHD。 选择“选择”  。
        1. 选择与 VHD 匹配的“OS 类型”。
    8. 选择等于或大于 VHD 的磁盘“大小(GiB)”。
    9. 选择“创建” 。

4. 创建磁盘后，便可以使用磁盘创建新 VM。