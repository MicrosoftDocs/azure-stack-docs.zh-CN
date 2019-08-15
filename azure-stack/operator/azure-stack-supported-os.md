---
title: Azure Stack 支持的来宾操作系统 | Microsoft Docs
description: 在 Azure Stack 上可以使用这些来宾操作系统。
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
ms.openlocfilehash: 98baf8217bc245f7aa6ac7bc0e2b8f8a5284c6d6
ms.sourcegitcommit: 86e2b776383a9f761072199ec6b6b8a7d2e6b798
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69021845"
---
# <a name="guest-operating-systems-supported-on-azure-stack"></a>Azure Stack 支持的来宾操作系统

适用对象：*Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="windows"></a>Windows

Azure Stack 支持下表中列出的 Windows 来宾操作系统：

| 操作系统 | 描述 | 在市场中可用 |
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
> 若要在 Azure Stack 上部署 Windows 10 客户端操作系统，必须具备 [Windows 每用户授权](https://www.microsoft.com/Licensing/product-licensing/windows10.aspx)，或者通过合格多租户托管商 ([QMTH](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx)) 购买。

Marketplace 映像适用于即用即付或 BYOL (EA/SPLA) 许可。 不支持在单个 Azure Stack 实例上同时使用这两者。 在部署期间，Azure Stack 会将适当版本的来宾代理注入到映像中。

Datacenter 版本可在市场中下载；客户可以自带包含其他版本的服务器映像。 市场中未提供 Windows 客户端映像。

## <a name="linux"></a>Linux

市场中列为可用的 Linux 发行版包括必要的 Windows Azure Linux 代理 (WALA)。 如果将自己的映像带到 Azure Stack，请遵循[将 Linux 映像添加到 Azure Stack](azure-stack-linux.md) 中的指导。

> [!NOTE]
> 自定义映像应该使用最新的公共 WALA 版本（基于 1903 Azure Stack 及更高版本，或者包含 1901/1902 修补程序）或 2.2.20 版本生成。 2\.2.20 之前的版本以及介于 2.2.20 和 2.2.35 之间（排他）的版本在 Azure Stack 上可能无法正常运行。
>
> 目前 Azure Stack 不支持 [cloud-init](https://cloud-init.io/)。

| 分发组 | 描述 | 发布服务器 | 市场 |
| --- | --- | --- | --- |
| 基于 CentOS 的 6.9 | 64 位 | Rogue Wave | 是 |
| 基于 CentOS 的 7.5 | 64 位 | Rogue Wave | 是 |
| 基于 CentOS 的 7.3 | 64 位 | Rogue Wave | 是 |
| ClearLinux | 64 位 | ClearLinux.org | 是 |
| 容器 Linux |  64 位 | CoreOS | Stable |
| Debian 8 "Jessie" | 64 位 | credativ |  是 |
| Debian 9“Stretch” | 64 位 | credativ | 是 |
| Oracle Linux | 64 位 | Oracle | 是 |
| Red Hat Enterprise Linux 7.1（及更高版本） | 64 位 | Red Hat | 自带映像 |
| SLES 11SP4 | 64 位 | SUSE | 是 |
| SLES 12SP3 | 64 位 | SUSE | 是 |
| Ubuntu 14.04-LTS | 64 位 | 规范 | 是 |
| Ubuntu 16.04-LTS | 64 位 | 规范 | 是 |
| Ubuntu 18.04-LTS | 64 位 | 规范 | 是 |

有关 Red Hat Enterprise Linux 支持信息，请参阅 [Red Hat 和 Azure Stack：常见问题解答](https://access.redhat.com/articles/3413531)。

## <a name="next-steps"></a>后续步骤

有关 Azure Stack 市场的详细信息，请参阅以下文章：

- [下载市场项](azure-stack-download-azure-marketplace-item.md)  
- [创建和发布市场项](azure-stack-create-and-publish-marketplace-item.md)
