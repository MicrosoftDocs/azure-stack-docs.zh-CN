---
title: 保护在 Azure Stack Hub 上部署的 VM
description: 了解如何生成恢复计划来保护 Azure Stack Hub 上部署的 VM，以防出现数据丢失和计划外停机。
author: mattbriggs
ms.topic: conceptual
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/5/2020
ms.openlocfilehash: 5634cd783a010f5aa45de88ba923dfe6a8378c4c
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742717"
---
# <a name="protect-vms-deployed-on-azure-stack-hub"></a>保护在 Azure Stack Hub 上部署的 VM

使用本文作为指南，为部署在 Azure Stack Hub 上的用户部署型 IaaS 虚拟机 (VM) 制定数据保护和灾难恢复策略。

为了防止数据丢失和停机时间过长，请为用户应用程序及其数据实施备份恢复或灾难恢复计划。 每个应用程序都必须作为组织全面的业务连续性和灾难恢复的一部分进行评估 (BC/DR) 策略。 可以从 [Azure Stack Hub：业务连续性和灾难恢复的注意事项](https://aka.ms/azurestackbcdrconsiderationswp)着手。

## <a name="considerations-for-protecting-iaas-vms"></a>有关 IaaS VM 保护的注意事项

### <a name="roles-and-responsibilities"></a>角色和职责

首先，请确保对应用程序所有者和操作员在保护和恢复过程中的角色和职责有清楚的了解。

用户负责保护 VM。 操作员负责使 Azure Stack Hub 保持联机并正常运行。 Azure Stack Hub 包含的一项服务可用于从基础结构服务备份内部服务数据，这其中不包含任何用户数据（例如用户创建的VM、包含用户数据或应用程序数据的存储帐户，或用户数据库）。


| 应用程序所有者/架构师   | Azure Stack Hub 操作员  |
|---    |---    |
| <ul><li>使应用程序体系结构符合云设计原则。</li></br><li>根据需要使传统应用程序现代化，针对云环境做好准备。</li></br><li>为应用程序定义可接受的 RTO 和 RPO。</li></br><li>确定需要保护的应用程序资源和数据存储库。</li></br><li>实施与应用程序体系结构和客户要求最相符的数据和应用程序恢复方法。</li></ul>     | <ul><li>确定组织的业务连续性和灾难恢复目标。</li></br><li>部署足够的 Azure Stack Hub 实例，以满足组织的业务连续性/灾难恢复目标。</li></br><li>设计并操作应用程序/数据保护基础设施。</li></br><li>提供针对保护服务的托管解决方案或自助访问权限。</li></br><li>与应用程序所有者/架构师合作，了解应用程序设计并推荐保护策略。</li></br><li>启用进行服务修复和云恢复所需的基础设施备份。</li></ul>    |

## <a name="sourcetarget-combinations"></a>源/目标组合

需要针对数据中心或站点服务中断情况提供防护措施的用户可以使用另一个 Azure Stack Hub 或 Azure 来确保高可用性或快速恢复。 使用主位置和辅助位置，用户可以在两个环境中采用主动/主动或主动/被动配置来部署应用程序。 对于不太重要的工作负荷，用户可以使用辅助位置中的容量从备份中执行应用程序的按需还原。

可以将一个或多个 Azure Stack Hub 云部署到数据中心。 为了在重大灾难中存活下来，可以在另一个数据中心内部署至少一个 Azure Stack Hub 云，确保你能够对工作负荷进行故障转移，并最大程度地缩短计划外停机时间。 如果只有一个 Azure Stack Hub，则应考虑使用 Azure 公有云作为恢复云。 应用程序运行位置的确定将取决于政府法规、公司策略和延迟要求的严格程度。 你可以灵活地确定每个应用程序的适当恢复位置。 例如，可以让一个订阅中的应用程序将数据备份到另一个数据中心，让另一个订阅中的应用程序将数据复制到 Azure 公有云。

## <a name="application-recovery-objectives"></a>应用程序恢复目标

应用程序所有者主要负责确定应用程序和组织可以容忍的停机时间和数据丢失量。 通过对可接受的停机时间和可接受的数据丢失进行量化，你可以制定恢复计划，将灾难对组织的影响降到最低。 对于每个应用程序，请考虑以下事项：

 - **恢复时间目标 (RTO)**  
RTO 是指发生某个事件后，可接受应用不可用的最长时间。 例如，如果 RTO 是 90 分钟，则意味着从发生灾难开始，必须能够在 90 分钟内将应用还原到正常运行状态。 如果 RTO 低，可以持续运转一个后备部署，以防范区域性服务中断。
 - **恢复点目标 (RPO)**  
RPO 是指发生灾难期间，可接受数据丢失的最大持续时间。 例如，如果在单个数据库中存储数据并且未将数据复制到其他数据库，而是执行每小时备份，则最长可能会丢失一小时的数据。

另一个指标是*平均恢复时间* (MTTR)，指的是发生故障后还原应用程序所需的平均时间。 MTTR 反映的是系统的经验值。 如果 MTTR 超过 RTO，则系统发生故障会导致不可接受的业务中断，因为无法在定义的 RTO 内将系统还原。

## <a name="protection-options"></a>保护选项 

### <a name="backup-restore"></a>备份-还原

备份应用程序和数据集后，如果因数据损坏、意外删除或灾难而导致停机，你将能够快速恢复。 对于基于 IaaS VM 的应用程序，你可以使用来宾内代理来保护应用程序数据、操作系统配置以及存储在卷上的数据。 

#### <a name="backup-using-in-guest-agent"></a>使用来宾内代理进行备份

使用来宾 OS 代理备份 VM 通常包括捕获操作系统配置、文件/文件夹、磁盘、应用程序二进制文件或应用程序数据。 

从代理恢复应用程序需要以手动方式重新创建 VM，并安装操作系统和来宾代理。 然后就可以将数据还原到来宾 OS 或直接还原到应用程序。

#### <a name="backup-using-disk-snapshot-for-stopped-vms"></a>使用已停止的 VM 的磁盘快照进行备份

备份产品可以保护附加到已停止 VM 的 IaaS VM 配置和磁盘。 使用集成了 Azure Stack Hub API 的备份产品来捕获 VM 配置并创建磁盘快照。 如果可以安排应用程序停机，请在启动备份工作流之前确保 VM 处于已停止状态。  

#### <a name="backup-using-disk-snapshot-snapshot-for-running-vms"></a>使用运行中的 VM 的磁盘快照进行备份

> [!Important]  
> 对于处于运行状态的 VM，当前不支持使用磁盘快照。 创建附加到运行中 VM 的磁盘的快照可能会降低性能，或者会影响 VM 中的操作系统或应用程序的可用性。 如果无法安排应用程序停机，建议使用来宾内代理来保护应用程序。 

### <a name="vms-in-a-scale-set-or-availability-set"></a>规模集或可用性集中的 VM

不应在 VM 级别备份规模集或可用性组中被视为临时资源的 VM，特别是在应用程序无状态的情况下。 对于在规模集或可用性集中部署的有状态应用程序，请考虑保护应用程序数据（例如，存储池中的数据库或卷）。 

### <a name="replicationmanual-failover"></a>复制/手动故障转移

对于要求数据丢失量最少或停机时间最短的应用程序，可以在来宾 OS 或应用程序级别启用数据复制，以便将数据复制到其他位置。 某些应用程序（例如 Microsoft SQL Server）本身就支持复制。 如果应用程序不支持复制，则可使用来宾 OS 中的软件来复制磁盘，或者使用来宾 OS 中作为代理安装的合作伙伴解决方案。

使用此方法时，会将应用程序部署在一个云中，将数据复制到本地的另一个云中或复制到 Azure。 触发了故障转移时，目标中的应用程序将需要启动并附加到复制的数据，然后才能开始处理请求。
 
### <a name="high-availabilityautomatic-failover"></a>高可用性/自动故障转移

对于只能容忍数秒或数分钟停机时间的无状态应用程序，请考虑使用高可用性配置。 根据设计，高可用性应用程序部署在主动/主动拓扑中的多个位置，其中的所有实例都可以处理请求。 对于本地硬件故障，Azure Stack Hub 基础结构使用两个架顶式交换机在物理网络中实现高可用性。 对于计算级别故障，Azure Stack Hub 会在一个缩放单元中使用多个节点并会自动对 VM 进行故障转移。 在 VM 级别，可以使用规模集，也可以使用可用性集中的 VM，以确保节点故障不会导致应用程序无法使用。 需要将同一应用程序采用同一配置部署到辅助位置。 若要使应用程序采用主动/主动配置，可以使用负载均衡器或 DNS 将请求定向到所有可用实例。

### <a name="no-recovery"></a>不恢复

环境中的某些应用可能不需要针对计划外停机或数据丢失进行保护。 例如，用于开发和测试的 VM 通常不需要进行恢复。 是否对某个应用程序或数据集进行保护由你自行决定。

## <a name="recommended-topologies"></a>建议的拓扑

Azure Stack Hub 部署的重要注意事项：

|     | 建议 | 注释 |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|**将 VM 备份/还原到已部署在数据中心的外部备份目标**| 建议 | 利用现有的备份基础结构和操作技能。 确保在设置备份基础结构的大小时，能够让它保护其他的 VM 实例。 确保备份基础结构不要紧靠源。 可以将 VM 还原到源 Azure Stack Hub、辅助 Azure Stack Hub 实例或 Azure。 |
|**将 VM 备份/还原到专用于 Azure Stack Hub 的外部备份目标**| 建议 | 可以为 Azure Stack Hub 购买新的备份基础结构或预配专用的备份基础结构。 确保备份基础结构不要紧靠源。 可以将 VM 还原到源 Azure Stack Hub、辅助 Azure Stack Hub 实例或 Azure。 |
|**将 VM 直接备份/还原到全球版 Azure 或受信任的服务提供商**| 建议 | 只要能够满足数据隐私和法规要求，就可以将备份存储到全球版 Azure 或受信任的服务提供商。 理想情况下，该服务提供商也会运行 Azure Stack Hub，因此你在还原时获得的操作体验是一致的。 |
|**将 VM 复制/故障转移到单独的 Azure Stack Hub 实例**| 建议 | 在进行故障转移时，需要有一个运行完全正常的辅助 Azure Stack Hub 云，这样就可以避免应用不可用的时间延长。 |
|**将 VM 直接复制/故障转移到 Azure 或受信任的服务提供商**| 建议 | 只要能够满足数据隐私和法规要求，就可以将数据复制到全球版 Azure 或受信任的服务提供商。 理想情况下，该服务提供商也会运行 Azure Stack Hub，因此你在故障转移后获得的操作体验是一致的。 |
|**将备份目标部署到也承载着由该备份目标保护的所有应用程序的 Azure Stack Hub 上。**| 独立目标：不建议 </br> 将备份数据复制到外部的目标：建议 | 如果选择将备份设备部署到 Azure Stack Hub 上（目的是对可操作还原进行优化），则必须确保将所有数据持续复制到外部备份位置。 |
|**将物理备份设备部署到安装了 Azure Stack Hub 解决方案的机架**| 不支持 | 目前，不能将任何其他设备连接到不属于原始解决方案的架顶式交换机。 |

## <a name="next-steps"></a>后续步骤

本文提供了用于保护 Azure Stack Hub 上部署的用户 VM 的一般准则。 有关使用 Azure 服务保护用户 VM 的信息，请参阅：

- [Azure Stack IaaS – 第四部分 – 保护你的内容](https://azure.microsoft.com/blog/azure-stack-iaas-part-four/)
- [业务连续性和灾难恢复的注意事项](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="azure-backup-server"></a>Azure 备份服务器
 - [使用 Azure 备份来备份 Azure Stack Hub 上的文件和应用](/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure Stack Hub 的 Azure 备份服务器支持](/azure/backup/ ) 
 
 ### <a name="azure-site-recovery"></a>Azure Site Recovery
 - [Azure Stack Hub 的 Azure Site Recovery 支持](/azure/site-recovery/)  
 
 ### <a name="partner-products"></a>合作伙伴产品
 - [Azure Stack Hub 数据中心集成合作伙伴生态系统数据表](https://aka.ms/azurestackbcdrpartners)
