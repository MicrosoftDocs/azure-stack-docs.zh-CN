---
title: 在 Azure Stack Hub 中使用缩放单元节点所需的知识耐用
description: 了解在 Azure Stack Hub 中使用缩放单元节点所需的知识耐用
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: b96bf9ecd1f46495da2aa4c2d49d66cc0aff1b53
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487793"
---
# <a name="required-knowledge-for-working-with-scale-unit-nodes-in-azure-stack-hub-ruggedized"></a>在 Azure Stack Hub 中使用缩放单元节点所需的知识耐用

若要完成 FRU 过程，必须熟悉并能够访问以下概念、指南和网站。

## <a name="privileged-access-workstation-and-the-privileged-endpoint"></a>特权访问工作站和特权终结点

特权访问工作站 (PAW) 是一种通过 Internet 和外部威胁向量保护的专用工作站。 它可以驻留在硬件生命周期主机上 (HLH) 或在外部的客户网络上，可路由访问 Azure Stack 集线器缩放单位节点。

若要访问 PAW，必须使用远程桌面登录。 从客户获取凭据和 IP 地址。

你还可以通过此计算机访问特权终结点 (PEP) 。

## <a name="azure-stack-hub-administrator-portal"></a>Azure Stack 中心管理员门户

获取客户的管理员门户凭据和 URL。
有关详细信息，请[](../../operator/azure-stack-manage-portals.md)参阅 
 [在 Azure Stack Hub 中](../../operator/azure-stack-manage-portals.md)使用管理员门户。


