---
title: Azure Stack 集线器网络简介 |Microsoft Docs
description: 了解 Azure Stack 集线器网络
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 0682a698dadf79feb2e22eaa683b723e7ae23717
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883348"
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