---
title: Azure Stack 中心中的 DNS
description: 了解 Azure Stack 集线器中的 DNS 以及如何创建和管理 DNS 区域。
author: sethmanheim
ms.topic: article
ms.date: 01/24/2020
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: dd2b00ced084f4a069626f56d37eec1cdae238d6
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884830"
---
# <a name="use-dns-in-azure-stack-hub"></a>在 Azure Stack 集线器中使用 DNS

Azure Stack 中心支持以下 Azure DNS 功能：

* DNS 主机名解析。
* 使用 API 创建和管理 DNS 区域和记录。

## <a name="support-for-dns-hostname-resolution"></a>支持 DNS 主机名解析

可以为公共 IP 资源指定 DNS 域名标签。 Azure Stack 中心对标签名称使用**p p.** ，并将其映射到 Azure Stack 集线器托管的 DNS 服务器中的公共 IP 地址。

例如，如果在本地 Azure Stack 中心位置创建了**contoso**作为域名标签的公共 ip 资源，则[完全限定的域名（FQDN）](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) **p p.** 将解析为该资源的公共 IP 地址。 可以使用此 FQDN 创建指向 Azure Stack 集线器中的公共 IP 地址的自定义域 CNAME 记录。

若要了解有关名称解析的详细信息，请参阅[DNS 解析](/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)一文。

> [!IMPORTANT]
> 你创建的每个域名标签在其 Azure Stack 中心位置中必须是唯一的。

以下屏幕截图显示了使用门户创建公共 IP 地址的 "**创建公共 ip 地址**" 对话框：

![创建公共 IP 地址](media/azure-stack-dns/image01.png)

### <a name="example-scenario"></a>示例方案

你有一个来自 web 应用的负载均衡器处理请求。 负载均衡器后面是在一个或多个虚拟机上运行的网站。 你可以使用 DNS 名称而不是 IP 地址来访问负载平衡的网站。

## <a name="create-and-manage-dns-zones-and-records-using-the-apis"></a>使用 Api 创建和管理 DNS 区域和记录

可以在 Azure Stack Hub 中创建和管理 DNS 区域和记录。

Azure Stack 集线器使用与 Azure DNS Api 一致的 Api，提供与 Azure 类似的 DNS 服务。  通过在 Azure Stack Hub DNS 中托管你的域，你可以使用相同的凭据、Api 和工具来管理你的 DNS 记录。 你还可以使用与其他 Azure 服务相同的计费和支持。

Azure Stack 集线器 DNS 基础结构比 Azure 更紧凑。 Azure Stack 中心部署的大小和位置会影响 DNS 范围、规模和性能。 这也意味着，性能、可用性、全局分发和高可用性可能因部署而异。

## <a name="comparison-with-azure-dns"></a>与 Azure DNS 比较

Azure Stack 集线器中的 DNS 类似于 Azure 中的 DNS，但有几个重要的例外：

* 不**支持 aaaa 记录**： Azure Stack 集线器不支持 aaaa 记录，因为 Azure Stack 中心不支持 IPv6 地址。 这是 Azure 中的 DNS 与 Azure Stack 中心之间的主要区别。

* **不是多租户**的： Azure Stack 集线器中的 DNS 服务不是多租户的。 租户无法创建相同的 DNS 区域。 只有第一个尝试创建区域的订阅才会成功，并且以后的请求会失败。 这是 Azure 与 Azure Stack 中心 DNS 的另一个主要区别。

* **标记、元数据和 etag**： Azure Stack 中心处理标记、元数据、etag 和限制的方式之间存在细微的差异。

若要详细了解 Azure DNS，请参阅[DNS 区域和记录](/azure/dns/dns-zones-records)。

### <a name="tags"></a>标记

Azure Stack 集线器 DNS 支持在 DNS 区域资源上使用 Azure 资源管理器标记。 它不支持 DNS 记录集上的标记。 作为替代方法，在 DNS 记录集上支持**元数据**，如下一节中所述。

### <a name="metadata"></a>元数据

作为记录集标记的替代方法，Azure Stack 集线器 DNS 支持使用*元数据*批注记录集。 与标记相类似，通过元数据可将名称/值对与每个记录集相关联。 例如，元数据可用于记录每个记录集的用途。 与标记不同，你无法使用元数据提供 Azure 帐单的筛选视图，并且不能在 Azure 资源管理器策略中指定元数据。

### <a name="etags"></a>Etag

假设两个人或两个进程尝试同时修改一条 DNS 记录。 哪一个占先？ 占先方是否知道他们/它们覆盖了其他人/进程创建的更改？

Azure Stack 中心 DNS 使用*etag*来安全地处理对同一资源的并发更改。 Etag 不同于 Azure 资源管理器*标记*。 每个 DNS 资源（区域或记录集）都有与其相关联的 Etag。 检索资源时，也会检索该资源的 Etag。 更新资源时，可以选择传递回 Etag，以便 Azure Stack 集线器 DNS 可以验证服务器上的 Etag 是否匹配。 由于对资源的每次更新都会导致重新生成 Etag，Etag 不匹配表示发生了并发更改。 在创建新资源时，还可以使用 etag，以确保该资源尚不存在。

默认情况下，Azure Stack 集线器 DNS PowerShell cmdlet 使用 Etag 来阻止对区域和记录集的并发更改。 可以使用可选的 `-Overwrite` 开关来取消 Etag 检查。 如果没有 Etag 检查，则会覆盖发生的所有并发更改。

在 Azure Stack Hub DNS REST API 级别，使用 HTTP 标头指定 Etag。 下表介绍了其行为：

| 标头 | 行为|
|--------|---------|
| 无   | PUT 始终成功（没有 Etag 检查）。|
| If-match| 仅当资源存在并且 Etag 匹配时，PUT 才会成功。|
| If-match *| 仅当资源存在时，PUT 才会成功。|
| If-none-match *| 仅当资源不存在时，PUT 才会成功。|

### <a name="limits"></a>限制

使用 Azure Stack 集线器 DNS 时，以下默认限制适用：

| 资源| 默认限制|
|---------|--------------|
| 每个订阅的区域数| 100|
| 每个区域的记录集数| 5000|
| 每个记录集的记录数| 20|

## <a name="next-steps"></a>后续步骤

* [Azure Stack 中心简介 Idn](azure-stack-understanding-dns.md)
