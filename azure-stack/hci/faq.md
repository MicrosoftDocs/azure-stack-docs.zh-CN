---
title: Azure Stack HCI 常见问题
description: Azure Stack HCI 常见问题解答。
ms.topic: conceptual
author: JohnCobb1
ms.author: v-johcob
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 11/03/2020
ms.openlocfilehash: 67d81ee3bcec9ba1007296a6a50c028653b7d7b7
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328868"
---
# <a name="azure-stack-hci-faq"></a>Azure Stack HCI 常见问题
Azure Stack HCI 常见问题包含有关 Azure Stack HCI 连接的部分和一般常见问题解答部分。

## <a name="azure-stack-hci-connectivity"></a>Azure Stack HCI 连接
Azure Stack HCI 是作为 Azure 混合服务提供的本地超聚合基础结构堆栈。 将 Azure Stack HCI 软件安装在你在本地控制的物理服务器上。 然后，将连接到 Azure 以实现基于云的监视、支持、计费，以及可选的管理和安全功能。 本 FAQ 部分说明 Azure Stack HCI 如何通过解决有关连接要求和行为的常见问题来使用云。

### <a name="your-data-stays-on-premises"></a>你的数据保留在本地

**Azure Stack HCI 上存储的数据是否已发送到云？**

通常情况下，不能。 不会将本地虚拟机 (Vm) 的名称、元数据、配置和内容发送到云，除非你为此目的明确启用其他服务，如 Azure 备份或 Azure Site Recovery。 除非将这些 Vm 单独注册到云管理服务（如 Azure Arc）中。

### <a name="edge-local-management-and-control"></a>边缘-本地管理和控制

**Azure Stack HCI 的控制平面是否经过云中？**

通常，尽管它依赖于所使用的功能。 即使云的网络连接已关闭或受到严格限制，你也可以使用边缘本地工具（如 Windows 管理中心、PowerShell 或 System Center）直接管理主机基础结构和 Vm。 常见的日常操作（例如，在主机之间移动 VM、更换发生故障的驱动器或配置 IP 地址）不依赖于云。 但是，需要云连接才能获取无线软件更新、更改 Azure 注册，或使用直接依赖于云服务进行备份、监视等功能。

**Azure Stack HCI 与云之间是否存在带宽或延迟要求？**

通常，尽管它依赖于所使用的功能。 有限带宽连接（如乡村 T1 线路或卫星/蜂窝连接）足以满足 Azure Stack HCI 同步的需要。每日所需的最小连接数仅为几 kb。 其他服务可能需要额外的带宽，特别是复制或备份整个 Vm，下载大型软件更新，或上传详细日志以便在云中进行分析和监视。

### <a name="designed-for-intermittent-and-limited-connectivity"></a>为间歇连接和有限连接而设计

**Azure Stack HCI 是否需要持续连接到云？**

错误。 Azure Stack HCI 用于处理有限或零的连接时间段。

**如果到云的网络连接暂时中断，会发生什么情况？**

连接关闭时，所有主机基础结构和 Vm 都将继续正常运行，并且你可以使用用于管理的边缘本地工具。 你将无法使用直接依赖于云服务的功能。 直到 Azure Stack HCI 能够再次同步之前，Azure 门户中的信息也会过期。

**连接关闭后，Azure Stack HCI 运行的时间有多长？**

Azure Stack HCI 至少需要在每30天的时间内通过 Azure 成功同步。

**超过30天限制会发生什么情况？**

如果 Azure Stack HCI 连续30天内未与 Azure 同步，则群集的连接状态将显示在 Azure 门户和其他工具的 **策略之外** ，并且群集将进入缩减功能模式。 在此模式下，主机基础结构将保持运行状态，并且所有当前 Vm 都将继续正常运行。 但是，在 Azure Stack HCI 能够再次同步之前，无法创建新的 Vm。 内部技术原因是，群集的云生成的许可证已过期，需要通过与 Azure 同步来续订。

### <a name="understanding-sync"></a>了解同步

**与云 Azure Stack HCI 同步的内容有哪些？**

这取决于所使用的功能。 Azure Stack HCI 至少会同步基本群集信息以显示在 Azure 门户中 (如群集节点、硬件型号和软件版本) 的列表）;汇总自上次同步以来的累计内核天数的计费信息;最少的所需诊断信息可帮助 Microsoft 保持 Azure Stack HCI 安全、最新且工作正常。 总大小为小–几 kb。 如果启用其他服务，它们可能会上传更多：例如，Azure Log Analytics 会上传日志和性能计数器进行监视。

**Azure Stack HCI 与云同步的频率如何？**

这取决于所使用的功能。 Azure Stack HCI 至少会尝试每12小时同步一次。 如果同步不成功，则会在本地保留内容并在下一次成功同步时发送。除了此常规计时器，还可以使用 `Sync-AzureStackHCI` PowerShell cmdlet 或从 Windows 管理中心手动同步。 如果打开其他服务，则可能会更频繁地上传：例如，Azure Log Analytics 每5分钟上载一次进行监视。

### <a name="data-residency"></a>数据驻留

**已同步的信息实际上会在何处？**

Azure Stack HCI 与初始注册期间选择的 Azure 区域同步。 默认值为 "美国东部"。 Azure Stack HCI 还可在西欧中使用，我们正在努力扩展到更多的区域。 例如，如果您向美国东部注册，则您的信息只同步到该区域，并且仅在美国中，存储在安全的 Microsoft 运营数据中心内。 若要了解详细信息，请参阅 [Azure 中的数据驻留](https://azure.microsoft.com/global-infrastructure/data-residency/)。

### <a name="disconnected-or-air-gapped"></a>断开连接或 "有气流"

**能否使用 Azure Stack HCI 并从不连接到 Azure？**

错误。 Azure Stack HCI 需要连续每30天成功与 Azure 同步。

**能否在 "气流" Azure Stack HCI 和 Azure 之间脱机传输数据？**

错误。 目前没有任何机制可以在没有网络连接的情况下在本地与 Azure 之间注册和同步。 例如，不能使用可移动存储来传输证书或计费数据。 如果有足够的客户需求，我们将在将来浏览此类功能。 请在 [AZURE STACK HCI 反馈论坛](https://feedback.azure.com/forums/929833-azure-stack-hci)中告知我们。

## <a name="azure-stack-hci-general-faqs"></a>Azure Stack HCI 一般 Faq

**Azure Stack HCI 与 Windows Server 有何关系？**

Windows Server 是几乎每个 Azure 产品的基础，并且你需要的所有功能将继续在 Windows Server 中提供支持。 Azure Stack HCI 的初始产品/服务基于 Windows Server 2019，使用了传统的 Windows Server 许可模式。 目前，Azure Stack HCI 拥有自己的操作系统和基于订阅的许可模式。 Azure Stack HCI 是在本地部署 HCI 的建议方式，可以使用我们的合作伙伴提供的经过 Microsoft 验证的硬件。

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
