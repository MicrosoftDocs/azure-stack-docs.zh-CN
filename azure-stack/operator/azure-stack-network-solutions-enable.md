---
title: 使用 Fortinet FortiGate 在 Azure Stack 集线器中提供网络解决方案
description: 了解如何在 Azure Stack 集线器中使用 Fortinet FortiGate 启用网络解决方案
author: mattbriggs
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 11104bc3cfd52b6e725420ad5fd71416c12a70a8
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698532"
---
# <a name="offer-a-network-solution-in-azure-stack-hub-with-fortinet-fortigate"></a>使用 Fortinet FortiGate 在 Azure Stack 集线器中提供网络解决方案

可以将 FortiGate 下一代防火墙添加到 Azure Stack 中心市场。 FortiGate 使用户能够创建虚拟专用网络（VPN）等网络解决方案来 Azure Stack 集线器和 VNET 对等互连。 网络虚拟设备（NVA）控制从外围网络到其他网络或子网的网络流量。 

有关 Azure Marketplace 中的 FortiGate 的详细信息，请参阅[Fortinet FortiGate 下一代防火墙单一 VM 解决方案](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm)。

## <a name="download-the-required-azure-stack-hub-marketplace-items"></a>下载所需的 Azure Stack 中心市场项

1.  打开 Azure Stack 中心管理员门户。

2.  选择 " **Marketplace 管理**" 并选择 "**从 Azure 添加**"。

3. 在 "搜索" 框中键入 `Forti`，然后双击 > 选择 "**下载**" 以获取以下项的最新可用版本： 
    - Fortinet FortiGate-Azure BYOL 的 VM
    - FortiGate NGFW-单 VM 部署（BYOL）

    ![Azure Stack 中心 FortiGate Fortinet](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

2.  等到 Marketplace 项的状态为 "已**下载**"。 下载项目可能需要几分钟时间。

    ![Azure Stack 中心 FortiGate Fortinet](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>后续步骤

[使用 FortiGate NVA 设置 Azure Stack 集线器的 VPN](../user/azure-stack-network-howto-vnet-to-onprem.md)  
[如何通过对等互连连接两个 Vnet](../user/azure-stack-network-howto-vnet-to-vnet.md)  
[如何使用 Fortinet FortiGate NVA 建立 VNET 到 VNET 的连接](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
