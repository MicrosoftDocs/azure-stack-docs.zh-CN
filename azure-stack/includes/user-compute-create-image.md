---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 7da53434f8e4e11d3686e63b2e80672cdf575e21
ms.sourcegitcommit: 9a340b383dcf42c85bc6ec0d01ff3c9ae29dfe4c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89609751"
---
1. 登录到 Azure Stack Hub 用户门户。

    如果你是创建平台映像的云操作员，请按照 [添加平台映像](/azure-stack/operator/azure-stack-add-vm-image.md#add-a-platform-image) 中的说明，通过管理员门户或管理员终结点添加 VHD。

2. 在用户门户中，选择 "**所有服务**" "映像" "  >  **Images**  >  **添加**"。

3. 在 " **创建映像**" 中：

    1. 键入映像的 **名称** 。
    2. 选择 **订阅**。
    3. 创建映像，或将其添加到 **资源组**。
    4. 选择 ASDK 的 **位置**（也称为区域）。
    5. 选择与映像相匹配的 **OS 类型** 。
    6. 选择 " **浏览** "，然后导航到你的存储帐户、容器和 VHD。 选择“选择”  。
    5. 选择“帐户类型”。
        - ** (SSD) 的高级磁盘 ** 受固态驱动器支持，并提供一致的低延迟性能。 高级磁盘可在价格与性能之间实现最佳平衡，非常适合用于 I/O 密集型应用程序和生产工作负荷。  
        - **标准磁盘 (HDD) ** 支持磁盘驱动器，并且更适用于不经常访问数据的应用程序。 区域冗余存储 (ZRS) 支持区域冗余存储，可跨多个区域复制数据，即使单个区域关闭也可用。

    8. 选择 " **读取/写入** 主机捕获"。
    9. 选择“创建”。

4. 创建映像后，使用映像创建新 VM。