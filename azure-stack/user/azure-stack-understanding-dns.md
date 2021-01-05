---
title: 在 Azure Stack Hub 中使用 iDNS
description: 了解如何在 Azure Stack Hub 中使用 iDNS 的特性和功能。
author: PatAltimore
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: patricka
ms.reviewer: scottnap
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 8860553f9b35b5a17d07edeb867ea5bad4c3fefa
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873786"
---
# <a name="use-idns-in-azure-stack-hub"></a>在 Azure Stack Hub 中使用 iDNS 

iDNS 是一种 Azure Stack Hub 网络功能，可用于解析外部 DNS 名称（例如，https:\//www.bing.com）。它还可用于注册内部虚拟网络名称。 如此一来，就可按名称（而非 IP 地址）解析同一虚拟网络上的虚拟机 (VM)。 使用此方法，不再需要提供自定义 DNS 服务器条目。 有关 DNS 的详细信息，请参阅 [Azure DNS 概述](/azure/dns/dns-overview)。

## <a name="what-does-idns-do"></a>iDNS 有什么作用？

使用 Azure Stack Hub 中的 iDNS 可以获得以下功能，而无需指定自定义 DNS 服务器条目：

- 适用于租户工作负荷的共享 DNS 名称解析服务。
- 适用于租户虚拟网络内的名称解析和 DNS 注册的权威 DNS 服务。
- 从租户 VM 解析 Internet 名称的递归 DNS 服务。 租户不再需要指定自定义 DNS 条目，就可以解析 Internet 名称（例如，www\.bing.com）。

你仍然可以沿用自己的 DNS，也可以使用自定义 DNS 服务器。 但是，通过使用 iDNS，你可以解析 Internet DNS 名称并连接到同一虚拟网络中的其他 VM，而无需创建自定义 DNS 条目。

## <a name="what-doesnt-idns-do"></a>iDNS 不做什么？

iDNS 不允许针对可从虚拟网络外部解析的名称创建 DNS 记录。

在 Azure 中，可以选择指定与公共 IP 地址关联的 DNS 名称标签。 你可以选择标签（前缀），但 Azure 会根据创建公共 IP 地址所在的区域选择后缀。

![DNS 名称标签示例](media/azure-stack-understanding-dns-in-tp2/image3.png)

如上图所示，Azure 将在 DNS 中为在区域 **westus.cloudapp.azure.com** 下指定的 dns 名称标签创建 "A" 记录。 前缀和后缀将组合在一起，以构成可从公共 internet 上的任何位置解析 (FQDN) 的 [完全限定域名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) 。

Azure Stack Hub 仅支持将 iDNS 用于内部名称注册，因此它无法执行以下操作：

- 在现有的托管 DNS 区域（例如，local.azurestack.external）下创建 DNS 记录。
- 创建 DNS 区域（例如 Contoso.com）。
- 在你自己的自定义 DNS 区域下创建记录。
- 支持购买域名。

## <a name="demo-of-how-idns-works"></a>演示 iDNS 的工作原理

虚拟网络中 VM 的所有主机名都作为 DNS 资源记录存储在同一区域，但是，在这些 VM 自有的唯一区间，这些主机名定义为 GUID，该 GUID 与作为 VM 部署目标的 SDN 基础结构中的 VNET ID 相关。 租户 VM 的完全限定域名 (FQDN) 由计算机名以及虚拟网络的 DNS 后缀字符串（采用 GUID 格式）组成。

<!--- what does compartment mean? Add a screenshot? can we clarify what we mean by host name and computer name. the description doesn't match the example in the table.--->
 
以下简单实验演示了相关的工作原理。 我们在一个 VNet 中创建了 3 个 VM，并在另一个 VNet 中创建了另一个 VM：

<!--- Is DNS Label the right term? If so, we should define it. The column lists FQDNs, afaik. Where does the domain suffix come from? --->
 
|VM    |vNet    |专用 IP   |公共 IP    | DNS 标签                                |
|------|--------|-------------|-------------|------------------------------------------|
|VM-A1 |VNetA   | 10.0.0.5    |172.31.12.68 |VM-A1-Label.lnv1.cloudapp.azscss.external |
|VM-A2 |VNetA   | 10.0.0.6    |172.31.12.76 |VM-A2-Label.lnv1.cloudapp.azscss.external |
|VM-A3 |VNetA   | 10.0.0.7    |172.31.12.49 |VM-A3-Label.lnv1.cloudapp.azscss.external |
|VM-B1 |VNetB   | 10.0.0.4    |172.31.12.57 |VM-B1-Label.lnv1.cloudapp.azscss.external |
 
 
|VNet  |GUID                                 |DNS 后缀字符串                                                  |
|------|-------------------------------------|-------------------------------------------------------------------|
|VNetA |e71e1db5-0a38-460d-8539-705457a4cf75 |e71e1db5-0a38-460d-8539-705457a4cf75.internal.lnv1.azurestack.local|
|VNetB |e8a6e386-bc7a-43e1-a640-61591b5c76dd |e8a6e386-bc7a-43e1-a640-61591b5c76dd.internal.lnv1.azurestack.local|
 
 
可以执行一些名称解析测试，以更好地了解 iDNS 的工作原理：

<!--- why Linux?--->

在 VM-A1 (Linux VM) 中：查找 VM-A2。 可以看到，已添加 VNetA 的 DNS 后缀，并且名称已解析为专用 IP：
 
```console
carlos@VM-A1:~$ nslookup VM-A2
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2.e71e1db5-0a38-460d-8539-705457a4cf75.internal.lnv1.azurestack.local
Address: 10.0.0.6
```
 
在不提供 FQDN 的情况下查找 VM-A2-Label 将会失败，这在意料之中：

```console 
carlos@VM-A1:~$ nslookup VM-A2-Label
Server:         127.0.0.53
Address:        127.0.0.53#53
 
** server can't find VM-A2-Label: SERVFAIL
```

如果提供 DNS 标签的 FQDN，则名称将解析为公共 IP：

```console
carlos@VM-A1:~$ nslookup VM-A2-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-A2-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.76
```
 
尝试解析 VM-B1（位于不同的 VNet 中）将会失败，因为此记录不在此区域中。

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
 
如果使用 DNS 标签的 FQDN，则可成功解析：

``` 
carlos@VM-A1:~$ nslookup VM-B1-Label.lnv1.cloudapp.azscss.external
Server:         127.0.0.53
Address:        127.0.0.53#53
 
Non-authoritative answer:
Name:   VM-B1-Label.lnv1.cloudapp.azscss.external
Address: 172.31.12.57
```
 
在 VM-A3 (Windows VM) 中。 请注意权威与非权威应答之间的差异。

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
 
概括而言，在以上内容中可以看到：
 
*   每个 VNet 具有自身的区域，其中包含所有专用 IP 地址的 A 记录，这些记录由 VM 名称和 VNet 的 DNS 后缀（其 GUID）组成。
    *   \<vmname>.\<vnetGUID\>.内部。 \<region>\<stackinternalFQDN>
    *   此过程是自动完成的
*   如果使用公共 IP 地址，则也可以为其创建 DNS 标签。 这些地址的解析方式类似于其他任何外部地址。
 
 
- iDNS 服务器是其内部 DNS 区域的权威服务器，并且还在租户 VM 尝试连接到外部资源时，充当公共名称的解析器。 如果存在对外部资源的查询，则 iDNS 服务器会将请求转发到权威 DNS 服务器进行解析。
 
从实验结果中可以看到，你可以对使用的 IP 进行控制。 如果使用 VM 名称，则会获得专用 IP 地址；如果使用 DNS 标签，则会获得公共 IP 地址。

## <a name="next-steps"></a>后续步骤

[使用 Azure Stack Hub 中的 DNS](azure-stack-dns.md)
