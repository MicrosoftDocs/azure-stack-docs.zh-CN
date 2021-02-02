---
title: 在 Azure Stack Hub 上运行 Windows 虚拟机
description: 了解如何在 Azure Stack Hub 上运行 Windows 虚拟机。
author: mattbriggs
ms.topic: how-to
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 3/9/2020
ms.openlocfilehash: 5bbad22a6a0998cd56fd3fefc309c14f665b2faa
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247534"
---
# <a name="run-a-windows-virtual-machine-on-azure-stack-hub"></a>在 Azure Stack Hub 上运行 Windows 虚拟机

除 VM 本身以外，在 Azure Stack Hub 中预配虚拟机 (VM) 还需要其他一些组件，包括网络和存储资源。 本文介绍在 Azure 上运行 Windows VM 的最佳做法。

![Azure Stack Hub 上的 Windows VM 体系结构](./media/iaas-architecture-vm-windows/image1.png)

## <a name="resource-group"></a>资源组

[资源组](/azure/azure-resource-manager/resource-group-overview)是保存相关 Azure Stack Hub 资源的逻辑容器。 一般情况下，可根据资源的生存期及其管理者将资源分组。

将共享相同生命周期、密切相关的资源放入同一[资源组](/azure/azure-resource-manager/resource-group-overview)。 资源组可让你以组的形式部署和监视资源，并按资源组跟踪计费成本。 还可以删除作为集的资源，这适用于测试部署。 指定有意义的资源名称，以便简化特定资源的查找并了解其角色。 有关详细信息，请参阅 [Azure 资源的建议命名约定](/azure/architecture/best-practices/naming-conventions)。

## <a name="virtual-machine"></a>虚拟机

可以通过发布的映像列表或上传到 Azure Stack Hub Blob 存储的自定义托管映像或虚拟硬盘 (VHD) 文件来预配 VM。

Azure Stack Hub 提供了与 Azure 不同的虚拟机大小。 有关详细信息，请参阅 [Azure Stack Hub 中的虚拟机大小](./azure-stack-vm-sizes.md)。 若要将现有工作负荷转移到 Azure Stack Hub，一开始请先使用与本地服务器/Azure 最匹配的 VM 大小。 然后从 CPU、内存和每秒磁盘输入/输出操作次数 (IOPS) 等方面测量实际工作负荷的性能，并根据需要调整大小。

## <a name="disks"></a>磁盘

成本取决于预配磁盘的容量。 IOPS 和吞吐量（即数据传输速率）取决于 VM 大小，因此在预配磁盘时，请全面考虑三个因素（容量、IOPS 和吞吐量）。

Azure Stack Hub 上的磁盘 IOPS（每秒输入/输出操作次数）是 [VM 大小](./azure-stack-vm-sizes.md)（而不是磁盘类型）的函数。 这意味着，对于 Standard_Fs 系列 VM，不管你选择 SSD 还是 HDD 作为磁盘类型，单个额外的数据磁盘的 IOPS 限制都是 2300。 施加的 IOPS 限制是一种上限（最大可能值），目的是防止邻域干扰。 它不是你会在特定 VM 大小上获得的 IOPS 的保证。

我们还建议使用[托管磁盘](./azure-stack-managed-disk-considerations.md)。 托管磁盘可代你处理存储，简化磁盘管理。 托管磁盘不需要存储帐户。 只需指定磁盘的大小和类型，就可以将它部署为高度可用的资源。

OS 磁盘是存储在 Azure Stack Hub blob 存储中的 VHD，因此即使主机关闭，OS 磁盘也仍然存在。 我们还建议创建一个或多个[数据磁盘](./azure-stack-manage-vm-disks.md)（用于保存应用程序数据的持久性 VHD）。 如果可能，请将应用程序安装在数据磁盘上，而不是 OS 磁盘上。 某些旧版应用程序可能需要将组件安装在 C: 驱动器上；在这种情况下，可使用 PowerShell [重设 OS 磁盘的大小](/azure/virtual-machines/windows/expand-os-disk)。

还使用临时磁盘（Windows 上的 D: 驱动器）创建 VM。 此磁盘存储在 Azure Stack Hub 存储基础结构中的临时卷上。 它在重新启动期间以及发生其他 VM 生命周期事件期间可能会被删除。 只使用此磁盘存储临时数据，如页面文件或交换文件。

## <a name="network"></a>网络

网络组件包括以下资源：

-   虚拟网络  。 每个 VM 都会部署到可细分为多个子网的虚拟网络中。

-   **网络接口 (NIC)** 。 NIC 使 VM 能够与虚拟网络进行通信。 如果 VM 需要多个 NIC，请注意每种 [VM 大小](./azure-stack-vm-sizes.md)都定义了最大 NIC 数量。

-   **公共 IP 地址/VIP**。 需要使用公共 IP 地址才能与 VM 通信 - 例如，通过远程桌面 (RDP)。 公共 IP 地址可以是动态的或静态的。 默认是动态的。

-   如果需要不会更改的固定 IP 地址 — 例如，如果需要创建 DNS 'A' 记录或将 IP 地址添加到安全列表，请保留[静态 IP 地址](/azure/virtual-network/virtual-networks-reserved-public-ip)。

-   还可以为 IP 地址创建完全限定域名 (FQDN)。 然后，可以在 DNS 中注册指向 FQDN 的 [CNAME 记录](https://en.wikipedia.org/wiki/CNAME_record)。 有关详细信息，请参阅[在 Azure 门户中创建完全限定的域名](/azure/virtual-machines/windows/portal-create-fqdn)。

-   **网络安全组 (NSG)** 。 NSG 用于允许或拒绝流向 VM 的网络流量。 NSG 可与子网或单个 VM 实例相关联。

所有 NSG 都包含一组[默认规则](/azure/virtual-network/security-overview#default-security-rules)，其中包括阻止所有入站 Internet 流量的规则。 无法删除默认规则，但其他规则可以覆盖它们。 若要启用 Internet 流量，请创建允许特定端口的入站流量的规则 — 例如，将端口 80 用于 HTTP。 若要启用 RDP，请添加允许 TCP 端口 3389 的入站流量的 NSG 规则。

## <a name="operations"></a>操作

**诊断**。 启用监视和诊断，包括基本运行状况指标、诊断基础结构日志和[启动诊断](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)。 如果 VM 陷入不可启动状态，启动诊断有助于诊断启动故障。 创建用于存储日志的 Azure 存储帐户。 标准的本地冗余存储 (LRS) 帐户足以存储诊断日志。 有关详细信息，请参阅[启用监视和诊断](./azure-stack-metrics-azure-data.md)。

**可用性**。 由于 Azure Stack Hub 操作员计划的计划内维护，你的VM 可能需要重新启动。 为了在 Azure 中实现多 VM 生产系统的高可用性，可以将 VM 置于横跨多个容错域和更新域的[可用性集](/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)中。 在较小规模的 Azure Stack Hub 中，可用性集中的容错域定义为缩放单元中的单个节点。  

在发生硬件故障时，虽然 Azure Stack Hub 的基础结构已具备故障还原能力，但基础技术（故障转移群集功能）的局限仍会导致受影响物理服务器上的 VM 出现停机。 为了与 Azure 保持一致，Azure Stack Hub 支持的可用性集最多有三个容错域。

|                   |             |
|-------------------|-------------|
| **容错域** | 置于可用性集中的 VM 在物理上是彼此隔离的，换句话说，会尽可能均衡地让其分散到多个容错域（Azure Stack Hub 节点）中。 如果发生硬件故障，出现故障的容错域中的 VM 将在其他容错域中重启。 它们保留在与其他 VM 不同的容错域中，但如果可能，则保留在相同的可用性集中。 当硬件重新联机时，会对 VM 重新进行均衡操作，以维持高可用性。 |
| **更新域**| 更新域是 Azure 在可用性集中提供高可用性的另一种方法。 更新域是可以同时维护的基础硬件逻辑组。 同一个更新域中的 VM 会在计划内维护期间一起重启。 当租户在可用性集内创建 VM 时，Azure 平台会自动将 VM 分布到这些更新域。 <br>在 Azure Stack Hub 中，VM 会先跨群集中的其他联机主机进行实时迁移，然后其基础主机才会进行更新。 由于在主机更新期间不会造成租户停机，因此 Azure Stack Hub 上存在更新域功能只是为了确保与 Azure 实现模板兼容。 可用性集中的 VM 将显示 0 作为其在门户上的更新域编号。 |

**备份** 有关保护 Azure Stack Hub IaaS VM 的建议，请参阅 [保护在 Azure Stack Hub 上部署的 VM](azure-stack-manage-vm-protect.md)。

**停止 VM**。 Azure 对“已停止”和“已解除分配”状态做了区分。 当 VM 状态为已停止时（而不是当 VM 已解除分配时）将向你收费。 在 Azure Stack Hub 门户中，“停止”  按钮可解除分配 VM。 如果在已登录时通过 OS 关闭，VM 会停止，但  不会解除分配，因此仍会产生费用。

**删除 VM**。 如果删除 VM，不会删除 VM 磁盘。 这意味着可以安全地删除 VM，而不会丢失数据。 但是，仍将向你收取存储费用。 若要删除 VM 磁盘，请删除托管磁盘对象。 若要防止意外删除，请使用 *资源锁* 锁定整个资源组或锁定单个资源（如 VM）。

## <a name="security-considerations"></a>安全注意事项

将 VM 载入到 [Azure 安全中心](/azure/security-center/quick-onboard-azure-stack)以获取 Azure 资源的安全状态的中心视图。 安全中心监视潜在的安全问题，并全面描述了部署的安全运行状况。 安全中心针对每个 Azure 订阅进行配置。 根据[将 Azure 订阅载入安全中心标准版](/azure/security-center/security-center-get-started)中所述启用安全数据收集。 启用数据收集后，安全中心会自动扫描该订阅下创建的所有 VM。

**修补程序管理**。 若要在 VM 上配置修补程序管理，请参阅[此文](./vm-update-management.md)。 如果启用，安全中心会检查是否缺少任何安全更新和关键更新。 使用 VM 上的[组策略设置](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)可启用自动系统更新。

**反恶意软件**。 如果启用，安全中心会检查是否已安装反恶意软件。 还可以使用安全中心从 Azure 门户中安装反恶意软件。

**访问控制**。 使用[基于角色的访问控制 (RBAC)](/azure/active-directory/role-based-access-control-what-is) 来控制对 Azure 资源的访问。 RBAC 允许将授权角色分配给开发运营团队的成员。 例如，“读者”角色可以查看 Azure 资源，但不能创建、管理或删除这些资源。 某些权限特定于 Azure 资源类型。 例如，“虚拟机参与者”角色可以执行重启或解除分配 VM、重置管理员密码、创建新的 VM 等操作。 可能对此体系结构有用的其他[内置 RBAC 角色](/azure/active-directory/role-based-access-built-in-roles)包括[开发测试实验室用户](/azure/active-directory/role-based-access-built-in-roles#devtest-labs-user)和[网络参与者](/azure/active-directory/role-based-access-built-in-roles#network-contributor)。

> [!NOTE]  
> RBAC 不限制已登录到 VM 的用户可以执行的操作。 这些权限由来宾 OS 上的帐户类型决定。

**审核日志**。 使用[活动日志](./azure-stack-metrics-azure-data.md?#activity-log)可查看预配操作和其他 VM 事件。

**数据加密**。 Azure Stack Hub 使用 BitLocker 128 位 AES 加密在存储子系统中保护用户和基础结构静态数据。 有关详细信息，请参阅 [Azure Stack Hub 中的静态数据加密](../operator/azure-stack-security-bitlocker.md)。


## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure Stack Hub VM，请参阅 [Azure Stack Hub VM 功能](azure-stack-vm-considerations.md)。  
- 若要详细了解 Azure 云模式，请参阅[云设计模式](/azure/architecture/patterns)。
