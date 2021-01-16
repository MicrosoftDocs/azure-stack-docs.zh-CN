---
title: 使用战术性云设备中的缩放单位节点时所需的知识
description: 了解在战术性云设备中使用缩放单元节点所需的知识
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 4df7cfa84ff9b4a08d6751aa19ffdb1430a80605
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256126"
---
# <a name="required-knowledge-for-working-with-scale-unit-nodes-in-a-tactical-cloud-appliance"></a>使用战术性云设备中的缩放单位节点时所需的知识

若要完成 FRU 过程，必须熟悉并能够访问以下概念、指南和网站。

## <a name="privileged-access-workstation-and-the-privileged-endpoint"></a>特权访问工作站和特权终结点

特权访问工作站 (PAW) 是一种通过 Internet 和外部威胁向量保护的专用工作站。 它可以驻留在硬件生命周期主机上 (HLH) 或在外部的客户网络上，可路由访问 Azure Stack 集线器缩放单位节点。

若要访问 PAW，必须使用远程桌面登录。 从客户获取凭据和 IP 地址。

你还可以通过此计算机访问特权终结点 (PEP) 。
有关特权访问工作站以及 PEP 的详细信息，请参阅特权访问工作站和特权终结点访问。

## <a name="azure-stack-hub-administrator-portal"></a>Azure Stack 中心管理员门户

获取客户的管理员门户凭据和 URL。
有关详细信息， [](../../operator/azure-stack-manage-portals.md)请参阅 
 [在 Azure Stack Hub 中](../../operator/azure-stack-manage-portals.md)使用管理员门户。

## <a name="dell-emc-poweredge-r640-installation-and-service-manual"></a>Dell EMC PowerEdge R640 安装和服务手册

有关物理替换相关硬件的详细信息，请参阅 [DELL EMC PowerEdge R640 安装和服务手册](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4)中的安装和删除 PowerEdge R640 系统组件过程。
浏览到 "[安装和删除](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us) 
 [系统组件](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us)" 部分。

## <a name="microsoft-azure-stack-hub-tactical-cloud-appliance-service-manual"></a>Microsoft Azure Stack 中心战术性云设备服务手册

Microsoft Azure Stack 中心战术性云设备服务手册包含用于从 Tracewell 战术性箱中删除缩放单位节点服务器的说明。

## <a name="dell-emc-poweredge-idrac"></a>Dell EMC PowerEdge iDRAC

你必须了解如何导航并使用 Dell EMC PowerEdge iDRAC web 界面。 有关使用 iDRAC 的详细信息，请参阅 [集成 Dell 远程访问控制器9用户 \' 指南](https://www.dell.com/support/manuals/us/en/04/poweredge-r840/idrac9_4.00.00.00_ug_new/overview-of-idrac?guid=guid-a03c2558-4f39-40c8-88b8-38835d0e9003)。