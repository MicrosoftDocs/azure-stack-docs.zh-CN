---
title: Azure Stack 集线器上的 IoT 中心概述
description: 了解 Azure Stack 集线器上的 IoT 中心资源提供程序。
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: 098168947a28214a3955a1961c80a2c06825126b
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064712"
---
# <a name="overview-of-iot-hub-on-azure-stack-hub"></a>Azure Stack 集线器上的 IoT 中心概述

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

通过 Azure Stack 集线器上的 IoT 中心，你可以创建混合 IoT 解决方案。 IoT 中心是一种托管服务，充当用于 IoT 应用程序与它所管理的设备之间的双向通信的中心消息中心。 你可以使用 Azure Stack 集线器上的 IoT 中心来构建 IoT 解决方案，在 IoT 设备与本地解决方案后端之间提供可靠且安全的通信。

## <a name="features"></a>功能

| 功能 | Azure 上的 IoT 中心 | Azure Stack 中心预览版上的 IoT 中心 (S2/S3)  |
|-|-|-|
|设备到云的遥测| ✔ | ✔ |
|云到设备的消息传送| ✔ | ✔ |
|每设备标识| ✔ | ✔ |
|消息路由 <sub>1</sub><sub>，4</sub>| ✔ | ✔ |
|HTTP、AMQP、MQTT 协议| ✔ | ✔ |
|多租户| ✔ | ✔ |
|监视和诊断| ✔ | ✔ |
|云到设备的消息传送| ✔ | ✔ |
|设备管理、设备克隆、模块克隆| ✔ | ✔ |
|克隆通知，设备生命周期事件| ✔ | ✔ |
|边缘分层部署| ✔ | 共同 |
|管理员门户 <sub>2</sub>| ✘ | ✔ |
|机密旋转 <sub>2</sub>| ✘ | ✔ |
|容量管理 <sub>2</sub>| ✘ | ✔ |
|备份 & 还原 <sub>3</sub>| ✘ | ✘ |
|DeviceConnected，DeviceDisconnected，ASC <sub>4</sub>| ✔ | ✘ |
|设备模块配置| ✔ | 共同 |
|设备流式处理、IoT 即插即用、作业、文件上传 <sub>5</sub>| ✔ | ✘ |
|使用事件网格<sub>4</sub>监视设备连接状态| ✔ | ✘ |
|故障转移 <sub>6</sub>| ✔ | ✘ |

<sub>1</sub> 限制为内置终结点、事件中心和存储。 Service Bus 在 Azure Stack 集线器上不可用。  
<sub>2</sub> 适用于操作员在 ASH 上管理 IoT 中心。  
<sub>3</sub> 备份在预览版中可用。 GA 将支持还原。  
<sub>4</sub> 依赖于 Azure Stack 集线器上不提供的其他服务。  
<sub>5</sub> 在路线图中引入 Azure Stack 中心。  
<sub>6</sub> 在 Azure Stack 集线器上不适用。  

## <a name="api-available-for-iot-hub-on-azure-stack-hub"></a>API 可用于 Azure Stack 集线器上的 IoT 中心

|API|Azure Stack 集线器上的 IoT 中心|
|-|-|
|在设备上应用配置| ✔ |
| 配置创建 | ✔ |
| 配置删除 | ✔ |
| 配置读取 | ✔ |
|配置读取多个| ✔ |
|配置服务应用|  ✔ |
|配置更新|  ✔ |
|设备直接调用方法|  ✔ |
|GetDeviceAndModuleInScope|  ✔ |
|GetDevicesAndModulesInScope| ✔ |
|注销设备| ✔ |
|获取设备| ✔ |
|更新模块克隆| ✔ |
|D2C 获取克隆| ✔ |
|导入设备| ✔ |
|获取克隆| ✔ |
|取消注册模块| ✔ |
|更新设备| ✔ |
|更新模块| ✔ |
|查询设备| ✔ |
|导出设备| ✔ |
|备份和还原– ADM| ✔ |
|替换克隆| ✔ |
|备份和还原– DSS| ✔ |
|D2C 通知 DesiredProperties| ✔ |
|D2C 修补程序 ReportedProperties| ✔ |
|获取模块克隆| ✔ |
|模块 D2C 获取克隆| ✔ |
|获取模块| ✔ |
|模块 D2C 通知 DesiredProperties| ✔ |
|模块 D2C 修补 ReportedProperties| ✔ |
|Module Direct Invoke 方法| ✔ |
|更新克隆| ✔ |
|大容量设备操作| ✔ |
|设备到云的遥测| ✔ |
|注册设备| ✔ |
|注册模块| ✔ |
|替换模块克隆| ✔ |
|GenericAuthentication| ✔ |
|获取设备| ✔ |
|分区移动/角色更改| ✔ |

## <a name="differences-between-iot-hub-on-azure-cloud-and-iot-hub-on-azure-stack"></a>Azure Stack 上的 Azure 云和 IoT 中心上的 IoT 中心之间的差异

| 方面 | 云上的 IoT 中心 | 堆栈上的 IoT 中心 |
|-|-|-|
| 消息消耗 | https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin |默认情况下，消息将路由到与事件中心兼容的内置面向服务的终结点 (messages/events) 中。 在 Azure 云上，你可以通过提供 IoT 中心连接字符串或事件中心连接字符串从终结点访问消息。 但是，在 Azure Stack 集线器上，只支持事件中心连接字符串。 |

## <a name="next-steps"></a>后续步骤

如果 IoT 中心在订阅中不可用，请与管理员联系，以 [在 Azure Stack 中心资源提供程序上安装 Iot 中心](../operator/iot-hub-rp-overview.md)。

若要了解如何使用 IoT 中心，请参阅 [Azure Iot 中心文档](/azure/iot-hub/)。

