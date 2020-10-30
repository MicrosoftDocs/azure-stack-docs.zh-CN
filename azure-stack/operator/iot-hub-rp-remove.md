---
title: 如何在 Azure Stack 集线器上删除 IoT 中心
description: Azure Stack 集线器上卸载 IoT 中心的说明
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: 4095336f6a18441512e8c1052051d6e1608d904a
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93049668"
---
# <a name="how-to-remove-iot-hub-on-azure-stack-hub"></a>如何在 Azure Stack 集线器上删除 IoT 中心

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

本文提供了有关如何在 Azure Stack 集线器上删除 IoT 中心资源提供程序的说明。 此过程大约需37分钟。

> [!WARNING]
> 卸载 IoT 中心后， **_将删除所有设备和数据_** 。 此操作 **不可** 恢复。

## <a name="uninstalling-iot-hub"></a>正在卸载 IoT 中心

1) 请参阅 **Marketplace 管理** 。 IoT 中心将在列表中，并标记为 "已安装"。 单击 " **IoT 中心** "。

    [![资源提供程序列表](../operator/media/iot-hub-rp-remove/uninstall1.png)](../operator/media/iot-hub-rp-remove/uninstall1.png#lightbox)

2) 单击 " **IoT 中心** " 下的 " **卸载** "，提供资源提供程序名称 " **iothub** "，并单击其下方的 " **卸载** " 按钮。

    [![卸载 IoT 中心并确认](../operator/media/iot-hub-rp-remove/uninstall2.png)](../operator/media/iot-hub-rp-remove/uninstall2.png#lightbox)

3) 等待卸载完成。 再次尝试安装 IoT 中心之前，请至少等待10分钟。

>[!IMPORTANT]
>依赖关系 (例如。 将 **不** 会卸载事件中心) 。 如果要卸载/删除 marketplace 中的任何依赖项，则需要单独执行此操作。

## <a name="next-steps"></a>后续步骤

若要重新安装，请参阅 [在连接的 Azure Stack 上安装 IoT 中心资源提供程序](iot-hub-rp-install.md)。
