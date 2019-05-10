---
title: 向 Azure 报告 Azure Stack 用量数据 | Microsoft Docs
description: 了解如何在 Azure Stack 中设置用量数据报告。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: c744a686be2a00418f48b769a5971997a603693f
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172652"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>向 Azure 报告 Azure Stack 用量数据

用量数据也称为消耗数据，表示使用的资源量。

使用基于消耗的计费模式的 Azure Stack 多节点系统应向 Azure 报告用量数据，以便计费。 Azure Stack 操作员应将其 Azure Stack 实例配置为向 Azure 报告用量数据。

> [!IMPORTANT]
> 所有工作负荷都[必须部署在租户订阅](#are-users-charged-for-the-infrastructure-vms)下，才符合 Azure Stack 的许可条款。

使用情况数据报告是必需的许可证作为您的使用付费模式下的 Azure Stack 多节点用户。 对于在容量模型下许可证的客户而言是可选的 (请参阅[如何购买](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)页)。 对于 Azure Stack 开发工具包用户，Azure Stack 操作员可以报告用量数据并测试此功能。 但是，用户无需为产生的任何用量付费。

![计费流](media/azure-stack-usage-reporting/billing-flow.png)

用量数据通过 Azure Bridge 从 Azure Stack 发送到 Azure。 在 Azure 中，商务系统会处理用量数据并生成帐单。 生成帐单之后，Azure 订阅所有者可以从 [Azure 帐户中心](https://account.windowsazure.com/subscriptions)查看和下载帐单。 若要了解 Azure Stack 的许可方式，请参阅 [Azure Stack 打包和定价文档](https://go.microsoft.com/fwlink/?LinkId=842847)。

## <a name="set-up-usage-data-reporting"></a>设置用量数据报告

若要设置用量数据报告，必须[将 Azure Stack 实例注册到 Azure](azure-stack-registration.md)。 注册过程的一部分，将配置 Azure Stack，以便将 Azure Stack 连接到 Azure 并发送使用情况数据，Azure Bridge 组件。 会将以下用量数据从 Azure Stack 发送到 Azure：

- **计量 ID** - 消耗的资源的唯一 ID。
- **数量** - 资源用量。
- **位置** - 当前 Azure Stack 资源的部署位置。
- **资源 URI** - 正在报告其用量的资源的完全限定 URI。
- **订阅 ID** -Azure Stack 用户的订阅 ID（本地（Azure Stack）订阅）。
- **时间** - 用量数据的开始与结束时间。 在 Azure Stack 中使用这些资源的时间与向商务系统报告用量数据的时间存在一定的延迟。 Azure Stack 每隔 24 小时，将聚合使用情况数据，并向 Azure 中的商务管道报告使用情况数据需要另一个几个小时。 因此，午夜之前短暂发生的使用情况可以在 Azure 中显示下一天。

## <a name="generate-usage-data-reporting"></a>生成用量数据报告

- 若要测试用量数据报告，请在 Azure Stack 中创建一些资源。 例如，可以创建[存储帐户](azure-stack-provision-storage-account.md)， [Windows Server VM](../user/azure-stack-create-vm-template.md)，并使用基本和标准 Sku，以查看核心用量的报告的 Linux VM。 不同类型的资源的用量数据通过不同的计量器报告。

- 让资源运行几个小时。 系统大约每隔一小时收集一次用量信息。 收集之后，此数据将传输到 Azure 并在 Azure 商务系统中处理。 此过程最长可能需要花费几个小时。

## <a name="view-usage---csp-subscriptions"></a>查看用量 - CSP 订阅

如果已使用 CSP 订阅注册 Azure Stack，则可以查看用量和费用，查看方法与查看 Azure 消耗量相同。 Azure Stack 用量将包含在你的发票和对帐文件，可通过[合作伙伴中心](https://partnercenter.microsoft.com/partner/home)。 对帐文件每月更新。 如果需要访问最新的 Azure Stack 用量信息，可以使用合作伙伴中心 API。

![合作伙伴中心](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage---enterprise-agreement-subscriptions"></a>查看用量 - 企业协议订阅

如果已使用企业协议订阅注册 Azure Stack，则可以在 [EA 门户](https://ea.azure.com/)中查看用量和费用。 Azure Stack 用量包含在高级下载内容中，Azure 用量包含在此门户的报告部分下。

## <a name="view-usage---other-subscriptions"></a>查看用量 - 其他订阅

如果已注册 Azure Stack 使用任何其他订阅类型;有关示例，即用即付订阅，可以在 Azure 帐户中心查看用量和费用。 登录到[Azure 帐户中心](https://account.windowsazure.com/subscriptions)以 Azure 帐户管理员，并选择用来注册 Azure Stack 的 Azure 订阅。 您可以查看 Azure Stack 使用情况数据量收费为每个使用的资源，如在下图中所示：

![计费流](media/azure-stack-usage-reporting/pricing-details.png)

对于 Azure Stack 开发工具包，Azure Stack 资源不会收费，因此所示价格为 $0.00。

## <a name="which-azure-stack-deployments-are-charged"></a>哪些 Azure Stack 部署需要付费？

在 Azure Stack 开发工具包中使用资源无需付费。 对于 Azure Stack 多节点系统，工作负荷 VM、存储服务与应用服务需要付费。

## <a name="are-users-charged-for-the-infrastructure-vms"></a>使用基础结构 VM 是否需要付费？

不。 使用情况数据的某些 Azure Stack 资源提供程序 Vm 报告到 Azure，但无需支付费用为这些 Vm，也不在部署期间创建的 vm，以便在 Azure Stack 基础结构。  

用户只需为租户订阅下运行的 VM 付费。 必须将所有工作负荷部署在租户订阅之下，才符合 Azure Stack 的许可条款。

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>我有 Windows Server 的许可证，如何在 Azure Stack 上使用它？

使用现有许可证可避免生成用量计量值。 可以根据 [Azure Stack 许可指南](https://go.microsoft.com/fwlink/?LinkId=851536)的“在 Azure Stack 中使用现有软件”部分所述，在 Azure Stack 中使用现有的 Windows Server 许可证。 若要使用其现有许可证，客户必须部署其 Windows Server 虚拟机中所述[Windows Server 许可证的混合权益](/azure/virtual-machines/windows/hybrid-use-benefit-licensing)。

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>从哪个订阅中收取消耗的资源费用？

从[将 Azure Stack 注册到 Azure](azure-stack-registration.md) 时提供的订阅收费。

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>哪些类型的订阅支持用量数据报告？

对于 Azure Stack 多节点，支持企业协议 (EA) 和 CSP 订阅。 对于 Azure Stack 开发工具包，企业协议 (EA)，即用即付、 CSP 和 MSDN 订阅支持用量数据报告。

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>是否可以在主权云中运行用量数据报告？

在 Azure Stack 开发工具包中，用量数据报告需要全球 Azure 系统中创建的订阅。 借助 Azure，无法注册某个主权云 （Azure 政府、 Azure Germany 和 Azure 中国云） 中创建的订阅，因此它们不支持用量数据报告。

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>为什么在 Azure Stack 中报告的用量与从 Azure 帐户中心生成的报表不匹配？

始终是 Azure Stack 用量 Api 报告的使用情况数据和报告 Azure 帐户中心中的使用情况数据之间的延迟。 之所以发生这种延迟，是因为需要将用量数据从 Azure Stack 上传到 Azure 商务系统。 由于这种延迟，午夜之前短暂发生的使用情况可能会在 Azure 中显示下一天。 如果使用 [Azure Stack 用量 API](azure-stack-provider-resource-api.md) 并将结果与 Azure 计费门户中报告的用量相比较，则可能会看到差异。

## <a name="next-steps"></a>后续步骤

- [提供者使用情况 API](azure-stack-provider-resource-api.md)  
- [租户使用情况 API](azure-stack-tenant-resource-usage-api.md)
- [使用情况常见问题](azure-stack-usage-related-faq.md)
- [以云服务提供商身份管理使用情况和计费](azure-stack-add-manage-billing-as-a-csp.md)
