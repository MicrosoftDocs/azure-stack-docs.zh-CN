---
title: 在 Azure Stack 中心使用 Idn |Microsoft Docs
description: 了解如何在 Azure Stack 集线器中使用 Idn 的特性和功能。
services: azure-stack
documentationcenter: ''
author: Justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: Justinha
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 2ad223096601c31cf558e5557440d7d03065292e
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75818921"
---
# <a name="use-idns-in-azure-stack-hub"></a>在 Azure Stack 集线器中使用 Idn 

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

Idn 是一项 Azure Stack 集线器网络功能，可用于解析外部 DNS 名称（例如，https：\//www.bing.com。）它还允许你注册内部虚拟网络名称。 这样，你就可以通过名称而不是 IP 地址来解析同一虚拟网络上的虚拟机（Vm）。 此方法不再需要提供自定义 DNS 服务器条目。 有关 DNS 的详细信息，请参阅[Azure DNS 概述](https://docs.microsoft.com/azure/dns/dns-overview)。

## <a name="what-does-idns-do"></a>Idn 有什么作用？

利用 Azure Stack Hub 中的 Idn，你可以获得以下功能，而无需指定自定义 DNS 服务器条目：

- 适用于租户工作负荷的共享 DNS 名称解析服务。
- 用于在租户虚拟网络中进行名称解析和 DNS 注册的权威 DNS 服务。
- 用于解析来自租户 Vm 的 internet 名称的递归 DNS 服务。 租户不再需要指定自定义 DNS 条目来解析 internet 名称（例如 www\.bing.com）。

你仍可以自带 DNS 并使用自定义 DNS 服务器。 但是，通过使用 Idn，你可以解析 internet DNS 名称并连接到同一虚拟网络中的其他 Vm，而无需创建自定义 DNS 条目。

## <a name="what-doesnt-idns-do"></a>Idn 有什么作用？

Idn 不允许你为可以从虚拟网络外部解析的名称创建 DNS 记录。

在 Azure 中，可以选择指定与公共 IP 地址关联的 DNS 名称标签。 你可以选择标签（前缀），但 Azure 会根据你在其中创建公共 IP 地址的区域选择后缀。

![DNS 名称标签的示例](media/azure-stack-understanding-dns-in-tp2/image3.png)

如上图所示，Azure 将在 DNS 中为在区域**westus.cloudapp.azure.com**下指定的 dns 名称标签创建 "A" 记录。 前缀和后缀将组合在一起，以构成一个[完全限定的域名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)（FQDN），可以从公共 internet 上的任何位置解析该域名（FQDN）。

Azure Stack 集线器仅支持用于内部名称注册的 Idn，因此它无法执行以下操作：

- 在现有的托管 DNS 区域（例如 test-azurestack）下创建 DNS 记录。
- 创建 DNS 区域（例如 Contoso.com）。
- 在你自己的自定义 DNS 区域下创建一条记录。
- 支持购买域名。

## <a name="demo-of-how-idns-works"></a>演示 Idn 的工作方式

虚拟网络上的所有 Vm 的主机名均存储为同一区域下的 DNS 资源记录，但其各自唯一的隔离舱定义为与部署 VM 的 SDN 基础结构中的 VNET ID 相关联的 GUID。 租户 VM 完全限定的域名（Fqdn）包含计算机名称和虚拟网络的 DNS 后缀字符串（采用 GUID 格式）。

<!--- what does compartment mean? Add a screenshot? can we clarify what we mean by host name and computer name. the description doesn't match the example in the table.--->
 
下面是一个简单的实验室，演示其工作原理。 我们已在一个 VNet 中创建3个 Vm，在单独的 VNet 中创建了另一个 VM：

<!--- Is DNS Label the right term? If so, we should define it. The column lists FQDNs, afaik. Where does the domain suffix come from? --->
 
|VM    |vNet    |专用 IP   |公共 IP    | DNS 标签                                |
|------|--------|-------------|-------------|------------------------------------------|
|VM-A1 |VNetA   | 10.0.0.5    |172.31.12.68 |VM-A1-lnv1. p p. azscss |
|VM-A2 |VNetA   | 10.0.0.6    |172.31.12.76 |VM-A2-lnv1. p p. azscss |
|VM-A3 |VNetA   | 10.0.0.7    |172.31.12.49 |VM-A3-lnv1. p p. azscss |
|VM-B1 |VNetB   | 10.0.0.4    |172.31.12.57 |VM-B1-lnv1. p p. azscss |
 
 
|VNet  |GUID                                 |DNS 后缀字符串                                                  |
|------|-------------------------------------|-------------------------------------------------------------------|
|VNetA |e71e1db5-0a38-460d-8539-705457a4cf75 |e71e1db5-0a38-460d-8539-705457a4cf75. lnv1. test-azurestack|
|VNetB |e8a6e386-bc7a-43e1-a640-61591b5c76dd |e8a6e386-bc7a-43e1-a640-61591b5c76dd. lnv1. test-azurestack|
 
 
可以执行一些名称解析测试，以更好地了解 Idn 的工作原理：

<!--- why Linux?--->

From VM-A1 （Linux VM）：查找 VM-A2。 你可以看到，已添加 VNetA 的 DNS 后缀并将名称解析为专用 IP：
 
```console
carlos@VM-A1:~$ nslookup VM-A2
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2.e71e1db5-0a38-460d-8539-705457a4cf75.internal.lnv1.azurestack.local
Address: 10.0.0.6
```
 
如以下所示，查找未提供 FQDN 的 VM-A2 标签失败：

```console 
carlos@VM-A1:~$ nslookup VM-A2-Label
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-A2-Label: SERVFAIL
```

如果为 DNS 标签提供 FQDN，则会将该名称解析为公共 IP：

```console
carlos@VM-A1:~$ nslookup VM-A2-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.76
```
 
尝试解析 VM-B1 （来自其他 VNet）失败，因为此记录在此区域中不存在。

```console
carlos@caalcobi-vm4:~$ nslookup VM-B1
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-B1: SERVFAIL
```

为 VM 使用 FQDN-B1 并不起作用，因为此记录来自其他区域。

```console 
carlos@VM-A1:~$ nslookup VM-B1.e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-B1.e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local: SERVFAIL
```
 
如果对 DNS 标签使用 FQDN，则它将成功解析：

``` 
carlos@VM-A1:~$ nslookup VM-B1-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-B1-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.57
```
 
From VM-A3 （Windows VM）。 请注意权威和非权威应答之间的差异。

内部记录：

```console
C:\Users\carlos>nslookup
Default Server:  UnKnown
Address:  168.63.129.16
 
> VM-A2
Server:  UnKnown
Address:  168.63.129.16
 
Name:    VM-A2.e71e1db5-0a38-460d-8539-705457ª4cf75.internal.lnv1.azurestack.local
Address:  10.0.0.6
```

外部记录：

```console
> VM-A2-Label.lnv1.cloudapp.azscss.external
Server:  UnKnown
Address:  168.63.129.16
 
Non-authoritative answer:
Name:    VM-A2-Label.lnv1.cloudapp.azscss.external
Address:  172.31.12.76
``` 
 
简而言之，你可以从上述内容中看出：
 
*   每个 VNet 都有其自己的区域，其中包含所有专用 IP 地址的记录，由 VM 名称和 VNet 的 DNS 后缀（其 GUID）组成。
    *   \<vmname >。\<vnetGUID\>。>\<区域。\<stackinternalFQDN >
    *   这是自动完成的
*   如果使用公共 IP 地址，则还可以为它们创建 DNS 标签。 这些地址的解析方式与其他外部地址相同。
 
 
- Idn 服务器是其内部 DNS 区域的权威服务器，当租户 Vm 尝试连接到外部资源时，它们也充当公用名的解析程序。 如果存在对外部资源的查询，Idn 服务器会将请求转发给权威 DNS 服务器进行解析。
 
从实验室结果可以看出，可以控制所使用的 IP。 如果使用 VM 名称，你将获得专用 IP 地址，并且如果你使用 DNS 标签，则会获取公共 IP 地址。

## <a name="next-steps"></a>后续步骤

[在 Azure Stack 集线器中使用 DNS](azure-stack-dns.md)
