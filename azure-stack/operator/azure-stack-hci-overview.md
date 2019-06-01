---
title: Azure Stack HCI 概述
description: Azure Stack HCI 是使用超聚合 Windows Server 2019 群集验证硬件来运行本地虚拟化工作负荷。 您可以选择性地连接到 Azure 服务的基于云的备份、 站点恢复和的详细信息。 Azure Stack HCI 解决方案使用 Microsoft 验证的硬件来确保最佳性能和可靠性，并包含技术如 NVMe 驱动器、 永久性内存和远程直接内存访问 (RDMA) 网络的支持。
ms.technology: storage
ms.topic: article
author: jasongerend
ms.author: jgerend
ms.localizationpriority: medium
ms.prod: windows-server-threshold
ms.date: 05/31/2019
ms.openlocfilehash: c3f25f38429b58d8d72037ff4ed9758d9993a2c5
ms.sourcegitcommit: 07cc716d97bf484c7260eb165ae205ae25e09589
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2019
ms.locfileid: "66453534"
---
# <a name="azure-stack-hci-overview"></a>Azure Stack HCI 概述

>适用对象：Windows Server 2019

Azure Stack HCI 是使用超聚合 Windows Server 2019 群集验证硬件来运行本地虚拟化工作负荷。 您可以选择性地连接到 Azure 服务的基于云的备份、 站点恢复和的详细信息。 Azure Stack HCI 解决方案使用 Microsoft 验证的硬件来确保最佳性能和可靠性，并包含技术如 NVMe 驱动器、 永久性内存和远程直接内存访问 (RDMA) 网络的支持。

Azure Stack HCI 是结合了多个产品的解决方案：

- 从 OEM 合作伙伴的硬件

- Windows Server 2019 Datacenter Edition

- Windows Admin Center

- Azure 服务 （可选）

![Azure Stack HCI 是 Microsoft 的超聚合解决方案，可从各种硬件合作伙伴。](media/azure-stack-hci/azure-stack-hci-solution.png)

Azure Stack HCI 是 Microsoft 的超聚合解决方案，可从各种硬件合作伙伴。 请考虑以下针对超聚合解决方案，可帮助您确定 Azure Stack HCI 是否最适合你需求的解决方案的应用场景：

- **刷新老化硬件。** 替换较旧的服务器和存储基础结构和运行 Windows 和 Linux 虚拟机的本地和边缘与现有 IT 技术和工具。

- **合并虚拟化工作负荷。** 合并的高效、 超聚合基础结构上的旧应用。 点击到相同类型的用来运行 Microsoft Azure 等超大规模数据中心的云提高效率。

- **连接到 Azure 以构建混合云服务。** 简化云管理和安全服务在 Azure 中，包括非现场备份、 site recovery、 基于云的监视，和的详细信息的访问权限。

## <a name="the-azure-stack-family"></a>Azure Stack 系列

Azure Stack HCI 是 Azure 的一部分，Azure Stack 系列中，使用相同的软件定义的计算、 存储和网络软件作为 Azure Stack。 下面是不同的解决方案的快速摘要：

- [Azure](https://azure.microsoft.com) -使用公共云服务
- [Azure Stack](azure-stack-overview.md) -运行云服务的本地
- [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) -运行虚拟化应用程序在本地，具有可选连接到 Azure

![Azure 和 Azure Stack 运行云服务，而 Azure Stack HCI 运行虚拟化本地应用程序](media/azure-stack-hci/azure-family.png)

|Azure：使用公有云服务|Azure Stack:云服务在本地操作|Azure Stack HCI:虚拟化应用程序在本地运行|
|-----------------|-----------------|-----------------|
|有关按需、 自助服务计算资源来迁移和运行现有应用现代化，并生成新的云本机应用。|生成和运行的云应用程序在边缘，当断开连接，或以满足监管要求，使用一致的 Azure 服务在本地。| 运行虚拟化应用程序在本地、 替换和合并老化服务器基础结构，并连接到 Azure 云服务。|
|在全球的 54 区域中可用的 100 多个服务。|Azure Vm 的 Windows 和 Linux，Azure Web 应用和函数、 Azure 密钥保管库、 Azure 资源管理器、 Azure Marketplace、 容器、 Azure IoT 和事件中心，管理工具 （计划、 产品/服务，RBAC）|验证 HCI 解决方案用于管理和集成的访问 Azure 服务由 HYPER-V 和存储空间直通使用 Windows Server 2019 和 Windows Admin Center 提供支持。|

若要了解详细信息，请访问以下链接：

- 了解更多信息，请访问我们[Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) solutions 网站。
- 观看 Microsoft 的专家 Jeff Woolsey 和 Vijay Tewari[讨论新的 Azure Stack HCI 解决方案](https://aka.ms/AzureStackOverviewVideo)。

## <a name="hyperconverged-efficiencies"></a>超聚合效率

Azure Stack HCI 解决方案带来了高度虚拟化的计算、 存储和网络上行业标准 x86 服务器和组件。 将组合在同一群集中的资源，使你更轻松地部署、 管理和缩放。 使用所选的命令行自动化或 Windows Admin Center 进行管理。

实现对 NVMe，永久性内存的内置支持服务器应用程序使用的 HYPER-V、 Microsoft 云的基础虚拟机监控程序技术和存储空间直通技术的行业领先虚拟机性能和远程直接内存访问 (RDMA) 网络。

帮助保护应用和数据安全与防护的虚拟机、 网络 microsegmentation 和本机加密。

## <a name="hybrid-capabilities"></a>混合功能

您可以充分利用云在本地和公有云中的超聚合基础结构平台一起使用。 你的团队可以开始构建与 Azure 基础结构管理服务的内置集成的云技能：

- Azure 站点恢复以实现高可用性和灾难恢复即服务 (DRaaS)。

- Azure 监视器集中式的集线器，以便跟踪跨应用程序、 网络和基础结构 – 与发生高级分析由 AI 提供支持。

- 云见证，以使用 Azure 作为轻型领带断路器用于群集仲裁。

- Azure 备份为场外数据保护，以及保护免受勒索软件威胁。

- Azure 更新管理的更新评估和更新在 Azure 中，在本地运行的 Windows 虚拟机的部署。

- 若要通过点到站点 VPN 在 Azure 中你的 vm 连接本地资源的 azure 网络适配器。

- 同步文件服务器与使用 Azure 文件同步在云中。

有关详细信息，请参阅[连接到 Azure 的混合服务的 Windows Server](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index)。

## <a name="management-tools-and-system-center"></a>管理工具和管理 System Center

Azure Stack HCI 作为 Azure Stack 中使用的相同虚拟化和软件定义的存储和网络软件。 但是，与 Azure Stack HCI 在群集上具有完全管理权限并可以直接管理任何其技术：

- [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
- [存储空间直通](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [软件定义的网络](https://docs.microsoft.com/windows-server/networking/sdn/)
- [故障转移群集](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)

若要管理这些技术，可以使用以下管理工具：

- [Windows Admin Center](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview)
- [System Center](https://www.microsoft.com/cloud-platform/system-center)
- [PowerShell](https://docs.microsoft.com/powershell/?view=powershell-6)
- 其他管理工具如[服务器管理器](https://docs.microsoft.com/windows-server/administration/server-manager/server-manager)，和 MMC 管理单元
- 非 Microsoft 工具，例如 5Nine 管理器

如果您选择使用 System Center 来部署和管理基础结构，将使用 System Center Virtual Machine Management (VMM) 和 System Center Operations Manager。 使用 VMM，可以预配和管理进行创建和部署到私有云的虚拟机和服务所需的资源。 借助 Operations Manager，以确定立即采取措施的问题在企业范围内监视服务、 设备和操作。

## <a name="hardware-partners"></a>硬件合作伙伴

你可以购买 15 合作伙伴提供的运行 Windows Server 2019 的已验证的 Azure Stack HCI 解决方案。 您首选的 Microsoft 合作伙伴可帮助你启动并运行没有耗时较长的设计和构建时间的情况下。 它们还提供了一个单一实现和支持服务联系点。

请访问[Azure Stack HCI 网站](https://azure.microsoft.com/overview/azure-stack/hci)若要查看我们 70 多个 Azure Stack HCI 解决方案当前可从这些 Microsoft 合作伙伴：ASUS、 Axellio、 蓝筹股、 DataON、 Dell EMC、 Fujitsu、 HPE、 Hitachi、 华为、 Lenovo、 NEC、 primeLine 解决方案、 QCT、 SecureGUARD 和 Supermicro。

## <a name="faq"></a>常见问题解答

### <a name="what-do-azure-stack-and-azure-stack-hci-solutions-have-in-common"></a>Azure Stack 和 Azure Stack HCI 解决方案必须什么共同点？

Azure Stack HCI 解决方案具有同一基于 HYPER-V 软件定义的计算、 存储和网络技术功能作为 Azure Stack。 这两个产品/服务符合严格的测试和验证条件，以确保可靠性和与基础硬件平台的兼容性。

### <a name="how-are-they-different"></a>那么其不同之处在哪里？

使用 Azure Stack 中，您运行云服务在本地。 你可以运行 Azure IaaS 和 PaaS 服务在本地以一致地生成并运行云应用程序任意位置，使用 Azure 门户的本地管理。

通过 Azure Stack HCI，在运行虚拟化工作负荷的本地托管 Windows Admin Center 和熟悉的 Windows Server 工具。 （可选） 可以用于混合方案，例如基于云的站点恢复、 监视和其他人连接到 Azure。

### <a name="why-is-microsoft-bringing-its-hci-offering-to-the-azure-stack-family"></a>Microsoft 将向 Azure Stack 系列提供其 HCI 的原因？

Microsoft 的超聚合技术已是 Azure Stack 的基础。

许多 Microsoft 客户具有复杂的 IT 环境和我们的目标是提供解决方案，以满足他们身在何处使用合适的技术为正确的业务需要。 Azure Stack HCI 是一种演变，以前与硬件合作伙伴提供的基于 Windows Server 2016 的 Windows Server Software-Defined (WSSD) 解决方案。 我们将其引入 Azure Stack 系列因为我们开始提供用于与 Azure 无缝连接基础结构管理服务的新选项。

### <a name="does-azure-stack-hci-need-to-be-connected-to-azure"></a>Azure Stack HCI 是否需要连接到 Azure？

否，它是可选的。 您可以充分利用与 Azure 集成以进行混合方案，例如非现场备份和灾难恢复和基于云的监视和更新管理，但它们可选。 它是从 Internet 运行已断开连接没有问题。

### <a name="how-does-azure-stack-hci-relate-to-windows-server"></a>Azure Stack HCI 到 Windows Server 有何关联？

Windows Server 2019 是几乎每个 Azure 产品的基础。 您值继续提供并支持在 Windows Server 中的所有功能。 Azure Stack HCI 是 HCI 的本地部署的建议的方法使用从我们的合作伙伴的 Microsoft 验证硬件。

### <a name="can-i-upgrade-from-azure-stack-hci-to-azure-stack"></a>可以从升级 Azure Stack HCI 到 Azure Stack？ 

不可以，但客户可以迁移其工作负荷从 Azure Stack HCI 到 Azure Stack 或 Azure。

### <a name="what-azure-services-can-i-connect-to-azure-stack-hci"></a>可以连接到 Azure Stack HCI 哪些 Azure 服务？

有关更新的列表的 Azure 服务可以连接 Azure Stack HCI 以，请参阅[连接到 Azure 的混合服务的 Windows Server](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index)。

### <a name="how-does-the-cost-of-azure-stack-hci-compare-to-azure-stack"></a>Azure Stack HCI 的成本与 Azure Stack 相比如何？ 

Azure Stack 作为包括服务和支持的完全集成系统出售。 你可以购买 Azure Stack 为你管理的系统或作为完全托管的服务在我们的合作伙伴。 除了基本的系统中，在 Azure Stack 或 Azure 运行的 Azure 服务在为你的使用付费的基础上出售。

Azure Stack HCI 解决方案遵循传统的购买模型。 你可以从 Azure Stack HCI 合作伙伴和现有通道的软件 (Windows Server 2019 Datacenter edition 软件定义的数据中心功能以及 Windows Admin Center) 购买经过验证的硬件。 对于可以使用 Windows Admin Center 使用的 Azure 服务，您支付 Azure 订阅。

### <a name="how-do-i-buy-azure-stack-hci-solutions"></a>如何购买 Azure Stack HCI 解决方案？

执行以下步骤:

1. 从你的首选的硬件合作伙伴购买的 Microsoft 验证的硬件系统。
1. 安装 Windows Server 2019 Datacenter edition 和 Windows Admin Center 的管理以及连接到 Azure 云服务的功能
1. 选择使用你的 Azure 帐户附加到你的工作负荷的基于云的管理和安全服务。

![若要购买 Azure Stack HCI 解决方案，选择的硬件合作伙伴和最适合你需求的配置。](media/azure-stack-hci/buying-azure-stack-hci.png)

## <a name="compare-azure-stack-and-azure-stack-hci"></a>比较 Azure Stack 和 Azure Stack HCI

你的组织进行数字转换，您可能会发现通过使用公共云服务在新式体系结构上构建，并刷新旧版应用您也可以更快地移动。 但是，出于包括技术和法规障碍的原因，许多工作负荷必须保留在本地。 下表可帮助您确定哪些 Microsoft 混合云策略提供所需的所需位置，无论他们身在何处提供适用于工作负荷的云创新的内容。

|Azure Stack|Azure Stack HCI|
|--------|-------|
|新技能，创新进程|相同的技能，熟悉的过程|
|你的数据中心中的 azure 服务|你的数据中心连接到 Azure 服务|

### <a name="when-to-use-azure-stack"></a>何时使用 Azure Stack

|Azure Stack|Azure Stack HCI|
|--------|-------|
|使用 Azure Stack 为自助服务基础结构作为-服务 (IaaS)，使用具有强隔离和精确的使用情况跟踪和共置的多个租户的退款。 适用于服务提供商和企业私有云。 Azure Marketplace 中的模板。|Azure Stack HCI 本机不会强制执行或提供多租户。|
|使用 Azure Stack 开发和运行的应用程序依赖于平台的作为-服务 (PaaS) 服务等 Web 应用、 函数或事件中心的本地。 完全像在 Azure 中，从而提供一致的混合开发和运行时环境，这些服务在 Azure Stack 上运行。|Azure Stack HCI 不会在本地运行的 PaaS 服务。
|使用 Azure Stack 实现现代化，应用程序部署和操作与 DevOps 实践等基础结构即代码、 持续集成和持续部署 (CI/CD) 和方便的功能，如 Azure 一致的 VM 扩展。 适用于开发和 DevOps 团队。|Azure Stack HCI 本机不包括任何 DevOps 工具。

### <a name="when-to-use-azure-stack-hci"></a>何时使用 Azure Stack HCI

|Azure Stack|Azure Stack HCI|
|---------------|---------------|
|Azure Stack 需要最少 4 个节点和其自身的网络交换机。|使用 Azure Stack HCI 远程办公室和分支的最小占用量。 只需 2 个服务器节点和峰值简单性和经济性的无交换机连续网络开始。 在 4 个驱动器，64 GB 内存，也在 10 万美元的硬件产品/服务开始 / 节点。
|Azure Stack 约束 Hyper V 可配置性和功能集与 Azure 保持一致。|使用 Azure Stack HCI 经典的企业应用，如 Exchange、 SharePoint 和 SQL Server 和 Windows Server 角色，如文件服务器、 DNS、 DHCP、 IIS 和 AD 虚拟化的基本的 HYPER-V 虚拟化。 不受限制的访问所有的 HYPER-V 功能，如受防护的 Vm。|
|Azure Stack 不公开这些基础结构技术。|使用 Azure Stack HCI 使用软件定义基础结构来代替老化的存储阵列或网络设备，而无需主要体系结构重建。 内置的 HYPER-V、 存储空间直通和软件定义网络 (SDN) 是直接访问和管理。|