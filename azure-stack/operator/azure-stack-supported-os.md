---
title: Azure Stack 支持的来宾操作系统 |Microsoft Docs
description: 这些来宾操作系统可用于 Azure Stack。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2019
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: 25a32b1d73818e988a8bdf7fb565d06b06d53d68
ms.sourcegitcommit: bb2bbfad8061f7677954f6ce5a435b4e6f9299b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74100017"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure Stack 上支持的来宾操作系统

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="windows"></a>Windows

Azure Stack 支持下表中列出的 Windows 来宾操作系统：

| 操作系统 | 描述 | 在 Marketplace 中提供 |
| --- | --- | --- |
| Windows Server，版本1709 | 64 位 | 包含容器的内核 |
| Windows Server 2019 | 64 位 |  数据中心、数据中心核心、包含容器的数据中心 |
| Windows Server 2016 | 64 位 |  数据中心、数据中心核心、包含容器的数据中心 |
| Windows Server 2012 R2 | 64 位 |  数据中心 |
| Windows Server 2012 | 64 位 |  数据中心 |
| Windows Server 2008 R2 SP1 | 64 位 |  数据中心 |
| Windows Server 2008 SP2 | 64 位 |  自带映像 |
| Windows 10 *（请参阅注释1）* | 64位、专业版和企业版 | 自带映像 |

> [!NOTE]
> 若要在 Azure Stack 上部署 Windows 10 客户端操作系统，必须拥有[windows 每用户许可](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx)或通过合格的多租户宿主（[QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)）购买。

Marketplace 映像适用于即用即付或 BYOL （EA/SPLA）许可。 不支持对单个 Azure Stack 实例使用这两者。 在部署期间，Azure Stack 会将适当版本的来宾代理注入到映像中。

Datacenter edition 在 marketplace 中提供，可供下载;客户可以引入自己的服务器映像，包括其他版本。 Windows 客户端映像在 marketplace 中不可用。

## <a name="linux"></a>Linux

在 marketplace 中列出的 Linux 分发版包括必要的 Microsoft Azure Linux 代理（WALA）。 如果你将自己的映像用于 Azure Stack，请按照[将 Linux 映像添加到 Azure Stack](azure-stack-linux.md)中的指南进行操作。

> [!NOTE]
> 自定义映像应以最新的公共 WALA 版本（在 1903 Azure Stack 生成和更高版本中，或通过1901/1902 修补程序）生成，或与版本2.2.20 一起生成。 2\.2.20 之前以及2.2.20 和2.2.35 （独占）之间的版本可能无法在 Azure Stack 上正常工作。
>
> Azure Stack 此时不支持[cloud init](https://cloud-init.io/) 。

| 分配 | 描述 | 发布者 | 市场 |
| --- | --- | --- | --- |
| 基于 CentOS 的6。9 | 64 位 | Rogue 波 | 是 |
| 基于 CentOS 的7。5 | 64 位 | Rogue 波 | 是 |
| 基于 CentOS 的 7.3 | 64 位 | Rogue 波 | 是 |
| ClearLinux | 64 位 | ClearLinux.org | 是 |
| CoreOS Linux (Stable) |  64 位 | CoreOS | 是 |
| Debian 8 "Jessie" | 64 位 | credativ |  是 |
| Debian 9 "Stretch" | 64 位 | credativ | 是 |
| Oracle Linux | 64 位 | Oracle | 是 |
| Red Hat Enterprise Linux 7.1 （及更高版本） | 64 位 | Red Hat | 自带映像 |
| SLES 11SP4 | 64 位 | SUSE | 是 |
| SLES 12SP3 | 64 位 | SUSE | 是 |
| Ubuntu 14.04-LTS | 64 位 | Canonical | 是 |
| Ubuntu 16.04-LTS | 64 位 | Canonical | 是 |
| Ubuntu 18.04-LTS | 64 位 | Canonical | 是 |

有关 Red Hat Enterprise Linux 支持信息，请参阅[Red Hat 和 Azure Stack：常见问题解答](https://access.redhat.com/articles/3413531)。

## <a name="next-steps"></a>后续步骤

有关 Azure Stack marketplace 的详细信息，请参阅以下文章：

- [下载市场项](azure-stack-download-azure-marketplace-item.md)  
- [创建并发布 marketplace 项](azure-stack-create-and-publish-marketplace-item.md)
