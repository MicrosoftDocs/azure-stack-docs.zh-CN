---
title: 使用硬件生命周期主机所需的知识
description: 了解使用硬件生命周期主机所需的知识
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 3d3e650e9b6b1b6c37e2f265aa5c049246600c2f
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487776"
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


