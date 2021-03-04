---
title: 概念-Azure Kubernetes Services 中的网络 (AKS) Azure Stack HCI
description: 了解 Azure Kubernetes Service 中的网络 (AKS) Azure Stack HCI，包括静态 IP 和 DHCP 网络和负载均衡器。
author: abha
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: abha
ms.custom: fasttrack-edit
ms.openlocfilehash: a8e96783be3228aae525027855c1498b0269bd57
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873585"
---
# <a name="network-concepts-for-deploying-azure-kubernetes-service-aks-on-azure-stack-hci"></a>用于在 Azure Stack HCI 上部署 Azure Kubernetes Service (AKS) 的网络概念

在基于容器的微服务应用程序开发方法中，应用程序组件必须协同工作才能处理其任务。 Kubernetes 提供支持此应用程序通信的各种资源。 可以在内部或外部连接应用程序并将其公开。 要生成高可用性应用程序，可以对应用程序进行负载均衡。 

本文介绍在 Azure Stack HCI 上的应用程序中为应用程序提供网络的核心概念。 然后，它将指导你完成不同的部署模型，并提供有关为本地 AKS 部署设置最佳网络基础结构的示例。

## <a name="kubernetes-on-azure-stack-hci-basics"></a>Azure Stack HCI 基础上的 Kubernetes

为允许访问应用程序或让应用程序组件相互通信，Kubernetes 为虚拟网络提供了抽象层。 Kubernetes 节点连接到虚拟网络，可以提供用于 pod 的入站和出站连接。 kube-proxy 组件在每个节点上运行，以提供这些网络功能。

在 Kubernetes 中， *服务* 在逻辑上将 pod 分组，以允许通过 IP 地址或 DNS 名称以及在特定端口上进行直接访问。 此外，还可使用负载均衡器分发流量。 

Azure Stack HCI 平台还有助于简化 Azure Stack HCI 群集上的 AKS 虚拟网络。 在 Azure Stack HCI 上的 AKS 上创建群集时， `HAProxy` 会创建和配置基础负载均衡器资源。 在 Kubernetes 群集的基础上构建应用程序时，会为 Kubernetes 服务配置 IP 地址。

## <a name="aks-on-azure-stack-hci-resources"></a>Azure Stack HCI 资源上的 AKS 

为简化应用程序工作负载的网络配置，Azure Stack HCI 上的 AKS 将 IP 地址分配到部署中的以下对象： 

- **Kubernetes 群集 api 服务器** -API 服务器是公开 Kubernetes API 的 Kubernetes 控制平面的组件。 API 服务器是 Kubernetes 控制平面的前端。 API 服务器始终分配静态 IP 地址，而不考虑基础网络模型。

- **虚拟机)  (上的 Kubernetes 节点** -Kubernetes 群集由一组称为节点的辅助角色计算机组成，这些计算机运行容器化应用程序。 每个群集至少具有一个辅助角色节点。 对于 Azure Stack HCI 上的 AKS，Kubernetes 节点在虚拟机中运行。 这些虚拟机是在 Azure Stack HCI 群集的基础上创建的。 

- **Kubernetes services** -在 Kubernetes 中， *服务* 以逻辑方式将 pod 分组，以允许通过 IP 地址或 DNS 名称以及在特定端口上进行直接访问。 此外，还可使用负载均衡器分发流量。 无论采用哪种基础网络模型，Kubernetes 服务始终分配静态 IP 地址。

- **HAProxy 负载均衡** 器-HAProxy (高可用性代理) 为 TCP/HTTP 负载均衡器和代理服务器，允许 web 服务器将传入请求分散到多个终结点。 AKS 中的每个工作负荷群集都有一个在其自己的专用虚拟机中运行的 HAProxy 负载均衡器。

- **Microsoft 本地云服务** -这是用于在本地 Azure Stack HCI 群集上运行 Kubernetes 的 Azure Stack HCI 云提供程序。 后跟 Azure Stack HCI 群集的网络模型确定此对象的 IP 地址分配方法。


## <a name="virtual-networks"></a>虚拟网络

在 Azure Stack HCI 上的 AKS 中，使用虚拟网络将 IP 地址分配到需要它们的 Kubernetes 资源，如上文所述。 根据 Azure Stack HCI 网络体系结构上所需的 AKS，可选择两种网络模型。 请注意，此处为 AKS 定义的虚拟网络体系结构 Azure Stack HCI 部署不同于数据中心的基础物理网络体系结构。

- 静态 IP 网络-虚拟网络将静态 IP 地址分配给 Kubernetes 群集 API 服务器、Kubernetes 节点、底层 Vm、负载均衡器以及在群集之上运行的任何 Kubernetes 服务。

- DHCP 网络-虚拟网络使用 DHCP 服务器将动态 IP 地址分配给 Kubernetes 节点、底层 Vm 和负载均衡器。 在群集顶层运行的 Kubernetes 群集 API 服务器和任何 Kubernetes 服务仍分配有静态 IP 地址。

### <a name="virtual-ip-pool"></a>虚拟 IP 池
虚拟 IP (VIP) 池对于 Azure Stack HCI 部署上的 AKS 是必需的。 VIP 池是一系列保留的静态 IP 地址，用于向 Kubernetes 群集 API 服务器分配 IP 地址，并使用 Kubernetes services 的外部 Ip 来保证你的应用程序始终可访问。 不管虚拟网络模型如何，你都必须为 AKS 主机部署预配 VIP 池。 VIP 池中的 IP 地址数取决于静态和 DHCP 网络模型在部署中运行的工作负荷群集和 Kubernetes 服务的数量。 但是，根据您的网络型号，VIP 池的区别如下：

- 静态 IP-如果使用的是静态 IP，请确保虚拟 ip 来自同一子网。 
- DHCP-如果你的网络配置了 DHCP，你将需要与你的网络管理员合作，并保留一个 IP 范围用于 VIP 池。

### <a name="kubernetes-node-ip-pool"></a>Kubernetes 节点 IP 池
如上所述，Kubernetes 节点在 Azure Stack HCI 部署的 AKS 中的专用虚拟机上运行。 Azure Stack HCI 上的 AKS 会将 IP 地址分配给这些虚拟机，以便在 Kubernetes 节点之间进行通信。 对于 DHCP 网络模式，无需指定 Kubernetes 节点 VM 池，因为网络上的 DHCP 服务器会动态分配 Kubernetes 节点的 IP 地址。 对于基于静态 IP 的网络模型，必须指定 Kubernetes 节点 IP 池范围。 此范围中的 IP 地址数取决于你的 AKS 主机和工作 Kubernetes 群集的 Kubernetes 节点总数。

### <a name="virtual-network-with-static-ip-networking"></a>具有静态 IP 网络的虚拟网络

此网络模型创建一个虚拟网络，该网络将静态 IP 地址分配给部署中的所有对象。 使用静态 IP 网络的一个额外好处是，长期的部署和应用程序工作负荷始终都可以访问。 建议在 Azure Stack HCI 部署中为你的 AKS 创建静态 IP 虚拟网络模型。

定义具有静态 IP 配置的虚拟网络时，必须指定以下参数：

- 名称：虚拟网络的名称
- AddressPrefix：要用于子网的 IP 地址前缀。
- 网关：子网的默认网关的 IP 地址。
- DNS 服务器：指向要用于子网的 DNS 服务器的 IP 地址的数组。 最少可提供1个和3个服务器。
- Kubernetes 节点 IP 池：要用于 Kubernetes 节点 Vm 的 IP 地址的连续范围。
- 虚拟 IP 池：要用于 Kubernetes 群集 API 服务器和 Kubernetes 服务（需要外部 IP 地址）的连续 IP 地址范围。
- vLAN ID：虚拟网络的 vLAN ID。 如果省略，则不会对虚拟网络进行标记。

### <a name="virtual-network-with-dhcp-networking"></a>具有 DHCP 网络的虚拟网络

此网络模型创建一个虚拟网络，该网络将动态 IP 地址分配给部署中的所有对象。  

定义具有静态 IP 配置的虚拟网络时，必须指定以下参数：

- 名称：虚拟网络的名称
- 虚拟 IP 池：要用于 Kubernetes 群集 API 服务器和 Kubernetes 服务（需要外部 IP 地址）的连续 IP 地址范围。
- vLAN ID：虚拟网络的 vLAN ID。 如果省略，则不会对虚拟网络进行标记。

## <a name="microsoft-on-premises-cloud-service"></a>Microsoft 本地云服务

Microsoft 本地云 (MOC) 是新的管理堆栈，可用于在 Azure Stack HCI 和基于 Windows Server 的 SDDC 云上管理虚拟机。 MOC 包含：

- 部署在群集上的高度可用服务的单个实例 `cloud agent` 。 此代理在 Azure Stack HCI 群集的任何一个节点上运行。 
- `node agent`在每个 AZURE STACK HCI 物理节点上运行的。 

`-cloudserviceCIDR`是命令中的一个参数 `Set-AksHciConfig` ，用于分配虚拟 IP 以确保具有高度可用的云代理服务。

分配 MOC 服务的 IP 地址取决于基础网络模型，以及 Azure Stack HCI 群集部署。 **MOC 服务的 IP 地址分配独立于你的虚拟网络模型，它仅依赖于为数据中心中的 Azure Stack HCI 群集节点提供 IP 地址的基础物理网络。**

### <a name="azure-stack-hci-cluster-nodes-with-a-dhcp-based-ip-address-allocation-model"></a>使用基于 DHCP 的 IP 地址分配模型 Azure Stack HCI 群集节点

如果 Azure Stack HCI 节点是从物理网络上存在的 DHCP 服务器分配的 IP 地址，则无需为 MOC 服务显式提供 IP 地址。

### <a name="azure-stack-hci-cluster-nodes-with-a-static-ip-allocation-model"></a>使用静态 IP 分配模型 Azure Stack HCI 群集节点 

如果为 Azure Stack HCI 群集节点分配了静态 IP 地址，则必须为 MOC 云服务显式提供 IP 地址。 此 MOC 服务的 IP 地址必须与 Azure Stack HCI 群集节点位于同一子网中。 若要为 MOC 服务显式分配 IP 地址，请使用 `-cloudserviceCIDR` 命令中的参数 `Set-AksHciConfig` 。 请确保输入 CIDR 格式的 IP 地址，例如 "10.11.23.45/16"。

## <a name="compare-network-models"></a>网络模型的比较

DHCP 和静态 IP 都为 Azure Stack HCI 部署上的 AKS 提供网络连接。 不过，这两个模型各有优缺点。 从较高层面讲，需要考虑以下因素：

* **DHCP**
    - 不保证 Azure Stack HCI 部署的 AKS 中某些资源类型的生存期较长的 IP 地址。
    - 如果你的部署比最初预计的要大，则支持扩展保留的 DHCP IP 地址。
    
* **静态 IP**
    - 为 Azure Stack HCI 部署上的 AKS 中的所有资源保证长生存期的 IP 地址。
    - 由于我们不支持 Kubernetes 节点 IP 池的自动扩展，因此，如果已用尽 Kubernetes 节点 IP 池，则可能无法创建新群集。


下表比较了静态 IP 和 DHCP 网络模型之间资源的 IP 地址分配：

| 功能                                                                                   | 静态 IP   | DHCP |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Kubernetes 群集 API 服务器                                             | 使用 VIP 池静态分配 | 使用 VIP 池静态分配 |
| 虚拟机上的 Kubernetes 节点 ()                                                                          | 使用 Kubernetes 节点 IP 池分配 | 动态分配 |
| Kubernetes 服务                                          | 使用 VIP 池静态分配 | 使用 VIP 池静态分配 |
| HAProxy 负载平衡器 VM                                          | 使用 Kubernetes 节点 IP 池分配 | 动态分配 |
| Microsoft 本地云服务                                               | 取决于 Azure Stack HCI 群集节点的物理网络配置 | 取决于 Azure Stack HCI 群集节点的物理网络配置 |
| VIP 池                                             | 必需 | 必需 |
| Kubernetes 节点 IP 池 | 必需 | 不支持 |


## <a name="minimum-ip-address-reservations-for-an-aks-on-azure-stack-hci-deployment"></a>Azure Stack HCI 部署上的 AKS 的最小 IP 地址保留
不管部署模型如何，保留的 IP 地址数仍保持不变。 本部分介绍基于 Azure Stack HCI 部署模型上的 AKS 而保留的 IP 地址数。

### <a name="minimum-ip-address-reservation"></a>最小 IP 地址保留

你至少应为你的部署保留以下数量的 IP 地址：

| 群集类型  | 控制平面节点 | 工作器节点 | 对于更新操作 | 负载均衡器  |
| ------------- | ------------------ | ---------- | ----------| -------------|
| AKS 主机 |  1 IP |  NA  |  2 IP |  NA  |
| 工作负荷群集  |  每个节点1个 IP  | 每个节点1个 IP |  5 IP  |  1 IP |

此外，还应为 VIP 池保留以下数量的 IP 地址：

| 资源类型  | IP 地址数 
| ------------- | ------------------
| 群集 API 服务器 |  每个群集1个 
| Kubernetes 服务  |  每个服务1个  

正如您所看到的，所需的 IP 地址数是可变的，具体取决于 Azure Stack HCI 体系结构上的 AKS 和 Kubernetes 群集上运行的服务数量。 建议为你的部署保留256个 (/24 子网) 的 IP 地址。

### <a name="walking-through-an-example-deployment"></a>演练部署示例

Jane 是 IT 管理员，只是从 Azure Stack HCI 上的 AKS 开始。 她想要在 Azure Stack HCI 群集上部署2个 Kubernetes 群集-Kubernetes 群集 A 和 Kubernetes 群集 B。 她还希望在自己的群集上运行投票应用程序。 此应用程序具有3个在两个群集上运行的前端 UI 实例和一个后端数据库实例。

- Kubernetes 群集 A 具有3个控制平面节点和5个工作节点
- Kubernetes 群集 B 具有1个控制平面节点和3个工作节点
- 3个前端 UI 实例 (端口 443) 
- 1个后端数据库实例 (端口 80) 

根据上表，她必须保留：

- 3 AKS 主机的 IP 地址 (1 个 IP for control 飞机节点和2个 ip 用于运行更新操作) 
- 3群集中的控制平面节点的 IP 地址 A 每个控制平面节点 (1 个 IP) 
- 5群集中她的辅助节点的 IP 地址 A 每个控制平面节点 (1 个 IP) 
- 6 IP 地址另外，对于群集 A (5 个 ip，用于运行更新操作，1个 IP 用于负载均衡器) 
- 1个群集 B 中的控制平面节点的 IP 地址 (1 个控制面节点的 IP) 
- 在群集 B 中，她的辅助节点的 IP 地址 (每个控制面节点1个 IP) 
- 6 IP 地址，另外还适用于群集 B (5 个 ip 用于运行更新操作，1个 IP 用于负载均衡器) 
- 2个 Kubernetes 群集 API 服务器的 IP 地址 (每个 Kubernetes 群集1个 IP) 
- 3个 Kubernetes 服务的 IP 地址 (前端 UI 的每个实例1个 IP 地址，因为它们都使用同一端口。 后端数据库可以使用三个 IP 地址之一，因为它位于不同的端口上) 

如上所述，Jane 至少需要32个 IP 地址。 因此，Jane 应为其虚拟网络保留一个/26 子网。 

### <a name="splitting-reserved-ip-addresses-based-on-a-static-ip-network-model"></a>基于静态 IP 网络模型拆分保留 IP 地址

尽管保留 IP 地址的总数保持不变，但部署模型确定如何将这些 IP 地址划分为多个 IP 组。 如前文所述，静态 IP 网络模型具有2个 IP 池：

- Kubernetes 节点 IP 池-适用于 Kubernetes 节点 Vm 和负载均衡器 VM。 此 IP 池还包括运行更新操作所需的 IP 地址。
- 虚拟 IP 池-适用于 Kubernetes API 服务器和 Kubernetes 服务

使用上面的示例，Jane 必须在 VIP 池和 Kubernetes 节点 IP 池之间进一步划分这些 IP 地址：
- 5 (2 用于 Kubernetes 群集 API 服务器，3用于 Kubernetes 服务 ) 的 VIP 池的 32 IP 地址。
- 27 (她的 Kubernetes 节点 IP 池的 Kubernetes 节点和底层 Vm 的所有 IP 地址、负载均衡器 Vm 和更新操作) 。

### <a name="splitting-reserved-ip-addresses-based-on-a-dhcp-network-model"></a>基于 DHCP 网络模型拆分保留 IP 地址

尽管保留 IP 地址的总数保持不变，但部署模型确定如何在) 的 IP 组 (之间划分这些 IP 地址。 如前文所述，DHCP 网络模型有1个 IP 池：

- 虚拟 IP 池-适用于 Kubernetes API 服务器和 Kubernetes 服务

使用上面的示例：
- Jane 必须在其 DHCP 服务器上保留总数为32的 IP 地址或/26 子网。 
- 她必须为 Kubernetes 群集 API 服务器保留5个 (2，为 Kubernetes 服务保留3个2个 32) 服务的 IP 地址。 

## <a name="next-steps"></a>后续步骤

本主题介绍了在 Azure Stack HCI 上的应用程序中为应用程序提供网络的核心概念。 接下来可以：

- [在 Azure Stack HCI 上的 AKS 上配置代理设置](./proxy.md)
- [系统要求](./system-requirements.md)

