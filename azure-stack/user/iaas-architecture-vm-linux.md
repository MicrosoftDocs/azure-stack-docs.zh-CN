---
title: 在 Azure Stack 上运行 Linux 虚拟机 |Microsoft Docs
description: 了解如何在 Azure Stack 上运行 Linux 虚拟机。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/01/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: e51f1bd10ad53671d4e3b60e448141207bf2f6e0
ms.sourcegitcommit: 8a74a5572e24bfc42f71e18e181318c82c8b4f24
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73569135"
---
# <a name="run-a-linux-virtual-machine-on-azure-stack"></a>在 Azure Stack 上运行 Linux 虚拟机

在 Azure Stack （如 Azure）中预配虚拟机（VM）时，除了 VM 本身还需要一些其他组件，包括网络和存储资源。 本文介绍了在 Azure Stack 上运行 Linux VM 的最佳实践。

![Azure Stack 上的 Linux VM 体系结构](./media/iaas-architecture-vm-linux/image1.png)

## <a name="resource-group"></a>资源组

[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)是保存相关 Azure Stack 资源的逻辑容器。 一般情况下，可根据资源的生存期及其管理者将资源分组。

将共享相同生命周期的密切关联资源置于同一[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)中。 资源组可让你以组的形式部署和监视资源，并按资源组跟踪计费成本。 还可以删除作为集的资源，这适用于测试部署。 指定有意义的资源名称，以便简化特定资源的查找并了解其角色。 有关详细信息，请参阅[Azure 资源的建议命名约定](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions)。

## <a name="virtual-machine"></a>虚拟机

可以从已发布映像的列表或上传到 Azure Stack Blob 存储的自定义映像或虚拟硬盘（VHD）文件预配 VM。 Azure Stack 支持运行各种热门 Linux 分发，包括 CentOS、Debian、Red Hat Enterprise、Ubuntu 和 SUSE。 有关详细信息，请参阅[Azure Stack 上的 Linux](https://docs.microsoft.com/azure-stack/operator/azure-stack-linux)。 你还可以选择联合 Azure Stack marketplace 上可用的已发布 Linux 映像之一。

Azure Stack 提供 Azure 提供的不同虚拟机大小。 有关详细信息，请参阅[Azure Stack 中虚拟机的大小](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)。 如果要将现有工作负荷移动到 Azure Stack，请从与本地服务器/Azure 最匹配的 VM 大小开始。 然后从 CPU、内存和每秒磁盘输入/输出操作次数 (IOPS) 等方面测量实际工作负荷的性能，并根据需要调整大小。

## <a name="disks"></a>磁盘

成本取决于预配磁盘的容量。 IOPS 和吞吐量（即数据传输速率）取决于 VM 大小，因此在预配磁盘时，请考虑所有三个因素（容量、IOPS 和吞吐量）。

Azure Stack 上的磁盘 IOPS （每秒输入/输出操作数）是[VM 大小](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)的函数，而不是磁盘类型。 这意味着，对于 Standard_Fs 系列 VM，不管你选择 SSD 还是 HDD 作为磁盘类型，单个额外的数据磁盘的 IOPS 限制都是 2300。 施加的 IOPS 限制是一个上限（可能的最大值），以防止邻居干扰。 它不是你会在特定 VM 大小上获得的 IOPS 的保证。

我们还建议使用[托管磁盘](https://docs.microsoft.com/azure-stack/user/azure-stack-managed-disk-considerations)。 托管磁盘可代你处理存储，简化磁盘管理。 托管磁盘不需要存储帐户。 只需指定磁盘的大小和类型，就可以将它部署为高度可用的资源。

OS 磁盘是存储在[Azure Stack 存储](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-overview)中的 VHD，因此即使主机关闭时也会保留。 对于 Linux Vm，操作系统磁盘为/dev/sda1。 建议创建一个或多个[数据磁盘](https://docs.microsoft.com/azure-stack/user/azure-stack-manage-vm-disks)，这些磁盘是用于应用程序数据的持久性 vhd。

刚创建的 VHD 尚未格式化， 登录到 VM 对磁盘进行格式化。 在 Linux shell 中，数据磁盘显示为/dev/sdc、/dev/sdd 等。 可以运行 lsblk 来列出块设备，包括磁盘。 若要使用数据磁盘，请创建一个分区和文件系统，然后装载磁盘。 例如：

```bash
# Create a partition.
sudo fdisk /dev/sdc \# Enter 'n' to partition, 'w' to write the change.

# Create a file system.
sudo mkfs -t ext3 /dev/sdc1

# Mount the drive.
sudo mkdir /data1
sudo mount /dev/sdc1 /data1
```

在添加数据磁盘时，将为磁盘分配逻辑单元号 (LUN) ID。 或者，你可以指定 LUN ID （例如，如果你要更换磁盘并希望保留相同的 LUN ID），或者你的应用程序会查找特定的 LUN ID。 但请记住，每个磁盘的 LUN ID 必须唯一。

使用临时磁盘创建 VM。 此磁盘存储在 Azure Stack 存储基础结构上的一个临时卷上。 它可能在重新启动和其他 VM 生命周期事件时被删除。 只使用此磁盘存储临时数据，如页面文件或交换文件。 对于 Linux Vm，临时磁盘是/dev/sdb1，并装载到/mnt/resource 或/mnt。

## <a name="network"></a>网络

网络组件包括以下资源：

-   虚拟网络。 每个 VM 都会部署到可细分为多个子网的虚拟网络中。

-   **网络接口 (NIC)** 。 NIC 使 VM 能够与虚拟网络进行通信。 如果 VM 需要多个 Nic，请注意，已为每个[vm 大小](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)定义了最大数量的 nic。

-   **公共 IP 地址/VIP**。 需要公共 IP 地址才能与 VM 通信，例如，通过远程桌面（RDP）。 公共 IP 地址可以是动态的或静态的。 默认是动态的。 如果 VM 需要多个 Nic，请注意，已为每个[vm 大小](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)定义了最大数量的 nic。

-   还可以为 IP 地址创建完全限定域名 (FQDN)。 然后，你可以在 DNS 中注册指向 FQDN 的[CNAME 记录](https://en.wikipedia.org/wiki/CNAME_record)。 有关详细信息，请参阅[在 Azure 门户中创建完全限定的域名](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-portal-create-fqdn)。

-   **网络安全组（NSG）。** 网络安全组用于允许或拒绝到 Vm 的网络流量。 NSG 可与子网或单个 VM 实例相关联。

所有 Nsg 都包含一组[默认规则](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules)，其中包括阻止所有入站 Internet 流量的规则。 无法删除默认规则，但其他规则可以覆盖它们。 若要启用 Internet 流量，请创建允许特定端口的入站流量的规则（例如，HTTP 的端口80）。 要启用 SSH，请添加允许 TCP 端口 22 的入站流量的 NSG 规则。

## <a name="operations"></a>操作

**SSH**。 在创建 Linux VM 之前，生成 2048 位 RSA 公共/专用密钥对。 创建 VM 时，使用公钥文件。 有关详细信息，请参阅[如何在 Azure 上将 SSH 与 Linux 配合使用](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-mac-create-ssh-keys)。

**诊断**。 启用监视和诊断，包括基本运行状况指标、诊断基础结构日志和[启动诊断](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)。 如果 VM 陷入不可启动状态，启动诊断有助于诊断启动故障。 创建用于存储日志的 Azure 存储帐户。 标准的本地冗余存储 (LRS) 帐户足以存储诊断日志。 有关详细信息，请参阅[启用监视和诊断](https://docs.microsoft.com/azure-stack/user/azure-stack-metrics-azure-data)。

**可用性**。 由于 Azure Stack 操作员计划的维护，你的 VM 可能会因计划内维护而重新启动。 为了提高可用性，请在[可用性集](https://docs.microsoft.com/azure-stack/operator/azure-stack-overview#providing-high-availability)中部署多个 VM。

**备份**有关保护 Azure Stack IaaS Vm 的建议，请参阅[此](https://docs.microsoft.com/azure-stack/user/azure-stack-manage-vm-protect)文。

**停止 VM**。 Azure 对“已停止”和“已解除分配”状态做了区分。 当 VM 状态为已停止时（而不是当 VM 已解除分配时）将向你收费。 在 Azure Stack 门户中，"**停止**" 按钮会释放 VM。 如果在已登录时通过 OS 关闭，VM 会停止，但不会解除分配，因此仍会产生费用。

**删除 VM**。 如果删除 VM，则不会删除 VM 磁盘。 这意味着可以安全地删除 VM，而不会丢失数据。 但是，仍将向你收取存储费用。 若要删除 VM 磁盘，请删除托管磁盘对象。 若要防止意外删除，请使用[资源锁](https://docs.microsoft.com/azure/resource-group-lock-resources)锁定整个资源组或锁定单个资源（如 VM）。

## <a name="security-considerations"></a>安全注意事项

将 Vm 集成到[Azure 安全中心](https://docs.microsoft.com/azure/security-center/quick-onboard-azure-stack)，以便集中查看 azure 资源的安全状态。 安全中心监视潜在的安全问题，并全面描述了部署的安全运行状况。 安全中心针对每个 Azure 订阅进行配置。 启用安全数据收集，如将[Azure 订阅与安全中心标准集成](https://docs.microsoft.com/azure/security-center/security-center-get-started)中所述。 启用数据收集后，安全中心会自动扫描该订阅下创建的所有 VM。

**修补程序管理**。 若要在 VM 上配置修补管理，请参阅[此](https://docs.microsoft.com/azure-stack/user/vm-update-management)文。 如果启用，安全中心会检查是否缺少任何安全更新和关键更新。 使用 VM 上[组策略设置](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)来启用自动系统更新。

**反恶意软件**。 如果启用，安全中心会检查是否已安装反恶意软件。 还可以使用安全中心从 Azure 门户中安装反恶意软件。

**访问控制**。 使用[基于角色的访问控制（RBAC）](https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is)来控制对 Azure 资源的访问。 RBAC 允许将授权角色分配给开发运营团队的成员。 例如，“读者”角色可以查看 Azure 资源，但不能创建、管理或删除这些资源。 某些权限特定于 Azure 资源类型。 例如，“虚拟机参与者”角色可以执行重启或解除分配 VM、重置管理员密码、创建新的 VM 等操作。 可用于此体系结构的其他[内置 RBAC 角色](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)包括[开发测试实验室用户](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#devtest-labs-user)和[网络参与者](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles#network-contributor)。

> [!Note]  
> RBAC 不限制已登录到 VM 的用户可以执行的操作。 这些权限由来宾 OS 上的帐户类型决定。

**审核日志**。 使用[活动日志](https://docs.microsoft.com/azure-stack/user/azure-stack-metrics-azure-data?#activity-log)查看预配操作和其他 VM 事件。

**数据加密**。 Azure Stack 使用静态加密来保护存储子系统级别的用户数据和基础结构数据。 Azure Stack 的存储子系统是配合 128 位 AES 加密法使用 BitLocker 加密的。 有关更多详细信息，请参阅[此](https://docs.microsoft.com/azure-stack/operator/azure-stack-security-bitlocker)文。

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure Stack Vm，请参阅[AZURE STACK VM 功能](azure-stack-vm-considerations.md)。  
- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
