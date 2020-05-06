---
title: Azure Stack 集线器中的诊断日志集合
description: 了解 Azure Stack 中心帮助 + 支持中的诊断日志收集。
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: 2fcc31625fc0f508532c5c6a524f26e9cef31760
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836030"
---
# <a name="diagnostic-log-collection-in-azure-stack-hub"></a>Azure Stack 集线器中的诊断日志集合

::: moniker range=">= azs-2002"

Azure Stack 中心是 Windows 组件和本地 Azure 服务彼此交互的大集合。 所有这些组件和服务都生成各自的日志集。 为了使 Microsoft 客户支持服务（CSS）能够有效地诊断问题，我们为诊断日志收集提供了无缝体验。

**帮助和支持** 中的诊断日志收集帮助操作员在无需 PowerShell 的简单用户界面中快速收集和共享包含 CSS 的诊断日志。 即使其他基础结构服务出现故障，也会收集日志。  

如果管理员门户或 "**帮助 + 支持**" 边栏选项卡不可用，则建议使用这种日志收集方法，只[使用特权终结点（PEP）](azure-stack-get-azurestacklog.md) 。

>[!NOTE]
>必须注册 Azure Stack 集线器，并使 internet 连接能够使用诊断日志收集。 如果未注册 Azure Stack 集线器，则[使用特权终结点（PEP）](azure-stack-get-azurestacklog.md)共享日志。

![Azure Stack 集线器中的诊断日志收集选项](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

## <a name="collection-options-and-data-handling"></a>集合选项和数据处理

诊断日志收集功能提供了两个用于发送日志的选项。 下表说明每个选项以及如何在每种情况下处理数据。

### <a name="send-logs-proactively"></a>主动发送日志

[主动日志收集](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)可简化和简化诊断日志收集，因此客户可以在打开支持案例之前将日志发送给 Microsoft。 诊断日志是从 Azure Stack 中心主动上传以便进行分析。 仅当引发[系统运行状况警报](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md#proactive-diagnostic-log-collection-alerts)时才收集这些日志，并且仅在支持案例的上下文中由 CSS 访问。

#### <a name="how-the-data-is-handled"></a>如何处理数据

仅根据 Azure Stack 集线器系统运行状况警报，由 Microsoft 同意定期自动收集日志。 你还可以在 Microsoft 管理和控制的 Azure 存储帐户中确认并同意这些日志的上传和保留。

数据仅用于排除系统运行状况警报，不会在未经你同意的情况下用于市场营销、广告或任何其他商业用途。 数据最多可保留90天，Microsoft 收集的任何数据都将按照我们的[标准隐私做法](https://privacy.microsoft.com/)进行处理。

以前使用您的同意收集的任何数据都不会受到您的权限的吊销的影响。

使用**前瞻性日志**收集收集的日志将上传到由 Microsoft 管理和控制的 Azure 存储帐户。 Microsoft 可能会在支持案例的上下文中访问这些日志，并改善 Azure Stack 集线器的运行状况。

### <a name="send-logs-now"></a>立即发送日志

[发送日志现在](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md)是一个手动选项，仅当你（作为客户）启动集合时，通常在打开支持案例之前，仅当你（客户）启动集合时，才从 Azure Stack 中心上载诊断日志。

Azure Stack 操作员可以使用管理员门户或 PowerShell 将诊断日志按需发送到 Microsoft 客户支持服务（CSS）。 如果 Azure Stack 集线器已连接到 Azure，则建议使用[管理员门户中的 "发送日志](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md)"，因为这是直接将日志发送到 Microsoft 的最简单方法。 如果该门户不可用，则操作员应改为[使用 PowerShell 立即发送日志](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md)。

如果你从 internet 断开连接或只想要在本地保存日志，请使用[get-azurestacklog](azure-stack-get-azurestacklog.md)方法发送日志。 以下流程图显示了在每种情况下用于发送诊断日志的选项。

![流程图显示了如何立即将日志发送给 Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

#### <a name="how-the-data-is-handled"></a>如何处理数据

通过从 Azure Stack 中心发起诊断日志收集，你确认并同意上载这些日志，并将其保留在 Microsoft 管理并控制的 Azure 存储帐户中。 Microsoft CSS 可以立即通过支持案例访问这些日志，而无需与客户进行日志收集。

数据将仅用于排除系统运行状况警报的故障，不会用于市场营销、广告或任何其他商业目的。 数据最多可保留90天，Microsoft 收集的任何数据都将按照我们的[标准隐私做法](https://privacy.microsoft.com/)进行处理。

使用**发送日志**收集的日志现在会上传到由 Microsoft 管理和控制的存储。 Microsoft 在支持案例的上下文中对这些日志进行访问，并改善 Azure Stack 集线器的运行状况。

## <a name="bandwidth-considerations"></a>带宽注意事项

诊断日志集合的平均大小取决于它是主动运行还是手动运行。 **主动日志收集**的平均大小约为 2 GB。 **发送日志**的集合大小现在取决于收集的小时数。

下表列出了在以受限或计量方式连接到 Azure 时的环境注意事项。

| 网络连接 | 影响 |
|----|---|
| 低带宽/高延迟连接 | 日志上传需要较长的时间才能完成。 |
| 共享连接 | 上传还可能会影响共享网络连接的其他应用/用户。 |
| 计量连接 | 您的 ISP 可能会额外收取额外的网络使用量。 |

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="collecting-logs-from-multiple-azure-stack-hub-systems"></a>从多个 Azure Stack 集线器系统收集日志

为要从中收集日志的每个 Azure Stack 集线器缩放单位设置一个 blob 容器。 有关如何配置 blob 容器的详细信息，请参阅[配置自动 Azure Stack 集线器诊断日志收集](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)。 最佳做法是，只保存单个 blob 容器内相同 Azure Stack 集线器缩放单位的诊断日志。

## <a name="retention-policy"></a>保留策略

创建 Azure Blob 存储[生命周期管理规则](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts)，管理日志保留策略。 建议将诊断日志保留 30 天。 若要在 Azure 存储中创建生命周期管理规则，请登录到 Azure 门户，选择 "**存储帐户**"，选择 "blob 容器"，然后在 " **blob 服务**" 下选择 "**生命周期管理**"。

![Azure 门户中的生命周期管理](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)

## <a name="sas-token-expiration"></a>SAS 令牌过期

将 SAS URL 到期时间设置为两年。 如果续订存储帐户密钥，请确保重新生成 SAS URL。 应按最佳做法管理 SAS 令牌。 有关详细信息，请参阅[使用 SAS 时的最佳做法](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas)。

## <a name="bandwidth-consumption"></a>带宽消耗

进行诊断日志收集时日志的平均大小各不相同，具体取决于日志收集是按需的还是自动的。

对于按需日志收集，进行日志收集时日志的大小取决于需要收集多少小时。 可以从过去七天选择 1-4 小时滑动窗口。

启用诊断日志自动收集时，服务会对是否存在严重警报进行监视。 在严重警报出现并持续大约 30 分钟后，服务会收集并上传相应的日志。 该日志收集的日志大小平均约为 2 GB。 如果出现修补程序和更新失败的情况，自动日志收集将仅在出现严重警报时开始，并持续大约30分钟。 建议遵循[有关监视修补程序和更新的指南](azure-stack-updates.md)。 警报监视、日志收集和上传对用户透明。

在运行状况良好的系统中，根本不会收集日志。 在运行不正常的系统中，可能会每天运行两到三次日志收集，但通常只运行一次。 最糟糕的情况是，在最坏的情况下，可能会在一天内最多运行10次。  

用户可以根据下表考虑在以受限或计量方式连接到 Azure 时启用日志自动收集对环境的影响。

| 网络连接 | 影响 |
|---|---|
| 低带宽/高延迟连接 | 日志上传需要较长的时间才能完成。 | 
| 共享连接 | 上传还可能会影响共享网络连接的其他应用/用户。 |
| 计量连接 | 您的 ISP 可能会额外收取额外的网络使用量。 |

## <a name="managing-costs"></a>管理成本

Azure [blob 存储费用](https://azure.microsoft.com/pricing/details/storage/blobs/)取决于每月保存的数据量以及数据冗余等其他因素。 如果没有现有的存储帐户，可以登录到 Azure 门户，选择 "**存储帐户**"，然后按照步骤[创建 AZURE BLOB 容器 SAS URL](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)。

最佳做法是创建 Azure Blob 存储[生命周期管理策略](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts)，尽量降低持续产生的存储成本。 有关如何设置存储帐户的详细信息，请参阅[配置自动 Azure Stack 集线器诊断日志收集](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)

::: moniker-end

## <a name="see-also"></a>请参阅

[Azure Stack 中心日志和客户数据处理](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)
