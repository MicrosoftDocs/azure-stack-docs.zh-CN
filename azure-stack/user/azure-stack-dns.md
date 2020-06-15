---
title: Azure Stack Hub 中的 DNS
description: 了解 Azure Stack Hub 中的 DNS 以及如何创建和管理 DNS 区域。
author: sethmanheim
ms.topic: article
ms.date: 06/11/2020
ms.author: sethm
ms.lastreviewed: 01/05/2020
ms.openlocfilehash: 7ab3775b0424a8cba0b84bc3d3ccb40705b192fb
ms.sourcegitcommit: dd140b3a2ac8e558eae9f5f422711d2ba560da16
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2020
ms.locfileid: "84744890"
---
# <a name="use-dns-in-azure-stack-hub"></a>使用 Azure Stack Hub 中的 DNS

Azure Stack Hub 支持以下 Azure DNS 功能：

* DNS 主机名解析。
* 使用 API 创建和管理 DNS 区域和记录。

## <a name="support-for-dns-hostname-resolution"></a>支持 DNS 主机名解析

可以为公用 IP 资源指定一个 DNS 域名标签。 Azure Stack Hub 使用 **domainnamelabel.location.cloudapp.azurestack.external** 作为标签名称并将其映射到 Azure Stack Hub 托管 DNS 服务器中的公共 IP 地址。

例如，如果创建一个公共 IP 资源并以 **contoso** 作为本地 Azure Stack Hub 位置中的域名标签，则[完全限定域名](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (FQDN) **contoso.local.cloudapp.azurestack.external** 会解析为资源的公共 IP 地址。 可以使用此 FQDN 创建自定义域 CNAME 记录，该记录指向 Azure Stack Hub 中的公共 IP 地址。

若要了解有关名称解析的详细信息，请参阅 [DNS 解析](/azure/dns/dns-for-azure-services?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)一文。

> [!IMPORTANT]
> 创建的每个域名标签在其 Azure Stack Hub 位置中必须是唯一的。

以下屏幕截图显示了使用门户创建公共 IP 地址时的“创建公共 IP 地址”对话框  ：

![创建公共 IP 地址](media/azure-stack-dns/image01.png)

### <a name="example-scenario"></a>示例方案

你有一个用于处理来自 Web 应用的请求的负载均衡器。 负载均衡器的后面是一个在一台或多台虚拟机上运行的网站。 可以使用 DNS 名称而非 IP 地址来访问进行了负载均衡的网站。

## <a name="create-and-manage-dns-zones-and-records-using-the-apis"></a>使用 API 创建和管理 DNS 区域和记录

可以在 Azure Stack Hub 中创建和管理 DNS 区域和记录。

Azure Stack Hub 使用与 Azure DNS API 一致的 API 提供与 Azure 类似的 DNS 服务。 通过将域托管在 Azure Stack Hub DNS 中，可以使用相同的凭据、API 和工具来管理 DNS 记录。 还可以使用与其他 Azure 服务相同的计费和支持功能。

Azure Stack Hub DNS 的基础结构比 Azure 的更为精简。 Azure Stack Hub 部署的大小和位置会影响 DNS 的范围、规模和性能。 这还意味着性能、可用性、全局分发和高可用性可能会因部署而异。

## <a name="comparison-with-azure-dns"></a>与 Azure DNS 比较

Azure Stack Hub 中的 DNS 类似于 Azure 中的 DNS，但有几个重要例外：

* **不支持 AAAA 记录**：Azure Stack Hub 不支持 AAAA 记录，因为 Azure Stack Hub 不支持 IPv6 地址。 这是 Azure DNS 与 Azure Stack Hub DNS 之间的主要差异。

* **不是多租户**的： Azure Stack 集线器中的 DNS 服务不是多租户的。 租户不能创建相同的 DNS 区域。 只有第一个尝试创建该区域的订阅会成功，以后的请求都会失败。 这是 Azure DNS 与 Azure Stack Hub DNS 之间另一个主要差异。

* **标记、元数据和 etag**： Azure Stack 集线器处理标记、元数据、etag 和限制的方式之间存在细微的差异。

若要了解有关 Azure DNS 的更多信息，请参阅 [DNS 区域和记录](/azure/dns/dns-zones-records)。

### <a name="tags"></a>Tags

Azure Stack Hub DNS 支持在 DNS 区域资源上使用 Azure 资源管理器标记。 它不支持 DNS 记录集上的标记。 作为替代方法，在 DNS 记录集上支持“元数据”，如下一部分所述。 

### <a name="metadata"></a>Metadata

作为记录集标记的替代方法，Azure Stack Hub DNS 支持使用“元数据”  批注记录集。 与标记相类似，通过元数据可将名称/值对与每个记录集相关联。 例如，元数据可用于记录每个记录集的用途。 与标记不同的是，不能使用元数据提供 Azure 帐单的筛选视图，且不能在 Azure 资源管理器策略中指定元数据。

### <a name="etags"></a>Etag

假设两个人或两个进程尝试同时修改一条 DNS 记录。 哪一个占先？ 占先方是否知道他们/它们覆盖了其他人/进程创建的更改？

Azure Stack Hub DNS 使用 Etag  来安全地处理对同一资源的并发更改。 Etag 与 Azure 资源管理器“标记”  不同。 每个 DNS 资源（区域或记录集）都有与其相关联的 Etag。 检索资源时，还会检索其 Etag。 更新资源时，可以选择传递回 Etag 以便 Azure Stack Hub DNS 可以验证服务器上的 Etag 是否匹配。 由于对资源的每次更新都会导致重新生成 Etag，Etag 不匹配表示发生了并发更改。 创建新的资源时也可以使用 Etag，以确保该资源尚不存在。

默认情况下，Azure Stack Hub DNS PowerShell cmdlet 使用 Etag 来阻止对区域和记录集的并发更改。 可以使用可选的 `-Overwrite` 开关取消 Etag 检查。 如果没有 Etag 检查，则会覆盖已发生的任何并发更改。

Etag 是在 Azure Stack Hub DNS REST API 级别使用 HTTP 标头指定的。 下表介绍了它们的行为：

| 标头 | 行为|
|--------|---------|
| 无   | PUT 始终成功（没有 Etag 检查）。|
| If-match| 只有当资源存在并且 Etag 匹配时，PUT 才会成功。|
| If-match *| 只有当资源存在时，PUT 才会成功。|
| If-none-match *| 只有当资源不存在时，PUT 才会成功。|

### <a name="limits"></a>限制

使用 Azure Stack Hub DNS 时，以下默认限制适用：

| 资源| 默认限制|
|---------|--------------|
| 每个订阅的区域数| 100|
| 每个区域的记录集数| 5000|
| 每个记录集的记录数| 20 个|

## <a name="next-steps"></a>后续步骤

* [适用于 Azure Stack Hub 的 iDNS 简介](azure-stack-understanding-dns.md)
