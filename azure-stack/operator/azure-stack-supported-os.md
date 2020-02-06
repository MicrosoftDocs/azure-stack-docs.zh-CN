---
title: Azure Stack 集线器上支持的来宾操作系统
titleSuffix: Azure Stack
description: 了解可在 Azure Stack 集线器上使用哪些来宾操作系统。
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: c590aed504df1549e3c4a43ae282a304e579195a
ms.sourcegitcommit: 74ce7c12a93d47315d70427b02bcacbd3b44f854
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2020
ms.locfileid: "77037193"
---
# <a name="guest-operating-systems-supported-on-azure-stack-hub"></a>Azure Stack 集线器上支持的来宾操作系统

## <a name="windows"></a>Windows

Azure Stack 中心支持下表中列出的 Windows 来宾操作系统：

| 操作系统 | 说明 | 在 Azure Stack Hub Marketplace 中提供 |
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
> 若要在 Azure Stack Hub 上部署 Windows 10 客户端操作系统，必须拥有[windows 每用户许可](https://www.microsoft.com/licensing/product-licensing/windows10.aspx)或通过合格的多租户宿主（[QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)）购买。

Marketplace 映像适用于即用即付或 BYOL （EA/SPLA）许可。 不支持在单个 Azure Stack 集线器实例上同时使用这两者。 在部署过程中，Azure Stack 集线器会将适当版本的来宾代理注入到映像中。

数据中心版本可用于下载 Azure Stack 中心市场;客户可以引入自己的服务器映像，包括其他版本。 Windows 客户端映像在 Azure Stack Hub Marketplace 中不可用。

## <a name="linux"></a>Linux

在 Azure Stack Hub Marketplace 中列出的 Linux 分发版包含所需的 Microsoft Azure Linux 代理（WALA）。 如果你将自己的映像用于 Azure Stack，请按照[将 Linux 映像添加到 Azure Stack](azure-stack-linux.md)中的指南进行操作。

> [!NOTE]
> 自定义映像应由最新的公共 WALA 版本（在 1903 Azure Stack 集线器 build 和更高版本、1901/1902 修补程序）或版本2.2.20 生成。 2\.2.20 之前的版本以及2.2.21 和2.2.34 （含）之间的版本可能无法在 Azure Stack 集线器上正常工作。 在 Azure Stack 集线器1910及更高版本中，所有 Azure WALA 代理版本都适用于 Azure Stack 中心。
>
> Azure Stack 集线器1910及更高版本支持[cloud init](https://cloud-init.io/) 。

| 分布 | 说明 | 发布者 | Azure Stack 中心市场 |
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

有关 Red Hat Enterprise Linux 支持信息，请参阅[Red Hat 和 Azure Stack 中心：常见问题解答](https://access.redhat.com/articles/3413531)。

## <a name="next-steps"></a>后续步骤

有关 Azure Stack 中心 Marketplace 的详细信息，请参阅以下文章：

- [下载市场项](azure-stack-download-azure-marketplace-item.md)  
- [创建并发布 marketplace 项](azure-stack-create-and-publish-marketplace-item.md)
