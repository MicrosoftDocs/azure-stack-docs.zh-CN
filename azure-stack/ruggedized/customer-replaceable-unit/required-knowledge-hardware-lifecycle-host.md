---
title: 使用硬件生命周期主机所需的知识
description: 了解使用硬件生命周期主机所需的知识
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: a53d2410c9b4ff7494877a39ec6e4c5405261f19
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99571154"
---
# <a name="required-knowledge-for-working-with-the-hardware-lifecycle-host"></a>使用硬件生命周期主机所需的知识

若要完成 FRU 过程，必须熟悉并能够访问以下概念和指南。

## <a name="hardware-lifecycle-host"></a>硬件生命周期主机

硬件生命周期主机 (HLH) 是位于 Azure Stack 中心机架顶部的物理管理服务器。 若要访问该主机，可以使用以下三种方法之一连接到它：

* 直接 (崩溃车) 
* iDRAC (服务端口) 
* iDRAC (IP 访问) 

如果位于数据中心内，则可以使用 VGA 和 USB 端口直接连接到 HLH。 例如，连接故障车。

如果位于数据中心内，请使用微型 USB 电缆将便携式计算机连接到 iDRAC 9 服务端口。 有关详细信息，请参阅通过直接 USB 连接访问 iDRAC 接口。

与客户合作，将 HLH 从其管理网络和管理工作站连接到 iDRAC IP。

> [!NOTE]
> 只有先前添加到交换机 Acl 的网络才能直接连接到 HLH iDRAC。

## <a name="credentials"></a>凭据

与客户合作，获取以下各项的凭据：

* HLH
* 主要区域中的
* iDRAC 帐户 (可选) 

具有完全管理员权限的 Windows 帐户。

如果未使用崩溃车直接连接到服务器，则需要 iDRAC 帐户凭据才能访问虚拟 KVM。

## <a name="dell-emc-poweredge-r640-installation-and-service-manual"></a>Dell EMC PowerEdge R640 安装和服务手册

有关物理替换相关硬件的详细信息，请参阅[Dell](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) 
 [EMC PowerEdge R640 安装和服务手册](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4)中的 PowerEdge R640 FRU 更换过程。
浏览到 " [安装和删除系统组件](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/installing-and-removing-system-components?guid=guid-5a5943c4-fe26-4faa-a10c-2afa4c1993ff&lang=en-us) " 部分。

## <a name="microsoft-azure-stack-hub-ruggedized-cloud-appliance-service-manual"></a>Microsoft Azure Stack 中心耐用云设备服务手册

Microsoft Azure Stack 集线器耐用 Cloud 设备 Service 手册包含用于删除硬件生命周期主机的说明。

## <a name="dell-emc-poweredge-idrac"></a>Dell EMC PowerEdge iDRAC

你必须了解如何导航并使用 Dell EMC PowerEdge iDRAC web 界面。

