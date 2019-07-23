---
title: 在 Azure Stack 中使用 Idn |Microsoft Docs
description: 了解如何在 Azure Stack 中使用 Idn 的特性和功能。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: e8a1e40ec5b333862eaca59d7269a46a91460237
ms.sourcegitcommit: 72d45bb935db0db172d4d7c37d8e48e79e25af64
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68376799"
---
# <a name="use-idns-in-azure-stack"></a>在 Azure Stack 中使用 Idn 

适用对象：*Azure Stack 集成系统和 Azure Stack 开发工具包*

iDNS 是一种 Azure Stack 网络功能，可用于解析外部 DNS 名称（例如，https:\//www.bing.com）。它还可用于注册内部虚拟网络名称。 这样, 你就可以通过名称而不是 IP 地址来解析同一虚拟网络上的虚拟机 (Vm)。 使用此方法，不再需要提供自定义 DNS 服务器条目。 有关 DNS 的详细信息, 请参阅[Azure DNS 概述](https://docs.microsoft.com/azure/dns/dns-overview)。

## <a name="what-does-idns-do"></a>iDNS 有什么作用？

使用 Azure Stack 中的 iDNS 可以获得以下功能，而无需指定自定义 DNS 服务器条目：

- 适用于租户工作负荷的共享 DNS 名称解析服务。
- 适用于租户虚拟网络内的名称解析和 DNS 注册的权威 DNS 服务。
- 用于解析来自租户 Vm 的 internet 名称的递归 DNS 服务。 租户不再需要指定自定义 DNS 条目来解析 internet 名称 (例如, www.bing.com)。

你仍然可以沿用自己的 DNS，也可以使用自定义 DNS 服务器。 但是, 通过使用 Idn, 你可以解析 internet DNS 名称并连接到同一虚拟网络中的其他 Vm, 而无需创建自定义 DNS 条目。

## <a name="what-doesnt-idns-do"></a>iDNS 不做什么？

Idn 不允许你为可以从虚拟网络外部解析的名称创建 DNS 记录。

在 Azure 中，可以选择指定与公共 IP 地址关联的 DNS 名称标签。 你可以选择标签（前缀），但 Azure 会根据创建公共 IP 地址所在的区域选择后缀。

![DNS 名称标签示例](media/azure-stack-understanding-dns-in-tp2/image3.png)

如上图所示, Azure 将在 DNS 中为在区域**westus.cloudapp.azure.com**下指定的 dns 名称标签创建 "A" 记录。 前缀和后缀将组合在一起, 以构成一个[完全限定的域名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)(FQDN), 可以从公共 internet 上的任何位置解析该域名 (FQDN)。

Azure Stack 仅支持用于内部名称注册的 Idn, 因此它无法执行以下操作:

- 在现有的托管 DNS 区域（例如，local.azurestack.external）下创建 DNS 记录。
- 创建 DNS 区域（例如 Contoso.com）。
- 在你自己的自定义 DNS 区域下创建记录。
- 支持购买域名。

## <a name="next-steps"></a>后续步骤

[使用 Azure Stack 中的 DNS](azure-stack-dns.md)
