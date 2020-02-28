---
title: 在 Azure Stack 集线器上运行 Windows 虚拟机
description: 了解如何在 Azure Stack 集线器上运行 Windows 虚拟机。
author: mattbriggs
ms.topic: how-to
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 1bd438802c00a1c00b10f81920c1756d22f83606
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704975"
---
# <a name="run-a-windows-virtual-machine-on-azure-stack-hub"></a>在 Azure Stack 集线器上运行 Windows 虚拟机

在 Azure Stack 集线器中预配虚拟机（VM）除了 VM 本身以外，还需要一些其他组件，包括网络和存储资源。 本文介绍在 Azure 上运行 Windows VM 的最佳做法。

![Azure Stack 集线器上的 Windows VM 体系结构](./media/iaas-architecture-vm-windows/image1.png)

## <a name="resource-group"></a>资源组

[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)是保存相关 Azure Stack 集线器资源的逻辑容器。 一般情况下，可根据资源的生存期及其管理者将资源分组。

将共享相同生命周期的密切关联资源置于同一[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)中。 资源组可让你以组的形式部署和监视资源，并按资源组跟踪计费成本。 还可以删除作为集的资源，这适用于测试部署。 指定有意义的资源名称，以便简化特定资源的查找并了解其角色。 有关详细信息，请参阅[Azure 资源的建议命名约定](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。

## <a name="virtual-machine"></a>虚拟机

可以从已发布映像的列表或上传到 Azure Stack 集线器 Blob 存储的自定义映像或虚拟硬盘（VHD）文件预配 VM。

Azure Stack 中心从 Azure 提供不同的虚拟机大小。 有关详细信息，请参阅[Azure Stack Hub 中虚拟机的大小](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)。 如果要将现有工作负荷移动到 Azure Stack 中心，请从与本地服务器/Azure 最匹配的 VM 大小开始。 然后从 CPU、内存和每秒磁盘输入/输出操作次数 (IOPS) 等方面测量实际工作负荷的性能，并根据需要调整大小。

## <a name="disks"></a>磁盘

成本取决于预配磁盘的容量。 IOPS 和吞吐量（即数据传输速率）取决于 VM 大小，因此在预配磁盘时，请考虑所有三个因素（容量、IOPS 和吞吐量）。

Azure Stack 集线器上的磁盘 IOPS （每秒输入/输出操作数）是[VM 大小](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)的函数，而不是磁盘类型。 这意味着，对于 Standard_Fs 系列 VM，无论你是为磁盘类型选择 SSD 还是 HDD，一个额外的数据磁盘的 IOPS 限制为 2300 IOPS。 施加的 IOPS 限制是一个上限（可能的最大值），以防止邻居干扰。 这并不能保证你会获得特定 VM 大小的 IOPS。

我们还建议使用[托管磁盘](https://docs.microsoft.com/azure-stack/user/azure-stack-managed-disk-considerations)。 托管磁盘可代你处理存储，简化磁盘管理。 托管磁盘不需要存储帐户。 只需指定磁盘的大小和类型，就可以将它部署为高度可用的资源。

OS 磁盘是存储在 Azure Stack 集线器 blob 存储中的 VHD，因此即使主机关闭也是如此。 建议创建一个或多个[数据磁盘](https://docs.microsoft.com/azure-stack/user/azure-stack-manage-vm-disks)，这些磁盘是用于应用程序数据的持久性 vhd。 如果可能，请将应用程序安装在数据磁盘上，而不是 OS 磁盘上。 某些旧版应用程序可能需要将组件安装在 C：驱动器上;在这种情况下，可以使用 PowerShell[调整 OS 磁盘的大小](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-expand-os-disk)。

还会使用临时磁盘（Windows 上的 D：驱动器）创建 VM。 此磁盘存储在 Azure Stack 中心存储基础结构中的临时卷上。 它可能在重新启动和其他 VM 生命周期事件时被删除。 只使用此磁盘存储临时数据，如页面文件或交换文件。

## <a name="network"></a>网络

网络组件包括以下资源：

-   虚拟网络。 每个 VM 都会部署到可细分为多个子网的虚拟网络中。

-   **网络接口 (NIC)** 。 NIC 使 VM 能够与虚拟网络进行通信。 如果 VM 需要多个 Nic，请注意，已为每个[vm 大小](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)定义了最大数量的 nic。

-   **公共 IP 地址/VIP**。 需要公共 IP 地址才能与 VM 通信，例如，通过远程桌面（RDP）。 公共 IP 地址可以是动态的或静态的。 默认是动态的。

-   如果需要固定的 IP 地址（例如，需要创建 DNS "A" 记录或将 IP 地址添加到安全列表），请保留[静态 ip 地址](https://docs.microsoft.com/azure/virtual-network/virtual-networks-reserved-public-ip)。

-   还可以为 IP 地址创建完全限定域名 (FQDN)。 然后，你可以在 DNS 中注册指向 FQDN 的[CNAME 记录](https://en.wikipedia.org/wiki/CNAME_record)。 有关详细信息，请参阅[在 Azure 门户中创建完全限定的域名](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-portal-create-fqdn)。

-   **网络安全组 (NSG)** 。 Nsg 用于允许或拒绝到 Vm 的网络流量。 NSG 可与子网或单个 VM 实例相关联。

所有 Nsg 都包含一组[默认规则](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)，其中包括阻止所有入站 Internet 流量的规则。 无法删除默认规则，但其他规则可以覆盖它们。 若要启用 Internet 流量，请创建允许特定端口的入站流量的规则（例如，HTTP 的端口80）。 若要启用 RDP，请添加允许 TCP 端口 3389 的入站流量的 NSG 规则。

## <a name="operations"></a>操作

**诊断**。 启用监视和诊断，包括基本运行状况指标、诊断基础结构日志和[启动诊断](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)。 如果 VM 陷入不可启动状态，启动诊断有助于诊断启动故障。 创建用于存储日志的 Azure 存储帐户。 标准的本地冗余存储 (LRS) 帐户足以存储诊断日志。 有关详细信息，请参阅[启用监视和诊断](https://docs.microsoft.com/azure-stack/user/azure-stack-metrics-azure-data)。

**可用性**。 由于 Azure Stack 中心操作员计划的维护，你的 VM 可能会因计划内维护而重新启动。 为了实现 Azure 中多 VM 生产系统的高可用性，将 Vm 放置在一个[可用性集中](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)，以跨多个容错域和更新域传播它们。 在较小的 Azure Stack 中心范围内，可用性集中的容错域定义为缩放单位中的单个节点。  

尽管 Azure Stack 中心的基础结构已对故障有弹性，但如果出现硬件故障，基础技术（故障转移群集）仍会对受影响的物理服务器上的 Vm 产生一些停机时间。 Azure Stack 中心支持拥有最多三个容错域的可用性集，以便与 Azure 保持一致。

|                   |             |
|-------------------|-------------|
| **容错域** | 放置在可用性集中的 Vm 将以物理方式彼此隔离，方法是在多个容错域（Azure Stack 中心节点）之间均匀分配它们。 如果发生硬件故障，故障容错域中的 Vm 将在其他容错域中重新启动。 它们将保存在与其他 Vm 不同的容错域中，但可能会保留在同一可用性集中。 当硬件重新联机时，将重新平衡 Vm 以保持高可用性。 |
| **更新域**| 更新域是 Azure 在可用性集中提供高可用性的另一种方法。 更新域是一组可同时进行维护的基础硬件逻辑组。 位于同一更新域中的 Vm 将在计划内维护期间一起重新启动。 当租户在可用性集中创建 Vm 时，Azure 平台会自动将 Vm 分布到这些更新域。 <br>在 Azure Stack 集线器中，Vm 在其基础主机更新之前，会在群集中的其他联机主机之间进行实时迁移。 由于在主机更新期间不会出现任何租户停机时间，因此，Azure Stack 集线器上的更新域功能只存在于与 Azure 的模板兼容性。 可用性集中的 Vm 会在门户上显示0作为其更新域的编号。 |

**备份**有关保护 Azure Stack 中心 IaaS Vm 的建议，请参阅此文。

**停止 VM**。 Azure 对“已停止”和“已解除分配”状态做了区分。 当 VM 状态为已停止时（而不是当 VM 已解除分配时）将向你收费。 在 Azure Stack 集线器门户中，"**停止**" 按钮会释放 VM。 如果在已登录时通过 OS 关闭，VM 会停止，但不会解除分配，因此仍会产生费用。

**删除 VM**。 如果删除 VM，则不会删除 VM 磁盘。 这意味着可以安全地删除 VM，而不会丢失数据。 但是，仍将向你收取存储费用。 若要删除 VM 磁盘，请删除托管磁盘对象。 若要防止意外删除，请使用*资源锁*锁定整个资源组或锁定单个资源（如 VM）。

## <a name="security-considerations"></a>安全注意事项

将 Vm 集成到[Azure 安全中心](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)，以便集中查看 azure 资源的安全状态。 安全中心监视潜在的安全问题，并全面描述了部署的安全运行状况。 安全中心针对每个 Azure 订阅进行配置。 启用安全数据收集，如将[Azure 订阅与安全中心标准集成](https://docs.microsoft.com/azure/security-center/security-center-get-started)中所述。 启用数据收集后，安全中心会自动扫描该订阅下创建的所有 VM。

**修补程序管理**。 若要在 VM 上配置修补管理，请参阅[此](https://docs.microsoft.com/azure-stack/user/vm-update-management)文。 如果启用，安全中心会检查是否缺少任何安全更新和关键更新。 使用 VM 上[组策略设置](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)来启用自动系统更新。

**反恶意软件**。 如果启用，安全中心会检查是否已安装反恶意软件。 还可以使用安全中心从 Azure 门户中安装反恶意软件。

**访问控制**。 使用[基于角色的访问控制（RBAC）](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)来控制对 Azure 资源的访问。 RBAC 允许将授权角色分配给开发运营团队的成员。 例如，“读者”角色可以查看 Azure 资源，但不能创建、管理或删除这些资源。 某些权限特定于 Azure 资源类型。 例如，“虚拟机参与者”角色可以执行重启或解除分配 VM、重置管理员密码、创建新的 VM 等操作。 可用于此体系结构的其他[内置 RBAC 角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)包括[开发测试实验室用户](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#devtest-labs-user)和[网络参与者](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#network-contributor)。

> [!Note]  
> RBAC 不限制已登录到 VM 的用户可以执行的操作。 这些权限由来宾 OS 上的帐户类型决定。

**审核日志**。 使用[活动日志](https://docs.microsoft.com/azure-stack/user/azure-stack-metrics-azure-data?#activity-log)查看预配操作和其他 VM 事件。

**数据加密**。 Azure Stack 集线器使用 BitLocker 128 位 AES 加密来保护存储子系统中的静态用户和基础结构数据。 有关详细信息，请参阅[Azure Stack 集线器中的静态数据加密](https://docs.microsoft.com/azure-stack/operator/azure-stack-security-bitlocker)。


## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure Stack 集线器 vm，请参阅[Azure Stack 中心 vm 功能](azure-stack-vm-considerations.md)。  
- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
