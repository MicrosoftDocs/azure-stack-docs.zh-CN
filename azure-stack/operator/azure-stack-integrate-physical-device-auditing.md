---
title: 将物理设备审核与 Azure Stack 中心数据中心集成
description: 了解如何将物理设备访问审核与 Azure Stack 中心数据中心集成。
author: ihenkel
ms.topic: article
ms.date: 06/10/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 0ebf71cc6ba794169965fb17181ae497913bf30b
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882103"
---
# <a name="integrate-physical-device-auditing-with-your-azure-stack-hub-datacenter"></a>将物理设备审核与 Azure Stack 中心数据中心集成

Azure Stack 集线器中的所有物理设备，如基板管理控制器（Bmc）和网络交换机，发出审核日志。 可以将审核日志集成到整个审核解决方案中。 由于不同 Azure Stack 中心 OEM 硬件供应商的设备有所不同，因此请与供应商联系以获取有关审核集成的文档。 以下各节提供了 Azure Stack 集线器中的物理设备审核的一些常规信息。  

## <a name="physical-device-access-auditing"></a>物理设备访问审核

Azure Stack 集线器中的所有物理设备都支持使用 TACACS 或 RADIUS。 支持包括对基板管理控制器（BMC）和网络交换机的访问。

Azure Stack 集线器解决方案不附带 RADIUS 或 TACACS 内置。 不过，解决方案已经过验证，可支持使用市场上可用的现有 RADIUS 或 TACACS 解决方案。

仅限 RADIUS，验证 Eap-mschapv2。 这表示使用 RADIUS 的最安全实现。 咨询 OEM 硬件供应商，在 Azure Stack 集线器解决方案随附的设备中启用 TACAS 或 RADIUS。

## <a name="syslog-forwarding-for-network-devices"></a>网络设备的 Syslog 转发

Azure Stack 集线器中的所有物理网络设备都支持 syslog 消息。 Azure Stack 集线器解决方案不附带 syslog 服务器。 但是，这些设备已经过验证，可支持向市场上提供的现有 syslog 解决方案发送消息。

Syslog 目标地址是为进行部署而收集的可选参数，但也可以在部署后添加。 咨询 OEM 硬件供应商，在网络设备上配置 syslog 转发。

## <a name="next-steps"></a>后续步骤

[服务策略](azure-stack-servicing-policy.md)
