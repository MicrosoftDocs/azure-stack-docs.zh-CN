---
title: Azure Stack Hub 支持的来宾操作系统
titleSuffix: Azure Stack
description: 了解在 Azure Stack Hub 上可以使用哪些来宾操作系统。
author: sethmanheim
ms.topic: article
ms.date: 12/15/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/06/2019
ms.openlocfilehash: 3d535893b75cdaf49b4003fce2382ab092b328fe
ms.sourcegitcommit: f30e5178e0b4be4e3886f4e9f699a2b51286e2a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97620579"
---
# <a name="guest-operating-systems-supported-on-azure-stack-hub"></a>Azure Stack Hub 支持的来宾操作系统

## <a name="windows"></a>Windows

Azure Stack Hub 支持下表中列出的 Windows 来宾操作系统：

| 操作系统 | 说明 | 在 Azure Stack Hub 市场中提供 |
| --- | --- | --- |
| Windows Server，版本 1709 | 64 位 | 包含容器的核心 |
| Windows Server 2019 | 64 位 |  数据中心、数据中心核心、包含容器的数据中心 |
| Windows Server 2016 | 64 位 |  数据中心、数据中心核心、包含容器的数据中心 |
| Windows Server 2012 R2 | 64 位 |  数据中心 |
| Windows Server 2012 | 64 位 |  数据中心 |
| Windows Server 2008 R2 SP1 | 64 位 |  数据中心 |
| Windows Server 2008 SP2 | 64 位 |  自带映像 |
| Windows 10 *（请参见注释 1）* | 64 位，Pro 和 Enterprise | 自带映像 |

> [!NOTE]
> 若要在 Azure Stack Hub 上部署 Windows 10 客户端操作系统，必须拥有 [windows 每用户许可](https://www.microsoft.com/licensing/product-licensing/windows10.aspx) 或通过合格的多 [租户宿主 (QMTH) ](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)购买。

Marketplace 映像适用于即用即付或 BYOL (EA/SPLA) 许可。 不支持在单个 Azure Stack 集线器实例上同时使用这两者。 在部署期间，Azure Stack Hub 会将适当版本的来宾代理注入到映像中。

Datacenter 版本可在 Azure Stack Hub 市场中下载；客户可以自带包含其他版本的服务器映像。 Azure Stack Hub 市场中未提供 Windows 客户端映像。

## <a name="linux"></a>Linux

Azure Stack Hub 市场中列为可用的 Linux 发行版包括必要的 Windows Azure Linux 代理 (WALA)。 如果将自己的映像带到 Azure Stack，请遵循[将 Linux 映像添加到 Azure Stack](azure-stack-linux.md) 中的指导。

> [!NOTE]
> 自定义映像应该使用最新的公共 WALA 版本（基于 1903 Azure Stack Hub 及更高版本，或者包含 1901/1902 修补程序）或 2.2.20 版本生成。 2\.2.20 之前的版本以及介于 2.2.21 和 2.2.34（含）之间的版本在 Azure Stack Hub 上可能无法正常运行。 在 Azure Stack Hub 1910 版及更高版本中，所有 Azure WALA 代理版本都可与 Azure Stack Hub 一起使用。
>
> Azure Stack Hub 1910 及更高版本不支持 [cloud-init](https://cloud-init.io/)。

| 分发 | 说明 | 发布者 | Azure Stack Hub 市场 |
| --- | --- | --- | --- |
| 基于 CentOS 的 6.9 | 64 位 | Rogue Wave | 是 |
| 基于 CentOS 的 7.5 | 64 位 | Rogue Wave | 是 |
| 基于 CentOS 的 7.3 | 64 位 | Rogue Wave | 是 |
| ClearLinux | 64 位 | ClearLinux.org | 是 |
| CoreOS Linux (Stable) |  64 位 | CoreOS | 是 |
| Debian 8 "Jessie" | 64 位 | credativ |  是 |
| Debian 9“Stretch” | 64 位 | credativ | 是 |
| Oracle Linux | 64 位 | Oracle | 是 |
| Red Hat Enterprise Linux 7.1（及更高版本） | 64 位 | Red Hat | 自带映像 |
| SLES 11SP4 | 64 位 | SUSE | 是 |
| SLES 12SP3 | 64 位 | SUSE | 是 |
| Ubuntu 14.04-LTS | 64 位 | Canonical | 是 |
| Ubuntu 16.04-LTS | 64 位 | Canonical | 是 |
| Ubuntu 18.04-LTS | 64 位 | Canonical | 是 |

有关 Red Hat Enterprise Linux 支持信息，请参阅 [Red Hat 和 Azure Stack Hub：常见问题解答](https://access.redhat.com/articles/3413531)。

## <a name="next-steps"></a>后续步骤

有关 Azure Stack Hub 市场的详细信息，请参阅以下文章：

- [下载市场项](azure-stack-download-azure-marketplace-item.md)  
- [创建和发布市场项](azure-stack-create-and-publish-marketplace-item.md)
