---
title: Azure Stack HCI 常见问题解答
description: Azure Stack HCI 常见问题解答。
ms.topic: conceptual
author: JohnCobb1
ms.author: v-johcob
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 1/21/2021
ms.openlocfilehash: 6c84bad0de72235c6ccde1b8c23016afeb956cba
ms.sourcegitcommit: 7ee28fad5b8ba628b1a7dc3d82cabfc36aa62f0d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2021
ms.locfileid: "102250625"
---
# <a name="azure-stack-hci-faq"></a>Azure Stack HCI 常见问题解答
Azure Stack HCI 常见问题解答包含一个有关 Azure Stack HCI 连接的部分和一个常规常见问题解答部分。

## <a name="azure-stack-hci-connectivity"></a>Azure Stack HCI 连接
Azure Stack HCI 是一个作为 Azure 混合服务提供的本地超融合基础设施堆栈。 可在本地控制的物理服务器上安装 Azure Stack HCI 软件。 然后连接到 Azure 以实现基于云的监视、支持、计费，并使用可选的管理和安全功能。 本常见问题解答部分通过解决有关连接要求和行为的常见问题，说明 Azure Stack HCI 如何使用云。

### <a name="your-data-stays-on-premises"></a>你的数据保留在本地

**我在 Azure Stack HCI 上存储的数据是否会发送到云？**

不是。 客户数据（包括本地虚拟机的名称、元数据、配置和内容） (Vm) 永远不会发送到云，除非你以明确的方式为此目的（如 Azure 备份或 Azure Site Recovery）或将这些 Vm 分别注册到云管理服务（如 Azure Arc）中。

若要详细了解我们收集的诊断数据以保持 Azure Stack HCI 安全、最新且按预期方式工作，请参阅[在 Azure 中](https://azure.microsoft.com/global-infrastructure/data-residency/) [Azure Stack HCI 数据收集](concepts/data-collection.md)和数据驻留。

### <a name="edge-local-management-and-control"></a>边缘-本地管理和控制

**Azure Stack HCI 的控制平面是否通过云？**

不是。 即使与云的网络连接已关闭或严重受限，你也可使用边缘-本地工具（如 Windows Admin Center、PowerShell 或 System Center）直接管理主机基础结构和 VM。 常见的日常操作（例如，在主机之间移动 VM、更换发生故障的驱动器或配置 IP 地址）不依赖于云。 但需要连接云才能获取无线软件更新、更改 Azure 注册，或使用直接依赖于云服务的功能进行备份和监视等。

**Azure Stack HCI 与云之间是否存在带宽或延迟要求？**

不是。 有限带宽连接（如乡村 T1 线路或卫星/蜂窝连接）足以完成 Azure Stack HCI 同步。每日所需的最小连接量仅为几千字节。 额外的服务可能需要额外的带宽，特别是复制或备份整个 VM、下载大型软件更新或上传详细日志以在云中进行分析和监视。

### <a name="designed-for-intermittent-and-limited-connectivity"></a>专为间歇性连接和受限连接设计

**Azure Stack HCI 是否需要持续连接到云？**

不是。 Azure Stack HCI 旨在处理连接受限或没有连接的情况。

**如果与云的网络连接暂时断开，会发生什么情况？**

连接断开时，所有主机基础结构和 VM 都会继续正常运行，并且你可使用边缘-本地工具进行管理。 你将无法使用直接依赖于云服务的功能。 在 Azure Stack HCI 能够再次同步之前，Azure 门户中的信息也会过期。

**连接断开后，Azure Stack HCI 可以运行多长时间？**

每连续 30 天，Azure Stack HCI 至少需要与 Azure 成功同步一次。

**如果超出 30 天的限制，会发生什么情况？**

如果 Azure Stack HCI 连续30天内未与 Azure 同步，则群集的连接状态将显示在 Azure 门户和其他工具的 **策略之外** ，并且群集将进入缩减功能模式。 在此模式下，主机基础结构保持正常运行，并且所有当前 VM 都继续正常运行。 但是，在 Azure Stack HCI 能够再次同步之前，无法创建新的 Vm。 内部技术原因是，群集的云生成的许可证已过期，需要通过与 Azure 同步来续订。

### <a name="understanding-sync"></a>了解同步

**Azure Stack HCI 与云同步哪些内容？**

这取决于所使用的功能。 Azure Stack HCI 至少会同步基本群集信息以显示在 Azure 门户中 (如群集节点、硬件型号和软件版本) 的列表）;汇总自上次同步以来的累计内核天数的计费信息;最少的所需诊断信息可帮助 Microsoft 保持 Azure Stack HCI 安全、最新且工作正常。 总大小很小 - 几千字节。 如果启用额外的服务，它们可能会上传更多内容：例如，Azure Log Analytics 会上传日志和性能计数器以用于监视。

**Azure Stack HCI 与云同步的频率如何？**

这取决于所使用的功能。 Azure Stack HCI 至少会每 12 小时尝试一次同步。 如果同步不成功，则会在本地保留内容并在下一次成功同步时发送。除了此常规计时器，还可以使用 `Sync-AzureStackHCI` PowerShell cmdlet 或从 Windows 管理中心手动同步。 如果启用额外的服务，它们可能会更加频繁地上传内容：例如，Azure Log Analytics 会每 5 分钟上传一次以便进行监视。

### <a name="data-residency"></a>数据驻留

**已同步的信息实际上会在何处？**

Azure Stack HCI 与 Azure 同步，并将数据存储在安全的、由 Microsoft 运营的数据中心。 若要详细了解我们收集的诊断数据以保持 Azure Stack HCI 安全、最新且按预期方式工作，请参阅[在 Azure 中](https://azure.microsoft.com/global-infrastructure/data-residency/) [Azure Stack HCI 数据收集](concepts/data-collection.md)和数据驻留。

### <a name="disconnected-or-air-gapped"></a>断开连接或 "有气流"

**能否使用 Azure Stack HCI 而永不连接到 Azure？**

不是。 每连续 30 天，Azure Stack HCI 需要与 Azure 成功同步一次。

**能否在“气隙”Azure Stack HCI 和 Azure 之间脱机传输数据？**

不是。 目前没有任何机制可以在不连接网络的情况下在本地与 Azure 之间注册和同步。 例如，不能使用可移动存储来传输证书或计费数据。 如果客户需求足够多，我们愿意在将来探索此类功能。 请在 [Azure Stack HCI 反馈论坛](https://feedback.azure.com/forums/929833-azure-stack-hci)中告诉我们。

## <a name="azure-stack-hci-general-faqs"></a>Azure Stack HCI 常规常见问题解答

**Azure Stack HCI 与 Windows Server 有何关系？**

Windows Server 是几乎所有 Azure 产品的基础，并且你重视的所有功能都将继续在 Windows Server 的支持下发布。 Azure Stack HCI 的初始产品/服务基于 Windows Server 2019，使用了传统的 Windows Server 许可模式。 目前，Azure Stack HCI 拥有自己的操作系统和基于订阅的许可模式。 Azure Stack HCI 是在本地部署 HCI 的建议方式，可以使用我们的合作伙伴提供的经过 Microsoft 验证的硬件。

**是否可以从 Windows Server 2019 升级到 Azure Stack HCI？**

目前没有从 Windows Server 到 Azure Stack HCI 的就地升级。 请继续关注适用于运行基于 Windows Server 2019 和 2016 的超融合群集的客户的特定迁移指南。

**哪些 Azure 服务可连接到 Azure Stack HCI？**

有关可与 Azure Stack HCI 连接的 Azure 服务的更新列表，请参阅[将 Windows Server 连接到 Azure 混合服务](/windows-server/manage/windows-admin-center/azure/index)。

**Azure Stack Hub 和 Azure Stack HCI 解决方案有哪些共同之处？**

Azure Stack HCI 与 Azure Stack Hub 同样有基于 Hyper-V 的软件定义计算、存储和网络技术功能。 这两个产品都符合严格的测试和验证准则，目的是确保与基础硬件平台配合时的可靠性和兼容性。

**那么其不同之处在哪里？**

使用 Azure Stack Hub 可在本地运行云服务。 可在本地运行 Azure IaaS 和 PaaS 服务，无论位于任何位置，都能以一致的方式生成并运行云应用，并可以在本地使用 Azure 门户进行管理。

使用 Azure Stack HCI 可在本地运行虚拟化工作负荷，并使用 Windows 管理中心和你熟悉的 Windows Server 工具进行管理。 还可以连接到 Azure 以实现混合方案，如基于云的站点恢复、监视等。

**是否可以从 Azure Stack HCI 升级到 Azure Stack Hub？**

不能，但客户可将工作负荷从 Azure Stack HCI 迁移到 Azure Stack Hub 或 Azure。

**如何实现识别 Azure Stack HCI 服务器？**

Windows Admin Center 在“所有连接”列表和其他不同位置列出操作系统，或者你也可以使用以下 PowerShell 命令查询操作系统名称和版本。

```PowerShell
Get-ComputerInfo -Property 'osName', 'osDisplayVersion'
```

下面是一些示例输出：

```
OsName                    OSDisplayVersion
------                    ----------------
Microsoft Azure Stack HCI 20H2
```
