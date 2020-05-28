---
title: Azure Stack Hub 网络简介
description: 了解 Azure Stack Hub 网络
author: mattbriggs
ms.topic: conceptual
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 1e850e766300ff4d1b24328ba47c8458a903732a
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111942"
---
# <a name="introduction-to-azure-stack-hub-networking"></a>Azure Stack Hub 网络简介

Azure Stack Hub 提供可结合使用或单独使用的各种网络功能：

- **Azure Stack Hub 资源之间的连接**  
    在云中的安全专用虚拟网络内将 Azure 资源连接在一起。
- **Internet 连接**  
    通过 Internet 与 Azure Stack Hub 资源进行通信。
- **本地连接**  
    在 Internet 上通过虚拟专用网络 (VPN) 或者通过与 Azure Stack Hub 建立的专用连接将本地网络连接到 Azure Stack Hub 资源。
- 负载均衡和流量方向   
    对发往同一位置中的服务器的流量进行负载均衡，并将流量定向到不同位置中的服务器。
- **安全性**  
    筛选网络子网或单个 VM 之间的网络流量。
- **路由**  
    使用默认路由，或者完全控制 Azure Stack Hub 与本地资源之间的路由。
- **可管理性**  
    监视和管理 Azure Stack Hub 网络资源。
- **部署和配置工具**  
    使用基于 Web 的门户或跨平台命令行工具来部署和配置网络资源。


## <a name="next-steps"></a>后续步骤

* [Azure Stack Hub 网络注意事项](azure-stack-network-differences.md)