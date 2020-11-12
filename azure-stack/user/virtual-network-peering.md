---
title: Azure Stack 中心中的虚拟网络对等互连
description: 了解如何使用虚拟网络对等互连在 Azure Stack 集线器中连接虚拟网络。
author: sethmanheim
ms.author: sethm
ms.date: 11/11/2020
ms.topic: conceptual
ms.reviewer: sranthar
ms.lastreviewed: 10/09/2020
ms.openlocfilehash: 16d7701161b8ec8c16aa3caecf5d5e291d6c0e99
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548652"
---
# <a name="virtual-network-peering"></a>虚拟网络对等互连

利用虚拟网络对等互连，可以在 Azure Stack 中心环境中无缝连接虚拟网络。 出于连接目的，两个虚拟网络会显示为一个。 虚拟机之间的流量使用基础 SDN 基础结构。 类似于同一网络中的虚拟机之间的流量，仅通过 Azure Stack 中心专用网络路由流量。

Azure Stack 中心不支持全局对等互连，因为 "区域" 的概念不适用。

使用虚拟网络对等互连的好处如下：

- 不同虚拟网络中资源之间的连接延迟低且带宽高。
- 一个虚拟网络中的资源与另一个虚拟网络中的资源进行通信的能力。
- 在不同订阅和 Azure Active Directory 租户之间跨虚拟网络传输数据的功能。
- 在创建对等互连之时或之后，虚拟网络中的资源不会出现停机的现象。

对等虚拟网络之间的网络流量是专用的。 虚拟网络之间的流量保留在基础结构层中。 虚拟网络之间的通信中不需要公共 internet、网关或加密。

## <a name="connectivity"></a>连接

对于对等互连的虚拟网络，任一虚拟网络中的虚拟机资源可直接连接到对等互连虚拟网络中的资源。

同一区域中对等互连虚拟网络上的虚拟机之间的网络延迟与单个虚拟网络中的延迟相同。 网络吞吐量取决于可供虚拟机使用的与其大小成比例的带宽。 对等互连的带宽没有任何其他限制。

对等互连虚拟网络中虚拟机之间的流量直接通过 SDN 层路由，而不通过网关或公共 internet 路由。

可以在虚拟网络中应用网络安全组，以阻止访问其他虚拟网络或子网。 配置虚拟网络对等互连时，可以打开或关闭虚拟网络之间的网络安全组规则。 如果在对等互连的虚拟网络之间建立了完全连接，则可以应用网络安全组来阻止或拒绝特定的访问。 完全连接是默认选项。 若要详细了解网络安全组，请参阅[安全组](/azure/virtual-network/security-overview)。

## <a name="service-chaining"></a>服务链

使用服务链，可以通过用户定义的路由将流量从一个虚拟网络定向到对等互连网络中的虚拟设备或网关。

若要启用服务链，请将指向对等互连虚拟网络中虚拟机的用户定义的路由配置为下一跃点 IP 地址。 

你可以部署 *中心辐射* 型网络，其中，中心虚拟网络托管基础结构组件，如网络虚拟设备或 VPN 网关。 然后，可将所有分支虚拟网络对等互连到中心虚拟网络。 流量流经中心虚拟网络中的网络虚拟设备或 VPN 网关。

虚拟网络对等互连使用户定义的路由中的下一跃点成为对等互连虚拟网络中虚拟机的 IP 地址。 若要深入了解用户定义的路由，请参阅[用户定义的路由概述](/azure/virtual-network/virtual-networks-udr-overview#user-defined)。 若要了解如何创建中心和分支网络拓扑，请参阅 [Azure 中的中心辐射型网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)。

## <a name="gateways-and-on-premises-connectivity"></a>网关和本地连接

每个虚拟网络（包括对等互连的虚拟网络）都可以有自身的网关。 虚拟网络可以使用其网关连接到本地网络。 请查看 [虚拟网络网关文档](/azure/vpn-gateway/)。

还可以将对等互连的虚拟网络中的网关配置为本地网络的传输点。 在这种情况下，使用远程网关的虚拟网络不能有自身的网关。 一个虚拟网络只能有一个网关。 网关是对等互连虚拟网络中的本地或远程网关，如下图所示：

:::image type="content" source="media/virtual-network-peering/virtual-network-gateway.png" alt-text="VPN 网关拓扑":::

请注意，在对等互连中启用 **UseRemoteGateways** 选项之前，必须在 VPN 网关上创建 **连接** 对象。

## <a name="virtual-network-peering-configuration"></a>虚拟网络对等互连配置

**允许虚拟网络访问：** 启用虚拟网络之间的通信可允许连接到任一虚拟网络的资源以相同的带宽和延迟进行通信，就像它们连接到相同的虚拟网络一样。 两个虚拟网络中的资源之间的所有通信都通过内部 SDN 层路由。  

不启用网络访问的一个原因可能是，你已将虚拟网络与另一个虚拟网络对等互连，但有时想要禁用这两个虚拟网络之间的流量流。 你可能会发现启用/禁用比删除并重新创建对等互连更加方便。 禁用此设置时，流量不会在对等互连的虚拟网络之间流动。

**允许转发的流量：** 选中此框将允许某个虚拟网络中通过网络虚拟设备转发的（不是从该虚拟网络发起的）流量通过对等互连流动到此虚拟网络。 例如，假设有名为 Spoke1、Spoke2 和 Hub 的三个虚拟网络。 每个辐射虚拟网络与中心虚拟网络之间存在一个对等互连，但各个辐射虚拟网络之间不存在对等互连。 一个网络虚拟设备部署在中心虚拟网络中，用户定义的路由应用于通过该网络虚拟设备在各个子网之间路由流量的每个辐射虚拟网络。 如果没有为每个辐射虚拟网络与中心虚拟网络之间的对等互连选中此复选框，则流量不会在各个辐射虚拟网络之间流动，因为中心i不在各个虚拟网络之间转发流量。 虽然启用此功能即可通过对等互连转发流量，但不会创建任何用户定义的路由或网络虚拟设备。 用户定义的路由和网络虚拟设备是单独创建的。 了解[用户定义的路由](/azure/virtual-network/virtual-networks-udr-overview#user-defined)。 如果通过 VPN 网关在虚拟网络之间转发流量，则不需要检查此设置。

**允许网关传输：** 如果已将虚拟网络网关附加到此虚拟网络，并且想要允许来自对等互连虚拟网络的流量流经网关，请选中此框。 例如，此虚拟网络可以通过虚拟网关附加到本地网络。 选中此框将允许来自所对等互连的虚拟网络的流量通过附加到此虚拟网络的网关流到本地网络。 如果选中此框，则已对等互连的虚拟网络不能有已配置的网关。 设置从其他虚拟网络到此虚拟网络的对等互连时，必须选中 " **使用远程网关** " 框。 如果将此框保持未选中状态 (默认) ，对等互连虚拟网络的流量仍会流向此虚拟网络，但无法流经附加到此虚拟网络的虚拟网络网关。

**使用远程网关：** 选中此框可允许来自此虚拟网络的流量流经附加到正与之对等互连的虚拟网络的虚拟网络网关。 例如，要与之对等互连的虚拟网络附加了一个 VPN 网关，因此可与本地网络通信。 选中此框即可让来自此虚拟网络的流量流经附加到已对等互连的虚拟网络的 VPN 网关。 如果选中此框，则对等互连虚拟网络必须附加有虚拟网络网关，并且必须选中 " **允许网关传输** " 框。 如果将此框保持未选中状态 (默认) ，对等互连虚拟网络的流量仍可流向此虚拟网络，但无法流经附加到此虚拟网络的虚拟网络网关。

如果已在虚拟网络中配置了网关，则无法使用远程网关。

### <a name="permissions"></a>权限

请确保在不同订阅中使用 Vnet 创建对等互连，并 Azure AD 租户中，为帐户分配 " **参与者** " 角色。 此外，在不同的 Azure AD 租户之间不存在对等互连的用户界面功能。 可以使用 Azure CLI 和 PowerShell 来创建对等互连。

## <a name="virtual-network-peering-frequently-asked-questions-faq"></a>虚拟网络对等互连常见问题 (FAQ) 

### <a name="what-is-virtual-network-peering"></a>什么是虚拟网络对等互连？

利用虚拟网络对等互连可以连接虚拟网络。 使用虚拟网络之间的 VNet 对等互连连接，可通过 IPv4 地址在这些虚拟网络之间私下路由流量。 对等互连的 VNet 中的虚拟机可相互通信，如同它们处于同一网络中一样。 也可以跨多个订阅创建 VNet 对等互连连接。

### <a name="does-azure-stack-hub-support-global-vnet-peering"></a>Azure Stack 中心是否支持全局 VNET 对等互连？

Azure Stack 中心不支持全局对等互连，因为 "区域" 的概念不适用。

### <a name="on-which-azure-stack-hub-update-will-virtual-network-peering-be-available"></a>虚拟网络对等互连在哪个 Azure Stack 的中心更新可用？

从2008更新开始，Azure Stack 集线器中提供了虚拟网络对等互连。

### <a name="can-i-peer-my-virtual-network-in-azure-stack-hub-to-a-virtual-network-in-azure"></a>是否可以将 Azure Stack 集线器中的虚拟网络与 Azure 中的虚拟网络建立对等互连？

不能，目前不支持 Azure 与 Azure Stack 中心之间的对等互连。

### <a name="can-i-peer-my-virtual-network-in-azure-stack-hub1-to-a-virtual-network-in-azure-stack-hub2"></a>是否可以将 Azure Stack Hub1 中的虚拟网络与 Azure Stack Hub2 中的虚拟网络建立对等互连？

不能，只能在一个 Azure Stack 集线器系统中的虚拟网络之间创建对等互连。 有关如何从不同 stamp 连接两个虚拟网络的详细信息，请参阅 [在 Azure Stack Hub 中建立 vnet 到 vnet 的连接](azure-stack-network-howto-vnet-to-vnet-stacks.md)。

### <a name="can-i-enable-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>如果虚拟网络属于不同 Azure Active Directory 租户中的订阅，是否可以启用对等互连？

是。 如果订阅属于不同的 Azure Active Directory 租户，则可以建立 VNet 对等互连。 可以通过 PowerShell 或 CLI 来执行此操作。 门户尚不受支持。

### <a name="can-i-peer-my-virtual-network-with-a-virtual-network-in-a-different-subscription"></a>是否可以将虚拟网络与另一订阅中的虚拟网络建立对等互连？

是。 你可以跨订阅对虚拟网络进行对等互连。

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>对等互连连接是否存在带宽限制？

否。 虚拟网络对等互连不会强加任何带宽限制。 带宽仅受 VM 或计算资源的限制。

### <a name="my-virtual-network-peering-connection-is-in-an-initiated-state-why-cant-i-connect"></a>虚拟网络对等互连处于已 *启动* 状态，为什么无法连接？

如果对等互连连接处于已 **启动** 状态，则表示你只创建了一个链接。 必须创建双向链接才能成功建立连接。 例如，若要对等 VNet A 到 VNet B，必须从 VNet A 到 VNet B 以及从 VNet B 到 VNet A 创建链接。创建这两个链接会将状态更改为 " **已连接** "。

### <a name="my-virtual-network-peering-connection-is-in-a-disconnected-state-why-cant-i-create-a-peering-connection"></a>虚拟网络对等互连处于 *断开连接* 状态，为什么无法创建对等互连连接？

如果虚拟网络对等互连连接处于 **断开连接** 状态，则表示已删除创建的链接之一。 若要重新建立对等互连连接，请删除该链接，然后重新创建它。

### <a name="is-virtual-network-peering-traffic-encrypted"></a>虚拟网络对等互连流量是否已加密？

不能。 对等互连虚拟网络中的资源之间的流量是专用和隔离的。 它完全位于 Azure Stack 集线器系统的 SDN 层中。

### <a name="if-i-peer-vnet-a-to-vnet-b-and-i-peer-vnet-b-to-vnet-c-does-that-mean-vnet-a-and-vnet-c-are-peered"></a>如果将 VNet A 到 VNet B，并将对等 VNet B 设置为 VNet C，这是否意味着 VNet A 和 VNet C 是对等互连的？

否。 不支持可传递对等互连。 必须对等 VNet A 和 VNet C。

## <a name="next-steps"></a>后续步骤

- [关于 Azure 虚拟网络](/azure/virtual-network/virtual-networks-overview)
- [用户定义的路由概述](/azure/virtual-network/virtual-networks-udr-overview#user-defined)
- [Azure 中的中心辐射型网络拓扑](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
