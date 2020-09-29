---
title: Azure Stack Hub 中的诊断日志收集
description: 了解 Azure Stack Hub 的“帮助 + 支持”中的诊断日志收集。
author: justinha
ms.topic: article
ms.date: 08/24/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 08/24/2020
ms.openlocfilehash: 841c031b6009cdb7970194a3268010e745e9e0f0
ms.sourcegitcommit: 4922a14fdbc8a3b67df065336e8a21a42f224867
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2020
ms.locfileid: "88764505"
---
# <a name="diagnostic-log-collection-in-azure-stack-hub"></a>Azure Stack Hub 中的诊断日志收集


Azure Stack Hub 是一个大型集合，包含可以彼此交互的 Windows 组件和本地 Azure 服务。 所有这些组件和服务都会生成自己的日志集。 为了使 Microsoft 支持部门能够有效地诊断问题，我们提供了诊断日志收集的无缝体验。

**帮助和支持**中的诊断日志收集帮助   操作员在无需 PowerShell 的简单用户界面中快速收集和共享包含 Microsoft 支持部门的诊断日志。 即使其他基础结构服务关闭，也可以收集日志。  

使用“帮助和支持”中的诊断日志收集功能，操作员可以快速地通过 Microsoft 客户支持服务 (CSS) 收集和共享诊断日志，该服务是一个易用的用户界面，不需要 PowerShell。 即使其他基础结构服务关闭，也可以收集日志。  

::: moniker range=">= azs-2002"

建议使用此日志收集方法，并且仅当管理员门户或“帮助和支持”边栏选项卡不可用时才[使用特权终结点 (PEP)](azure-stack-get-azurestacklog.md)。 

>[!NOTE]
>必须注册 Azure Stack Hub 才能使用诊断日志收集。 如果未注册 Azure Stack Hub，请使用 [Get-AzureStackLog](azure-stack-get-azurestacklog.md) 来共享日志。 

![Azure Stack Hub 中的诊断日志收集选项](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

## <a name="collection-options-and-data-handling"></a>收集选项和数据处理

::: moniker-end
::: moniker range=">= azs-2005"

根据与 Azure 的连接情况，Azure Stack Hub 提供了将诊断日志收集、保存和发送到 CSS 的适当方式。 如果 Azure Stack Hub 可以连接到 Azure，推荐的方法是启用“主动日志收集”，这会在引发关键警报时自动将诊断日志上传到 Azure 中由 Microsoft 控制的存储 blob。 还可以通过使用“立即发送日志”来按需收集日志，或者，如果 Azure Stack Hub 与 Azure 断开连接，可以在本地保存日志。 

以下部分说明了每个选项以及如何在各种情况下处理数据。 

::: moniker-end

::: moniker range="= azs-2002"
诊断日志收集功能提供两个用于发送日志的选项。 以下部分说明了每个选项以及如何在各种情况下处理数据。 
::: moniker-end

::: moniker range=">= azs-2002"

## <a name="send-logs-proactively"></a>主动发送日志

[主动收集日志](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002)可优化和简化诊断日志收集过程，让客户可以在提交支持案例之前将日志发送给 Microsoft。 可以从 Azure Stack Hub 主动上传要分析的诊断日志。 仅当出现 [系统运行状况警报](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002#proactive-diagnostic-log-collection-alerts) 时才收集这些日志，并且仅在支持案例的上下文中 Microsoft 支持部门才会对其进行访问。


### <a name="how-the-data-is-handled"></a>数据处理方式

仅根据 Azure Stack 集线器系统运行状况警报，由 Microsoft 同意定期自动收集日志。 你还可以在 Microsoft 管理和控制的 Azure 存储帐户中确认并同意这些日志的上传和保留。

这些数据只用于排查系统运行状况警报问题，在未经你同意的情况下不会用于市场营销、广告或任何其他商业目的。 数据最多可保留90天，Microsoft 收集的任何数据都将按照我们的 [标准隐私做法](https://privacy.microsoft.com/)进行处理。

如果你不再同意，则以前在你同意的情况下收集的任何数据都不会受到影响。

使用 **前瞻性日志** 收集收集的日志将上传到由 Microsoft 管理和控制的 Azure 存储帐户。 Microsoft 可能会在支持案例的上下文中访问这些日志，并改善 Azure Stack 集线器的运行状况。

## <a name="send-logs-now"></a>立即发送日志

[立即发送日志](./azure-stack-configure-on-demand-diagnostic-log-collection-portal.md?view=azs-2002)是一个手动选项。如果选择该选项，那么仅当你以客户身份启动收集操作时（通常在提交支持案例之前），才会将诊断日志从 Azure Stack Hub 上传。

Azure Stack 操作员可以使用管理员门户或 PowerShell 按需将诊断日志发送到 Microsoft 支持部门。 如果 Azure Stack 集线器已连接到 Azure，则建议使用 [管理员门户中的 "发送日志](./azure-stack-configure-on-demand-diagnostic-log-collection-portal.md?view=azs-2002) "，因为这是直接将日志发送到 Microsoft 的最简单方法。 如果门户不可用，则操作员应改为[使用 PowerShell 立即发送日志](./azure-stack-configure-on-demand-diagnostic-log-collection-powershell.md?view=azs-2002)。

::: moniker-end
::: moniker range="= azs-2002"
如果已断开与 Internet 的连接，或者只想在本地保存日志，请使用 [Get-AzureStackLog](azure-stack-get-azurestacklog.md) 方法发送日志。 以下流程图显示了各种情况下用于发送诊断日志的选项。 
::: moniker-end

::: moniker range=">= azs-2002"

![流程图，显示如何将日志立即发送到 Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

### <a name="how-the-data-is-handled"></a>数据处理方式

通过从 Azure Stack 中心发起诊断日志收集，你确认并同意上载这些日志，并将其保留在 Microsoft 管理并控制的 Azure 存储帐户中。 Microsoft 支持部门可以通过支持案例立即访问这些日志，而无需与客户进行日志收集。

::: moniker-end

::: moniker range=">= azs-2005"

## <a name="save-logs-locally"></a>在本地保存日志

当 Azure Stack Hub 与 Azure 断开连接时，可以将日志保存到本地 SMB 共享。 在“设置”边栏选项卡中，输入具有共享写入权限的路径、用户名和密码。 在支持案例中，Microsoft 支持部门将提供有关如何传输这些本地日志的详细步骤。 如果管理员门户不可用，则可以使用 [get-azurestacklog](azure-stack-get-azurestacklog.md) 在本地保存日志。

![诊断日志收集选项的屏幕截图](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end

::: moniker range=">= azs-2002"

## <a name="bandwidth-considerations"></a>带宽注意事项

进行诊断日志收集时日志的平均大小各不相同，具体取决于它是主动运行还是手动运行。 “主动收集日志”选项对应的日志平均大小约为 2 GB。 “立即发送日志”选项对应的收集日志的大小取决于需要收集多少小时。

下表列出了在以受限或计量方式连接到 Azure 时的环境注意事项。

| 网络连接 | 影响 |
|----|---|
| 低带宽/高延迟连接 | 完成日志上传的时间会延长。 |
| 共享连接 | 上传也可能影响共享网络连接的其他应用/用户。 |
| 计量连接 | ISP 可能会针对你额外使用网络的情况收取额外费用。 |

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="collecting-logs-from-multiple-azure-stack-hub-systems"></a>从多个 Azure Stack Hub 系统收集日志

为每个需要从其收集日志的 Azure Stack Hub 缩放单元设置一个 Blob 容器。 若要详细了解如何配置 Blob 容器，请参阅[配置 Azure Stack Hub 诊断日志自动收集](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002)。 最佳做法是仅将同一 Azure Stack Hub 缩放单元中的诊断日志保存到单个 Blob 容器中。

## <a name="retention-policy"></a>保留策略

创建 Azure Blob 存储[生命周期管理规则](/azure/storage/blobs/storage-lifecycle-management-concepts)，管理日志保留策略。 建议将诊断日志保留 30 天。 若要在 Azure 存储中创建生命周期管理规则，请登录到 Azure 门户，选择“存储帐户”再选择 Blob 容器，然后在“Blob 服务”下选择“生命周期管理”  。

![Azure 门户中的生命周期管理](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)

## <a name="sas-token-expiration"></a>SAS 令牌过期

将 SAS URL 到期时间设置为两年。 如果续订存储帐户密钥，请确保重新生成 SAS URL。 应按最佳做法管理 SAS 令牌。 有关详细信息，请参阅[使用 SAS 时的最佳做法](/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas)。

## <a name="bandwidth-consumption"></a>带宽消耗

进行诊断日志收集时日志的平均大小各不相同，具体取决于日志收集是按需的还是自动的。

对于按需日志收集，进行日志收集时日志的大小取决于需要收集多少小时。 可以从过去七天选择 1-4 小时滑动窗口。

启用诊断日志自动收集时，服务会对是否存在严重警报进行监视。 在严重警报出现并持续大约 30 分钟后，服务会收集并上传相应的日志。 该日志收集的日志大小平均约为 2 GB。 如果修补升级失败，则仅当严重警报出现并持续约 30 分钟时，才会启动日志自动收集。 建议按照[监视修补升级指南](azure-stack-updates.md)操作。 警报监视、日志收集和上传对用户透明。

在正常运行的系统中，根本不会收集到日志。 在运行不正常的系统中，可能会每天运行两到三次日志收集，但通常只运行一次。 在最糟糕的情况下，每天可能运行多达 10 次。  

用户可以根据下表考虑在以受限或计量方式连接到 Azure 时启用日志自动收集对环境的影响。

| 网络连接 | 影响 |
|---|---|
| 低带宽/高延迟连接 | 完成日志上传的时间会延长。 | 
| 共享连接 | 上传也可能影响共享网络连接的其他应用/用户。 |
| 计量连接 | ISP 可能会针对你额外使用网络的情况收取额外费用。 |

## <a name="managing-costs"></a>管理成本

Azure [Blob 存储费用](https://azure.microsoft.com/pricing/details/storage/blobs/)取决于每月保存的数据量以及其他因素，例如数据冗余。 如果没有现有的存储帐户，可以登录到 Azure 门户，选择“存储帐户”，然后按步骤[创建 Azure Blob 容器 SAS URL](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002)。

最佳做法是创建 Azure Blob 存储[生命周期管理策略](/azure/storage/blobs/storage-lifecycle-management-concepts)，尽量降低持续产生的存储成本。 若要详细了解如何设置存储帐户，请参阅[配置 Azure Stack Hub 诊断日志自动收集](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002)

::: moniker-end

## <a name="see-also"></a>另请参阅

[Azure Stack Hub 日志和客户数据处理](./azure-stack-data-collection.md)
