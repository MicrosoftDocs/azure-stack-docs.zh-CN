---
title: Azure Stack 中心集成系统的透明代理
description: Azure Stack 集线器集成系统中透明属性的概述。
author: PatAltimore
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: patricka
ms.reviewer: sranthar
ms.lastreviewed: 01/25/2021
ms.openlocfilehash: 974f40364b4eed13bd7440b35596597312c98624
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577270"
---
# <a name="transparent-proxy-for-azure-stack-hub"></a>Azure Stack 中心的透明代理

透明代理 (也称为拦截、内联或强制代理) 在网络层上拦截正常通信，无需特殊客户端配置。 客户端不需要知道代理是否存在。

如果你的数据中心需要所有流量才能使用代理，则可以配置透明代理以根据策略处理所有流量，方法是将网络上的区域之间的流量隔开。

## <a name="traffic-types"></a>流量类型

来自 Azure Stack 集线器的出站流量归类为租户流量或基础结构流量。

租户流量由租户通过虚拟机、负载均衡器、VPN 网关、应用服务等生成。

基础结构流量是从分配给 `/27` 基础结构服务的公共虚拟 IP 池的第一个范围（如标识、修补和更新、使用情况指标、市场供稿、注册、日志收集、Windows Defender 等）生成的。来自这些服务的流量将路由到 [Azure 终结点](azure-stack-integrate-endpoints.md#ports-and-urls-outbound)。 Azure 不接受代理修改的流量，也不接受截获的 TLS/SSL 流量。 此原因是 Azure Stack 集线器不支持本机代理配置的原因。

配置透明代理时，你可以选择发送所有出站流量，或者只发送通过代理的基础结构流量。

## <a name="partner-integration"></a>合作伙伴集成

Microsoft 已与业界领先的代理供应商合作，以使用透明代理配置来验证 Azure Stack 集线器用例场景。 下图是包含 HA 代理 Azure Stack 集线器网络配置的示例。 外部代理设备必须位于边界设备的北方北部。

![边框设备之前具有代理的网络图](./media/azure-stack-transparent-proxy/proxy.svg)

 此外，边界设备必须配置为通过以下方式之一从 Azure Stack 集线器路由流量：
- 将所有出站流量从 Azure Stack 中心路由到代理设备
- `/27`通过基于策略的路由将来自 Azure Stack 中心虚拟 IP 池的第一个范围的所有出站流量路由到代理设备。  

有关边框配置的示例，请参阅本文中的 [示例边框配置](#example-border-configuration) 部分。

请查看以下文档，了解 Azure Stack 中心验证的透明代理配置： 

- [配置检查点安全网关透明代理](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk171559)
- [配置 Sophos XG 防火墙透明代理](https://community.sophos.com/xg-firewall/f/recommended-reads/124106/xg-firewall-integration-with-azure-stack-hub)

在需要来自 Azure Stack 集线器的出站流量通过显式代理的情况下，Sophos 和检查点设备提供一种双重模式功能，该功能允许通过透明模式使用特定范围的流量，而其他范围可以配置为通过显式模式。 使用此功能可以配置这些代理设备，以便仅通过透明代理发送基础结构流量，并通过显式模式发送所有租户通信。

> [!IMPORTANT]
> SSL 流量截获不受支持，并且可能在访问终结点时导致服务失败。 与标识所需的终结点进行通信时，支持的最大超时值为 60 秒，并可以进行 3 次重试尝试。 有关详细信息，请参阅 [Azure Stack 集线器防火墙集成](azure-stack-firewall.md#ssl-interception)。

## <a name="example-border-configuration"></a>边框配置示例

此解决方案基于基于策略的路由 (.PBR) ，它使用由访问控制列表 (ACL) 实现的管理员定义的条件集。 ACL 将定向到路由映射中实现的代理设备的下一跃点 IP 的流量分类，而不是仅基于目标 IP 地址的普通路由。 端口80和443的特定基础结构网络流量从边框设备路由到透明代理部署。 透明代理进行 URL 筛选，但不 *允许丢弃任何允许* 的流量。

以下配置示例适用于 Cisco 结点9508机箱。 

在此方案中，需要访问 internet 的源基础结构网络如下所示：

- 公共 VIP-第一/27
- 基础结构网络–最后/27
- BMC 网络–最后/27

以下子网在此方案中接收基于策略的路由 (.PBR) 处理：

| 网络 | IP 范围 | 子网接收 .PBR 处理
|---------|----------|-------------------------------
| 公共虚拟 IP 池 | 第一个/27 个 172.21.107.0/27 | 172.21.107.0/27 = 172.21.107.1 到172.21.107.30
| 基础结构网络 | 172.21.7.0/24 的最后/27 | 172.21.7.224/27 = 172.21.7.225 到172.21.7.254
| BMC 网络 | 10.60.32.128/26 的最后/27 | 10.60.32.160/27 = 10.60.32.161 到10.60.32.190

### <a name="configure-border-device"></a>配置边框设备

通过输入命令启用 .PBR `feature pbr` 。 

```
****************************************************************************
PBR Configuration for Cisco Nexus 9508 Chassis
PBR Enivronment configured to use VRF08
The test rack has is a 4-node Azure Stack stamp with 2x TOR switches and 1x BMC switch. Each TOR switch 
has a single uplink to the Nexus 9508 chassis using BGP for routing. In this example the test rack 
is in it's own VRF (VRF08)
****************************************************************************
!
feature pbr
!

<Create VLANs that the proxy devices will use for inside and outside connectivity>

!
VLAN 801
name PBR_Proxy_VRF08_Inside
VLAN 802
name PBR_Proxy_VRF08_Outside
!
interface vlan 801
description PBR_Proxy_VRF08_Inside
no shutdown
mtu 9216
vrf member VRF08
no ip redirects
ip address 10.60.3.1/29
!
interface vlan 802
description PBR_Proxy_VRF08_Outside
no shutdown
mtu 9216
vrf member VRF08
no ip redirects
ip address 10.60.3.33/28
!
!
ip access-list PERMITTED_TO_PROXY_ENV1
100 permit tcp 172.21.107.0/27 any eq www
110 permit tcp 172.21.107.0/27 any eq 443
120 permit tcp 172.21.7.224/27 any eq www
130 permit tcp 172.21.7.224/27 any eq 443
140 permit tcp 10.60.32.160/27 any eq www
150 permit tcp 10.60.32.160/27 any eq 443
!
!
route-map TRAFFIC_TO_PROXY_ENV1 pbr-statistics
route-map TRAFFIC_TO_PROXY_ENV1 permit 10
  match ip address PERMITTED_TO_PROXY_ENV1
  set ip next-hop 10.60.3.34 10.60.3.35
!
!
interface Ethernet1/1
  description DownLink to TOR-1:TeGig1/0/47
  mtu 9100
  logging event port link-status
  vrf member VRF08
  ip address 192.168.32.193/30
  ip policy route-map TRAFFIC_TO_PROXY_ENV1
  no shutdown
!
interface Ethernet2/1
  description DownLink to TOR-2:TeGig1/0/48
  mtu 9100
  logging event port link-status
  vrf member VRF08
  ip address 192.168.32.205/30
  ip policy route-map TRAFFIC_TO_PROXY_ENV1
  no shutdown
!

<Interface configuration for inside/outside connections to proxy devices. In this example there are 2 firewalls>

!
interface Ethernet1/41
  description management interface for Firewall-1
  switchport
  switchport access vlan 801
  no shutdown
!
interface Ethernet1/42
  description Proxy interface for Firewall-1
  switchport
  switchport access vlan 802
  no shutdown
!
interface Ethernet2/41
  description management interface for Firewall-2
  switchport
  switchport access vlan 801
  no shutdown
!
interface Ethernet2/42
  description Proxy interface for Firewall-2
  switchport
  switchport access vlan 802
  no shutdown
!

<BGP network statements for VLAN 801-802 subnets and neighbor statements for R023171A-TOR-1/R023171A-TOR-2> 

!
router bgp 65000
!
vrf VRF08
address-family ipv4 unicast
network 10.60.3.0/29
network 10.60.3.32/28
!
neighbor 192.168.32.194
  remote-as 65001
  description LinkTo 65001:R023171A-TOR-1:TeGig1/0/47
  address-family ipv4 unicast
    maximum-prefix 12000 warning-only
neighbor 192.168.32.206
  remote-as 65001
  description LinkTo 65001:R023171A-TOR-2:TeGig1/0/48
  address-family ipv4 unicast
    maximum-prefix 12000 warning-only
!
!
```

创建新的 ACL，该 ACL 将用于识别将获得 .PBR 处理的流量。 该流量是 (HTTP 端口80和 HTTPS 端口 443) 的网络流量，如本示例中所述，该流量可获取代理服务。 例如，ACL 名称为 **PERMITTED_TO_PROXY_ENV1**。

```
ip access-list PERMITTED_TO_PROXY_ENV1
100 permit tcp 172.21.107.0/27 any eq www <<HTTP traffic from CL04 Public Admin VIPs leaving test rack>>
110 permit tcp 172.21.107.0/27 any eq 443 <<HTTPS traffic from CL04 Public Admin VIPs leaving test rack>>
120 permit tcp 172.21.7.224/27 any eq www <<HTTP traffic from CL04 INF-pub-adm leaving test rack>>
130 permit tcp 172.21.7.224/27 any eq 443 <<HTTPS traffic from CL04 INF-pub-adm leaving test rack>>
140 permit tcp 10.60.32.160/27 any eq www <<HTTP traffic from DVM and HLH leaving test rack>>
150 permit tcp 10.60.32.160/27 any eq 443 <<HTTPS traffic from DVM and HLH leaving test rack>>
```

.PBR 功能的核心是由 **TRAFFIC_TO_PROXY_ENV1** 路由映射实现的。 添加了 " **.pbr 统计** " 选项，以允许查看策略 "匹配统计信息"，以验证执行和不会获得 .pbr 转发的数据包数量。 路由映射序列10允许对流量会议 ACL **PERMITTED_TO_PROXY_ENV1** 条件进行 .pbr 处理。 该流量将转发到和的下一跃点 IP `10.60.3.34` 地址 `10.60.3.35` ，这些 IP 地址是示例配置中的主要/辅助代理设备的 vip

```
!
route-map TRAFFIC_TO_PROXY_ENV1 pbr-statistics
route-map TRAFFIC_TO_PROXY_ENV1 permit 10
  match ip address PERMITTED_TO_PROXY_ENV1
  set ip next-hop 10.60.3.34 10.60.3.35
```

Acl 用作 **TRAFFIC_TO_PROXY_ENV1** 路由映射的匹配条件。 当流量与 **PERMITTED_TO_PROXY_ENV1** ACL 匹配时，.pbr 将覆盖普通路由表，并将流量转发到列出的 IP 下一个跃点。
 
**TRAFFIC_TO_PROXY_ENV1** .pbr 策略适用于从 CL04 主机和公共 vip 以及测试机架中的 HLH 和 DVM 进入边界设备的流量。

## <a name="next-steps"></a>后续步骤

了解有关防火墙集成的详细信息，请参阅 [Azure Stack 集线器防火墙集成](azure-stack-firewall.md)。
