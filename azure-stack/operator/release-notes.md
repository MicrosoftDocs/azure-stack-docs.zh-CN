---
title: Azure Stack Hub 发行说明
description: Azure Stack Hub 集成系统的发行说明，包括更新和 bug 修复。
author: sethmanheim
ms.topic: article
ms.date: 11/11/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ms.openlocfilehash: 74b1be3736d21d968fa45135034637d4ca3cd5eb
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546049"
---
# <a name="azure-stack-hub-release-notes"></a>Azure Stack Hub 发行说明

本文介绍 Azure Stack Hub 更新包的内容。 此更新包括最新版 Azure Stack Hub 的改进和修复。

若要访问不同版本的发行说明，请使用左侧目录上方的版本选择器下拉列表。

::: moniker range=">=azs-2002"
> [!IMPORTANT]  
> 此更新包仅适用于 Azure Stack Hub 集成系统。 请勿将此更新程序包应用于 Azure Stack 开发工具包 (ASDK)。
::: moniker-end
::: moniker range="<azs-2002"
> [!IMPORTANT]  
> 如果 Azure Stack Hub 实例落后于两个以上的更新，则认为它不符合。 必须[至少更新到最低支持版本才能获得支持](azure-stack-servicing-policy.md#keep-your-system-under-support)。
::: moniker-end

## <a name="update-planning"></a>更新规划

应用更新之前，请务必查看以下信息：

- [已知问题](known-issues.md)
- [安全更新](release-notes-security-updates.md)
- [应用更新之前和之后的活动清单](release-notes-checklist.md)

有关对更新和更新过程进行故障排除的帮助，请参阅[对 Azure Stack Hub 的修补和更新问题进行故障排除](azure-stack-troubleshooting.md)。

## <a name="download-the-update"></a>下载更新

可使用 [Azure Stack Hub 更新下载程序工具](https://aka.ms/azurestackupdatedownload)下载 Azure Stack Hub 更新包。

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-2008"
## <a name="2008-build-reference"></a>2008生成引用

Azure Stack 中心2008更新内部版本号为 **1.2008.13.88** 。

### <a name="update-type"></a>更新类型

Azure Stack 中心2008更新生成类型已 **满** 。

与以前的更新相比，2008更新包的大小较大。 增加的大小会导致下载时间更长。 此更新将长时间保留为“正在准备”状态，操作员可预期此过程所需的时间长于以前的更新。 2008更新在我们的内部测试-4 节点中具有以下所需的运行时：13-20 小时、8个节点：16-26 小时、12个节点：19-32 小时、16个节点：22-38 个小时。 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件规格。 运行时间短于或长于预期值并不常见。因此，除非更新失败，否则不需要 Azure Stack Hub 操作员执行操作。 此运行时近似值特定于2008更新，不应与其他 Azure Stack 中心更新进行比较。

有关更新内部版本类型的详细信息，请参阅[在 Azure Stack Hub 中管理更新](azure-stack-updates.md)。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->
- Azure Stack 集线器现在支持 VNET 对等互连，它可以在没有网络虚拟设备 (NVA) 的情况下连接 Vnet。 有关详细信息，请参阅 [新的 VNET 对等互连文档](../user/virtual-network-peering.md)。
- Azure Stack 集线器 blob 存储现在允许用户使用不可变的 blob。 通过在容器上设置不可变的策略，你可以将业务关键数据对象存储在蠕虫 (写入一次，读取许多) 状态。 在此版本中，不可变的策略只能通过 REST API 或客户端 Sdk 来设置。 在此版本中，也不可能追加 blob 写入。 有关不可变 blob 的详细信息，请参阅 [将业务关键 blob 数据存储在不可变的存储](/azure/storage/blobs/storage-blob-immutable-storage)中。
- Azure Stack 中心存储现在支持 Azure 存储服务 Api 版本2019-07-07。 对于与新 REST API 版本兼容的 Azure 客户端库，请参阅 [Azure Stack 中心存储开发工具](../user/azure-stack-storage-dev.md#azure-client-libraries)。
- Azure Stack 集线器计算现在支持 Azure 计算 Api 版本2020-06-01，同时提供全部可用功能的子集。
- Windows 管理中心的预览，现在可以连接到 Azure Stack 集线器，在支持 (操作期间提供对基础结构的深入见解，) 需要中断玻璃。
- 可以在部署时将登录标语添加到特权终结点 (PEP) 。
- 发布了更 **互斥的操作** 横幅，这会提高系统上当前发生的操作的可见性，并禁止用户启动 (，并随后) 任何其他独占操作。
- 在每个 Azure Stack 中心市场项的产品页中引入了两个新横幅。 如果 Marketplace 下载失败，操作员可以查看错误详细信息，并尝试执行建议的步骤来解决此问题。
- 发布了一个分级工具供客户提供反馈。 这将允许 Azure Stack 中心衡量和优化客户体验。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->
- 实现了网络控制器和 SLB 主机代理的内部监视，因此，如果服务进入停止状态，则会自动修正。
- Active Directory 联合身份验证服务 (AD FS) 现在会检索新的令牌签名证书，叫客户已在其自己的 AD FS 服务器上轮换了该证书。 若要充分利用已配置系统的这一新功能，必须重新配置 AD FS 集成。 有关详细信息，请参阅 [将 AD FS 标识与 Azure Stack 中心数据中心集成](azure-stack-integrate-identity.md)。
- 对基础结构角色实例及其在缩放单元节点上的依赖项的启动和关闭过程的更改。 这会增加 Azure Stack 集线器启动和关闭的可靠性。
- 已更新 **test-azurestack** 验证工具的 **AzSScenarios** 套件，使云服务提供商能够在对所有客户帐户实施多重身份验证后成功运行此套件。
- 通过在生命周期操作期间为29个面向客户的警报添加抑制逻辑，提高了警报可靠性。

### <a name="changes"></a>更改

- SRP API 版本 **2016-01-01** 和 **2016-05-01** 中的 **supportHttpsTrafficOnly** 存储帐户资源类型属性已启用，但 Azure Stack 中心不支持此属性。
- 引发的卷容量利用率警报阈值为 80% (警告) 和 90% (关键) 到 90% (warning) 和 95% (严重) 。 有关详细信息，请参阅 [存储空间警报](azure-stack-manage-storage-shares.md#storage-space-alerts)
- 在此版本中，AD Graph 配置步骤会更改。 有关详细信息，请参阅 [将 AD FS 标识与 Azure Stack 中心数据中心集成](azure-stack-integrate-identity.md)。
- 为了符合为 Windows Server 2019 定义的最新最佳实践，Azure Stack 集线器正在进行更改，以利用附加的流量类或优先级进一步将服务器与服务器通信进行隔离，以支持故障转移群集控制通信。 这些更改的结果可为故障转移群集通信提供更好的恢复能力。 此流量类和带宽预留配置是通过在 Azure Stack 中心解决方案的 (ToR) 交换机上以及 Azure Stack 集线器的主机或服务器上进行更改来完成的。

  请注意，这些更改将添加到 Azure Stack 中心系统的主机级别。 请与 OEM 联系，以便在 (ToR) 网络交换机上进行所需的更改。 此 ToR 更改可在更新到2008版本之前执行，也可在更新到2008后执行。 有关详细信息，请参阅 [网络集成文档](azure-stack-network.md)。

  - 已在此版本中将 **(NVIDIA T4) NCas_v4** 的 GPU 支持的 vm 大小替换为 VM 大小 **NCasT4_v3** ，以便与 Azure 保持一致。 请注意，这些在门户中不可见，只能通过 Azure 资源管理器模板使用。

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- 修复了删除未附加到正在运行的 VM 的 NIC 的 NSG 的问题。
- 修复了修改关联到负载均衡器的公共 IP 的 **IdleTimeoutInMinutes** 值的问题，使公共 ip 进入 "失败" 状态。
- 修复了 **AzsDisk** cmdlet，以返回附加的托管磁盘的正确 **附加** 状态，而不是 **OnlineMigration** 。

## <a name="security-updates"></a>安全更新

有关此 Azure Stack Hub 更新中的安全更新的信息，请参阅 [Azure Stack Hub 安全更新](release-notes-security-updates.md)。

## <a name="hotfixes"></a>修补程序

Azure Stack Hub 定期发布修补程序。 从 2005 版开始，更新到新的主版本（如 1.2002.x 到1.2005.x）时，新的主版本中最新的修补程序（如果有）会自动安装。 在此之后，如果发布了适用于你的内部版本的修补程序，则应安装它。

> [!NOTE]
> Azure Stack Hub 修补程序版本是累积性的；你只需安装最新的修补程序即可获取该版本的任何以前修补程序版本中包含的所有修补程序。

有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

Azure Stack Hub 修补程序仅适用于 Azure Stack Hub 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="prerequisites-before-applying-the-2008-update"></a>先决条件：在应用2008更新之前

当你将更新到新的主版本 (例如，1.2005 到 1.2008) 时，如果新的主版本中的任何) 自动安装，则最新的修补程序会 (。 在此之后，如果发布了适用于你的内部版本的修补程序，则应安装它。

### <a name="after-successfully-applying-the-2008-update"></a>成功应用2008更新后

安装2008之后，如果以后发布了任何2008修补程序，则应该安装这些修补程序：

- 没有适用于2008的 Azure Stack 集线器修补程序。
::: moniker-end

::: moniker range="azs-2005"
## <a name="2005-build-reference"></a>2005 内部版本参考

Azure Stack Hub 2005 更新内部版本号为 1.2005.6.53。

### <a name="update-type"></a>更新类型

Azure Stack Hub 2005 更新内部版本类型为“完整”。

与以前的更新相比，2005 更新包更大。 增加的大小会导致下载时间更长。 此更新将长时间保留为“正在准备”状态，操作员可预期此过程所需的时间长于以前的更新。 在我们的内部测试中，2005 更新的预期运行时间如下 - 4 个节点：13-20 小时，8 个节点：16-26 小时，12 个节点：19-32 小时，16 个节点：22-38 小时。 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件规格。 运行时间短于或长于预期值并不常见。因此，除非更新失败，否则不需要 Azure Stack Hub 操作员执行操作。 此运行时近似值特定于 2005 更新，不应与其他 Azure Stack Hub 更新进行比较。

有关更新内部版本类型的详细信息，请参阅[在 Azure Stack Hub 中管理更新](azure-stack-updates.md)。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->
- 此内部版本支持 3 个新的 GPU VM 类型：NCv3 (Nvidia V100)、NVv4 (AMD MI25) 和 NCas_v4 (NVIDIA T4) VM 大小。 对于具有正确硬件并且已加入 Azure Stack Hub GPU 预览计划的用户，VM 部署将会成功。 如果你感兴趣，请在 https://aka.ms/azurestackhubgpupreview 注册 GPU 预览计划。 有关详细信息，[请参阅此处](../user/gpu-vms-about.md)。
- 此版本提供了一项支持自动修复的新功能，可检测故障、评估影响并安全地缓解系统问题。 借助此功能，我们可以努力提高系统的可用性，而无需手动干预。 对于版本 2005 及更高版本，客户遇到警报将会减少。 除非另行通知，否则此管道中的任何故障都不需要 Azure Stack Hub 操作员进行操作。
- Azure Stack Hub 管理门户中有一个新选项，气隙/断开连接的 Azure Stack Hub 客户可使用该选项在本地保存日志。 当 Azure Stack Hub 与 Azure 断开连接时，可以将日志存储在本地 SMB 共享中。
- 如果系统操作已在进行中，Azure Stack Hub 管理门户现在会阻止某些操作。 例如，如果正在进行更新，则不能添加新的缩放单元节点。
- 此版本在 1910 之前创建的 VM 中提供了与 Azure 更高的结构一致性。 在1910中，Microsoft 宣布所有新创建的 Vm 将使用 wireserver 协议，使客户能够使用与 Azure 相同的 WALA 代理和 Windows 来宾代理，从而更轻松地在 Azure Stack 集线器上使用 Azure 映像。 在此版本中，将自动迁移所有在 1910 前创建的 VM 以使用 wireserver 协议。 这也提供了更可靠的 VM 创建、VM 扩展部署以及稳定状态运行时间方面的改进。
- Azure Stack Hub 存储现在支持 Azure 存储服务 API 版本2019-02-02。 对于 Azure 客户端库，它与新的 REST API 版本兼容。 有关详细信息，请参阅 [Azure Stack Hub 存储开发工具](../user/azure-stack-storage-dev.md#azure-client-libraries)。
- Azure Stack Hub 现在支持最新版本的 [CreateUiDefinition（版本 2）](/azure/azure-resource-manager/managed-applications/create-uidefinition-overview)。
- 批处理 VM 部署的新指南。 有关详细信息，[请参阅此文](../operator/azure-stack-capacity-planning-compute.md)。
- Azure Stack Hub 市场 CoreOS 容器 Linux 项的[生命周期即将结束](https://azure.microsoft.com/updates/flatcar-in-azure/)。 有关详细信息，请参阅[从 CoreOS 容器 Linux 迁移](https://docs.flatcar-linux.org/os/migrate-from-container-linux/)。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- 对存储基础结构群集服务日志和事件的改进。 存储基础结构群集服务的日志和事件的保留时间最长为 14 天，以便更好地进行诊断和故障排除。
- 提高启动和停止 Azure Stack Hub 的可靠性的改进。
- 使用分散化和删除依赖项减少更新运行时的改进。 与 2002 更新相比，4 个节点标记更新时间从 15-42 小时缩短到 13-20 小时。 8 个节点标记更新时间从 20-50 小时缩短到 16-26 小时。 12 个节点标记更新时间从 20-60 小时缩短到 19-32 小时。 16 个节点标记更新时间从 25-70 小时缩短到 22-38 小时。 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件规格。
- 现在，如果发生某些无法恢复的错误，更新会提前失败。
- 提高了从 Internet 下载时更新包的复原能力。
- 提高了对 VM 停止解除分配的复原能力。
- 提高了网络控制器主机代理的复原能力。
- 向 syslog 消息的 CEF 有效负载添加了其他字段，以报告用于连接到特权终结点和恢复终结点的源 IP 和帐户。 有关详细信息，请参阅[通过 Syslog 转发将 Azure Stack Hub 与监视解决方案集成](azure-stack-integrate-security.md)。
- 向通过 syslog 客户端发出的事件列表添加了 Windows Defender 事件（事件 ID 5001、5010、5012）。
- 在 Azure Stack 管理员门户中为 Windows Defender 相关事件添加了警报，以报告 Defender 平台和签名版本不一致以及无法对检测到的恶意软件采取操作。
- 添加了将 Azure Stack Hub 集成到数据中心时对 4 个边界设备的支持。

### <a name="changes"></a>更改

- 从管理员门户中删除了停止、关闭和重启基础结构角色实例的操作。 还在 Fabric 资源提供程序中删除了相应的 API。 Azure Stack Hub 的管理员 RM 模块和 AZ 预览版中的以下 PowerShell cmdlet 不再有效：Stop-AzsInfrastructureRoleInstance、Disable-InfrastructureRoleInstance 和 Restart-InfrastructureRoleInstance  。 将从 Azure Stack Hub 的下一个管理员 AZ 模块版本中删除这些 cmdlet。
- Azure Stack Hub 2005 现在仅支持 [Azure Stack Hub 2020（版本 87.x）上的应用服务](app-service-release-notes-2020-Q2.md)。
- 硬件监视所需的用户加密设置已从 DES 更改为 AES，以提高安全性。 请联系你的硬件合作伙伴，以了解如何在基本板管理控制器 (BMC) 中更改设置。 在 BMC 中进行更改后，可能需要使用特权终结点再次运行命令 **BmcCredential** 。 有关详细信息，请参阅 [在 Azure Stack 中心内轮换机密](azure-stack-rotate-secrets.md)

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 修复了以下问题：修复缩放单元节点可能因找不到基础 OS 映像的路径而失败。
- 修复了对修复缩放单元节点具有级联影响的支持基础结构角色的横向缩减和横向扩展的问题。
- 修复了以下问题：当操作员通过“所有服务”>“计算”>“VM 映像”>“添加”，将自己的映像添加到 Azure Stack Hub 管理员门户时，不允许使用 .VHD 扩展（而不是 .vhd）。
- 修复了以下问题：在执行任何其他 VM 更新操作（添加磁盘、标记等）后，以前的 VM 重启操作随后出现意外重启。
- 修复了创建重复 DNS 区域导致门户停止响应的问题。 它现在应显示相应的错误。
- 修复了以下问题：Get-AzureStackLogs 不收集所需日志来解决网络问题。 
- 修复了以下问题：门户允许附加的 NIC 比它实际允许的 NIC 少。 
- 修复了代码完整性策略，以免为某些内部软件发出违规事件。 这可减少通过 syslog 客户端发出的代码完整性违规事件产生的干扰。
- 修复了 Set-TLSPolicy cmdlet 以强制实施新策略，而无需重启 https 服务或重启主机。
- 修复了以下问题：使用 Linux NTP 服务器错误地在管理门户中生成警报。  
- 修复了以下问题：备份控制器服务实例的故障转移导致禁用自动备份。
- 修复了以下问题：在基础结构服务未建立 Internet 连接的情况下，内部机密轮替失败。
- 修复了用户无法使用 Azure Stack Hub 门户查看订阅权限的问题。

## <a name="security-updates"></a>安全更新

有关此 Azure Stack Hub 更新中的安全更新的信息，请参阅 [Azure Stack Hub 安全更新](release-notes-security-updates.md)。

## <a name="hotfixes"></a>修补程序

Azure Stack Hub 定期发布修补程序。 从 2005 版开始，更新到新的主版本（如 1.2002.x 到1.2005.x）时，新的主版本中最新的修补程序（如果有）会自动安装。 在此之后，如果发布了适用于你的内部版本的修补程序，则应安装它。

> [!NOTE]
> Azure Stack Hub 修补程序版本是累积性的；你只需安装最新的修补程序即可获取该版本的任何以前修补程序版本中包含的所有修补程序。

有关详细信息，请参阅我们的[服务策略](azure-stack-servicing-policy.md)。

Azure Stack Hub 修补程序仅适用于 Azure Stack Hub 集成系统；请勿尝试在 ASDK 上安装修补程序。

### <a name="prerequisites-before-applying-the-2005-update"></a>先决条件：应用 2005 更新之前

必须在包含以下修补程序的版本 2002 中应用 Azure Stack Hub 版本 2005：

- [Azure Stack 中心修补程序1.2002.61.163](https://support.microsoft.com/help/4592241)

### <a name="after-successfully-applying-the-2005-update"></a>成功应用 2005 更新之后

从 2005 版开始，更新到新的主版本（如 1.2002.x 到1.2005.x）时，新的主版本中最新的修补程序（如果有）会自动安装。

安装 2005 之后，如果以后发布了任何 2005 修补程序，应安装这些修补程序：

- [Azure Stack 中心修补程序1.2005.20.82](https://support.microsoft.com/help/4592228)
::: moniker-end

::: moniker range="azs-2002"
## <a name="2002-build-reference"></a>2002 内部版本参考

Azure Stack Hub 2002 更新内部版本号为 **1.2002.0.35** 。

> [!IMPORTANT]  
> 在 Azure Stack 中心2002更新中，Microsoft 暂时扩展了我们的 [Azure Stack 中心支持策略声明](azure-stack-servicing-policy.md)。  我们正在与世界各地应对 COVID-19 的客户合作，这些客户可能正在就其 Azure Stack Hub 系统以及如何更新和管理这些系统做出重要决策，以确保其数据中心业务操作继续正常运行。 为支持我们的客户，Microsoft 提供了临时支持策略更改扩展，以包括三个以前的更新版本。  因此，将支持新发布的 2002 更新以及以前的三个更新版本（例如 1910、1908 和 1907）之一。

### <a name="update-type"></a>更新类型

Azure Stack Hub 2002 更新内部版本类型为“完整”。

与以前的更新相比，2002 更新程序包更大。 增加的大小会导致下载时间更长。 此更新将长时间保留为“正在准备”状态，操作员可预期此过程所需的时间长于以前的更新。 在我们的内部测试中，2002 更新的预期运行时间如下 - 4 个节点：15-42 小时，8 个节点：20-50 小时，12 个节点：20-60 小时，16 个节点：25-70 小时。 确切的更新运行时间通常取决于租户工作负荷在系统上使用的容量、系统网络连接（如果已连接到 Internet），以及系统的硬件规格。 运行时间短于或长于预期值并不常见。因此，除非更新失败，否则不需要 Azure Stack Hub 操作员执行操作。 此运行时近似值特定于 2002 更新，不应与其他 Azure Stack Hub 更新进行比较。

有关更新内部版本类型的详细信息，请参阅[在 Azure Stack Hub 中管理更新](azure-stack-updates.md)。

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>新增功能

<!-- What's new, also net new experiences and features. -->

- 提供了基于 AzureRM 的 Azure Stack Hub 管理员 PowerShell 模块的一个新版本 (1.8.1)。
- 已推出新版 Azure Stack Hub 管理员 REST API。 可在 [API 参考](/rest/api/azure-stack/)中找到有关终结点和中断性变更的详细信息。
- 将在 2020 年 4 月 15 日为 Azure Stack Hub 发布新的 Azure PowerShell 租户模块。 当前使用的 Azure RM 模块会继续工作，但在内部版本 2002 后将不再更新。
- 在 Azure Stack Hub 管理员门户上添加了新的警告警报，用于报告所配置的 syslog 服务器的连接问题。 警报标题是 **Syslog 客户端发送 Syslog 消息时遇到网络问题** 。
- 在 Azure Stack Hub 管理员门户上添加了新的警告警报，用于报告网络时间协议 (NTP) 服务器的连接问题。 警报标题是 **[node name] 上的时间来源无效** 。
- 由于 2002 中与 TLS 限制相关的一个中断性变更，[Java SDK](https://azure.microsoft.com/develop/java/) 发布了新的程序包。 你必须安装新的 Java SDK 依赖项。 可以在 [Java 和 API 版本配置文件](../user/azure-stack-version-profiles-java.md?view=azs-2002#java-and-api-version-profiles)中找到相关说明。
- 新版 (1.0.5.10) System Center Operations Manager - Azure Stack Hub MP 已发布，该版本是运行 2002 的所有系统所必需的，因为存在中断性 API 变更。 API 变更会影响备份和存储性能仪表板，建议你先将所有系统更新为 2002，然后再更新 MP。

### <a name="improvements"></a>改进

<!-- Changes and product improvements with tangible customer-facing value. -->

- 此更新包含对更新过程的更改，这些更改会显著提高将来的完整更新的性能。 这些更改将随 2002 发行版之后的下一次完整更新生效，具体目标是提高完整更新期间对主机操作系统进行更新的阶段的性能。 提高主机操作系统更新的性能会大大减小租户工作负荷在完整更新过程中受影响的时间窗口。
- Azure Stack Hub 就绪性检查器工具现在使用分配给 AD Graph 的所有 TCP IP 端口来验证 AD Graph 集成。
- 脱机联合工具已更新，改进了可靠性。 该工具在 GitHub 上不再可用，已[移到 PowerShell 库](https://www.powershellgallery.com/packages/Azs.Syndication.Admin/)中。 有关详细信息，请参阅[将市场项下载到 Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)。
- 将引入一项新的监视功能。 针对物理主机和基础结构 VM 的磁盘空间不足警报将由平台自动修正。仅当此操作失败时，该警报才会显示在 Azure Stack Hub 管理员门户中，供操作员执行操作。
- 对[诊断日志收集](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002)的改进。 新的体验优化和简化了诊断日志收集，它不需要预先配置 blob 存储帐户。 存储环境已预先配置，因此你可以在创建支持案例之前发送日志，并减少支持人员通话时间。
- [主动日志收集和按需日志收集](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002)所花费的时间已缩短 80%。 日志收集时间可能会比此预期值长，但不需要 Azure Stack Hub 操作员执行操作，除非日志收集失败。
- 启动更新后，“更新”边栏选项卡中会显示 Azure Stack Hub 更新程序包的下载进度。 这仅适用于那些选择[通过自动下载功能准备更新程序包](azure-stack-update-prepare-package.md#automatic-download-and-preparation-for-update-packages)且已连接的 Azure Stack Hub 系统。
- 改进了网络控制器主机代理的可靠性。
- 引入了一个名为 DNS Orchestrator 的新微服务，它改进了在修补和更新期间内部 DNS 服务的复原逻辑。
- 添加了一个新的请求验证，用于在创建 VM 时使启动诊断存储帐户参数的无效 blob URI 失败。
- 为 Rdagent 和主机代理（主机上用于方便执行 VM CRUD 操作的两个服务）添加了自动修正和日志记录改进。
- 向市场管理添加了一项新功能，使 Microsoft 能够添加属性，目的是防止管理员下载由于存在各种属性（例如 Azure Stack 版本或计费模型）而与其 Azure Stack 不兼容的市场产品。 只有 Microsoft 才能添加这些属性。 有关详细信息，请参阅[使用门户下载市场项](azure-stack-download-azure-marketplace-item.md#use-the-portal-to-download-marketplace-items)。

### <a name="changes"></a>更改

- 管理员门户现在会指示操作是否正在进行，并在 Azure Stack 区域旁显示一个图标。 当你将鼠标悬停在该图标上时，它会显示操作的名称。 这使你可以识别正在运行的系统后台操作；例如，可能会运行几个小时的备份作业或存储扩展。

- 以下管理员 API 已弃用：

  | 资源提供程序       | 资源              | 版本            |
  |-------------------------|-----------------------|--------------------|
  | Microsoft.Storage.Admin | 农场                 | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/acquisitions    | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/shares          | 2015-12-01-preview |
  | Microsoft.Storage.Admin | farms/storageaccounts | 2015-12-01-preview |

- 以下管理员 API 已替换为较新版本 (2018-09-01)：

  | 资源提供程序      | 资源              | 版本    |
  |------------------------|-----------------------|------------|
  | Microsoft.Backup.Admin | backupLocation         | 2016-05-01 |
  | Microsoft.Backup.Admin | backups                | 2016-05-01 |
  | Microsoft.Backup.Admin | 操作             | 2016-05-01 |

- 使用 PowerShell 创建 Windows VM 时，如果希望 VM 部署扩展，请确保添加 `provisionvmagent` 标志。 如果没有此标志，则会创建不带来宾代理的 VM，这将移除部署 VM 扩展的功能：

   ```powershell
   $VirtualMachine = Set-AzureRmVMOperatingSystem `
     -VM $VirtualMachine `
     -Windows `
     -ComputerName "MainComputer" `
     -Credential $Credential -ProvisionVMAgent
  ```

### <a name="fixes"></a>修复项

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- 修复了在虚拟机的同一 NIC 上添加多个公共 IP 会导致 Internet 连接问题的问题。 现在，具有两个公共 IP 的 NIC 能够按预期方式工作。
- 修复了一个导致系统引发警报的问题，该警报指出需要配置 Azure AD 主目录。
- 修复了一个导致警报无法自动关闭的问题。 该警报指出必须配置 Azure AD 主目录，但是，即使问题得到缓解，该警报也不关闭。
- 修复了一个因更新资源提供程序存在内部故障而导致更新在更新准备阶段失败的问题。
- 修复了一个在执行 Azure Stack Hub 机密轮换后导致附加产品资源提供程序操作失败的问题。
- 修复了一个问题，该问题是由于 ERCS 角色的内存压力导致 Azure Stack Hub 更新失败的常见原因。
- 修复了“更新”边栏选项卡中的一个 bug：在 Azure Stack Hub 更新的准备阶段，更新状态显示为“正在安装”而不是“正在准备”。
- 修复了虚拟交换机上的 RSC 功能导致不一致并丢弃流经负载均衡器的流量的问题。 现在，RSC 功能默认处于禁用状态。
- 修复了问题：NIC 上的多个 IP 配置导致流量在出站连接中被错误路由和阻止。 
- 修复了在 NIC 的 MAC 地址被缓存的情况下将该地址分配给另一资源导致 VM 部署失败的问题。
- 修复了来自零售渠道的 Windows VM 映像无法通过 AVMA 激活其许可证的问题。
- 修复了当 VM 所请求的虚拟核心数等于节点的物理核心数时无法创建 VM 的问题。 我们现在允许 VM 的虚拟核心数等于或少于节点的物理核心数。
- 修复了不允许将许可证类型设置为“null”以将即用即付映像切换到 BYOL 的问题。
- 修复了一个问题，现在允许向 VM 规模集添加扩展。

## <a name="security-updates"></a>安全更新

有关此 Azure Stack Hub 更新中的安全更新的信息，请参阅 [Azure Stack Hub 安全更新](release-notes-security-updates.md)。

## <a name="hotfixes"></a>修补程序

Azure Stack Hub 定期发布修补程序。 将 Azure Stack Hub 更新到 2002 之前，请务必先安装 1910 的最新 Azure Stack Hub 修补程序。

> [!NOTE]
> Azure Stack Hub 修补程序版本是累积性的；你只需安装最新的修补程序即可获取该版本的任何以前修补程序版本中包含的所有修补程序。

Azure Stack Hub 修补程序仅适用于 Azure Stack Hub 集成系统；请勿尝试在 ASDK 上安装修补程序。

有关修补程序的详细信息，请参阅 [Azure Stack Hub 服务策略](azure-stack-servicing-policy.md#hotfixes)。

### <a name="prerequisites-before-applying-the-2002-update"></a>先决条件：应用 2002 更新之前

必须在包含以下修补程序的版本 1910 中应用 Azure Stack Hub 版本 2002：

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 中心修补程序1.1910.84.230](https://support.microsoft.com/help/4592243)

### <a name="after-successfully-applying-the-2002-update"></a>成功应用 2002 更新之后

安装此更新之后，请安装所有适用的修补程序。

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack 中心修补程序1.2002.61.163](https://support.microsoft.com/help/4592241)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1910"
## <a name="1910-archived-release-notes"></a>1910存档的发行说明
::: moniker-end
::: moniker range="azs-1908"
## <a name="1908-archived-release-notes"></a>1908存档的发行说明
::: moniker-end
::: moniker range="azs-1907"
## <a name="1907-archived-release-notes"></a>1907 已存档的发行说明
::: moniker-end
::: moniker range="azs-1906"
## <a name="1906-archived-release-notes"></a>1906 已存档的发行说明
::: moniker-end
::: moniker range="azs-1905"
## <a name="1905-archived-release-notes"></a>1905 已存档的发行说明
::: moniker-end
::: moniker range="azs-1904"
## <a name="1904-archived-release-notes"></a>1904 已存档的发行说明
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-release-notes"></a>1903 已存档的发行说明
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-release-notes"></a>1902 已存档的发行说明
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-release-notes"></a>1901 已存档的发行说明
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-release-notes"></a>1811 已存档的发行说明
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-release-notes"></a>1809 已存档的发行说明
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-release-notes"></a>1808 已存档的发行说明
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-release-notes"></a>1807 已存档的发行说明
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-release-notes"></a>1805 已存档的发行说明
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-release-notes"></a>1804 已存档的发行说明
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-release-notes"></a>1803 已存档的发行说明
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-release-notes"></a>1802 已存档的发行说明
::: moniker-end

::: moniker range="<azs-2002"
可以访问 [TechNet 库中旧版本 Azure Stack Hub 的发行说明](https://aka.ms/azsarchivedrelnotes)。 提供这些已存档文档仅供参考，并不意味着支持这些版本。 有关 Azure Stack Hub 支持的信息，请参阅 [Azure Stack Hub 服务策略](azure-stack-servicing-policy.md)。 如需进一步的帮助，请联系 Microsoft 客户支持服务。
::: moniker-end
