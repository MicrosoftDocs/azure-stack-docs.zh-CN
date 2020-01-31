---
title: Azure Stack 集线器网络简介
description: 了解 Azure Stack 集线器网络
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: c23d005869e83b46ac73be8660adc3056dcc52e0
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884549"
---
# <a name="introduction-to-azure-stack-hub-networking"></a>Azure Stack 集线器网络简介

Azure Stack 中心提供不同种类的网络功能，这些功能可一起使用或单独使用：

- **Azure Stack 集线器资源之间的连接**  
    在云中的安全专用虚拟网络中将 Azure 资源连接在一起。
- **Internet 连接**  
    通过 internet 与 Azure Stack 集线器资源通信。
- **本地连接**  
    通过 internet 上的虚拟专用网络（VPN）或者通过与 Azure Stack 中心的专用连接将本地网络连接到 Azure Stack 集线器资源。
- 负载均衡和流量方向  
    对发往同一位置中的服务器的流量进行负载均衡，并将流量定向到不同位置中的服务器。
- **安全性**  
    筛选网络子网或单个 Vm 之间的网络流量。
- **路由**  
    在 Azure Stack 中心和本地资源之间使用默认路由或完全控制路由。
- **可管理性**  
    监视和管理 Azure Stack 集线器网络资源。
- **部署和配置工具**  
    使用基于 Web 的门户或跨平台命令行工具来部署和配置网络资源。


## <a name="next-steps"></a>后续步骤

* [Azure Stack 集线器网络的注意事项](azure-stack-network-differences.md)