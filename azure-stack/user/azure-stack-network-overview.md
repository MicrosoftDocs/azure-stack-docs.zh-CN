---
title: Azure Stack 网络简介 | Microsoft Docs
description: 了解 Azure Stack 网络
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: e5e76c3edcaa93e412d2715d7dc732731ac3d2ce
ms.sourcegitcommit: b36d078e699c7924624b79641dbe9021af9606ba
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67816133"
---
# <a name="introduction-to-azure-stack-networking"></a>Azure Stack 联网简介

适用对象：*Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 提供不同类型的可以单独或一起使用的网络功能：

- **Azure Stack 资源之间的连接**  
    在云中的安全且私密的虚拟网络中 Azure 资源连接在一起。
- **Internet 连接**  
    通过 internet 进行通信传入和传出 Azure Stack 资源。
- **本地连接**  
    在本地网络连接到 Azure Stack 资源通过虚拟专用网络 (VPN) 通过 internet，或通过专用连接到 Azure Stack。
- 负载均衡和流量方向   
    对发往同一位置中的服务器的流量进行负载均衡，并将流量定向到不同位置中的服务器。
- **安全性**  
    筛选网络子网或单独的 Vm 之间的网络流量。
- **路由**  
    使用默认路由，或者完全控制 Azure Stack 与本地资源之间的路由。
- **可管理性**  
    监视和管理 Azure Stack 网络资源。
- **部署和配置工具**  
    使用基于 Web 的门户或跨平台命令行工具来部署和配置网络资源。


## <a name="next-steps"></a>后续步骤

* [Azure Stack 网络注意事项](azure-stack-network-differences.md)