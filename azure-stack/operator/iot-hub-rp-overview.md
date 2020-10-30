---
title: Azure Stack 集线器上的 IoT 中心概述
description: 了解 Azure Stack 集线器上的 IoT 中心资源提供程序和 IoT 中心 Azure 托管版本之间的差异。
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: adf6635fbdcf62398218a8e6ad0ce061900e0059
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93049667"
---
# <a name="iot-hub-on-azure-stack-hub-overview"></a>Azure Stack 集线器上的 IoT 中心概述

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

通过 Azure Stack 集线器上的 IoT 中心，你可以创建混合 IoT 解决方案。 IoT 中心是一种托管服务，充当用于 IoT 应用程序与它所管理的设备之间的双向通信的中心消息中心。 你可以使用 Azure Stack 集线器上的 IoT 中心来构建 IoT 解决方案，在 IoT 设备与本地解决方案后端之间提供可靠且安全的通信。 

## <a name="differences-between-azure-iot-hub-and-iot-hub-on-azure-stack-hub"></a>Azure Stack 集线器上的 Azure IoT 中心和 IoT 中心之间的差异

| 功能 | Azure IoT 中心 | Azure Stack 集线器上的 IoT 中心 |
|-|-|-|
| 缩放 | https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling | 支持 S2 和 S3 IoT 中心|
| 服务更新 | 自动 | 手动 |

## <a name="iot-hub-throttling"></a>IoT 中心限制

Azure Stack 中心 S2 和 S3 Sku 上的 IoT 中心的限制和配额与 Azure 上的 IoT 中心相同。 有关更多详细信息，请参阅 [Azure IoT 中心配额和限制](/azure/iot-hub/iot-hub-devguide-quotas-throttling#quotas-and-throttling) 。

## <a name="next-steps"></a>后续步骤

若要准备在 Azure Stack Hub 上安装 IoT 中心，请查看 [必备项](iot-hub-rp-prerequisites.md) 文档。

若要详细了解连接和断开连接 Azure Stack 中心之间的差异，请查看 [Azure Stack 连接模型](azure-stack-connection-models.md)。
