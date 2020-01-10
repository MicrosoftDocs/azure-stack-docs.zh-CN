---
title: 向 Azure 报告 Azure Stack 集线器使用情况数据 |Microsoft Docs
description: 了解如何在 Azure Stack Hub 中设置使用情况数据报告。
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
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 17cb3b649e253f610c30b7c6e10b442498340242
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75812988"
---
# <a name="report-azure-stack-hub-usage-data-to-azure"></a>向 Azure 报告 Azure Stack 集线器使用情况数据

使用情况数据也称为消耗数据，表示使用的资源量。

使用基于消耗的计费模型的 Azure Stack 集线器多节点系统应将使用情况数据报告给 Azure，以便进行计费。 Azure Stack 中心操作员应配置其 Azure Stack 集线器实例，以将使用情况数据报告给 Azure。

> [!IMPORTANT]
> 所有工作负载都[必须在租户订阅下部署](#are-users-charged-for-the-infrastructure-vms)，才能符合 Azure Stack 中心的许可条款。

使用即用即付模式下的 Azure Stack 中心多节点用户需要使用数据报告。 对于在容量模式下进行许可的客户，它是可选的（请参阅[如何购买](https://azure.microsoft.com/overview/azure-stack/how-to-buy/)页）。 对于 Azure Stack 开发工具包（ASDK）用户，Azure Stack 中心操作员可以报告使用数据并测试该功能。 但是，不会对用户所产生的任何使用量向用户收费。

![计费流程](media/azure-stack-usage-reporting/billing-flow.png)

使用情况数据通过 Azure Bridge 从 Azure Stack 集线器发送到 Azure。 在 Azure 中，商务系统会处理使用情况数据并生成帐单。 生成帐单后，Azure 订阅所有者可以从[Azure 帐户中心](https://account.windowsazure.com/subscriptions)查看并下载帐单。 若要了解 Azure Stack 集线器的许可方式，请参阅[Azure Stack 中心打包和定价文档](https://go.microsoft.com/fwlink/?LinkId=842847)。

## <a name="set-up-usage-data-reporting"></a>设置使用情况数据报告

若要设置使用情况数据报告，必须将[Azure Stack 集线器实例注册到 Azure](azure-stack-registration.md)。 作为注册过程的一部分，将配置 Azure Stack 中心的 Azure Bridge 组件，该组件将 Azure Stack 集线器连接到 Azure 并发送使用情况数据。 以下使用情况数据从 Azure Stack 集线器发送到 Azure：

- **计量 id** -已使用的资源的唯一 ID。
- **数量**-资源使用量。
- **位置**-当前 Azure Stack 中心资源的部署位置。
- **资源 URI** -正在报告其使用情况的资源的完全限定 URI。
- **订阅 id** -Azure Stack 中心用户的订阅 id，该 id 是本地（Azure Stack 中心）订阅。
- 使用情况数据的**开始时间和**结束时间。 在 Azure Stack 集线器中使用这些资源的时间与将使用情况数据报告给商务之间存在一定的延迟。 Azure Stack 中心每24小时聚合一次使用数据，并在 Azure 中将使用情况数据报告给商务管道需要花费几个小时。 因此，午夜之前不久就会出现在 Azure 中。

## <a name="generate-usage-data-reporting"></a>生成使用情况数据报告

- 若要测试使用情况数据报告，请在 Azure Stack Hub 中创建几个资源。 例如，可以创建一个包含基本和标准 Sku 的[存储帐户](azure-stack-provision-storage-account.md)、 [Windows Server VM](../user/azure-stack-create-vm-template.md)和 Linux VM，以查看如何报告内核使用情况。 不同类型的资源的使用数据在不同的指标下报告。

- 让你的资源运行几个小时。 大约每小时收集一次使用情况信息。 收集之后，会将此数据传输到 Azure 并处理到 Azure commerce 系统。 此过程可能需要几个小时。

## <a name="view-usage---csp-subscriptions"></a>查看使用情况-CSP 订阅

如果使用 CSP 订阅注册了 Azure Stack 中心，则可以使用查看 Azure 消耗的相同方式查看使用情况和费用。 Azure Stack 集线器使用情况包括在发票和对帐文件中，可通过[合作伙伴中心](https://partnercenter.microsoft.com/partner/home)获得。 对帐文件每月更新一次。 如果需要访问最新的 Azure Stack 集线器使用情况信息，可以使用合作伙伴中心 Api。

![合作伙伴中心](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage---enterprise-agreement-subscriptions"></a>查看使用情况-企业协议订阅

如果你使用企业协议订阅注册了 Azure Stack 中心，则可以在[EA 门户](https://ea.azure.com/)中查看你的使用情况和费用。 此门户中 "报告" 部分下的 "Azure 使用情况" 中提供了 Azure Stack 集线器使用情况。

## <a name="view-usage---other-subscriptions"></a>查看使用情况-其他订阅

如果使用任何其他订阅类型注册了 Azure Stack 集线器，则为;例如，即用即付订阅，你可以查看 Azure 帐户中心中的使用情况和费用。 以 Azure 帐户管理员身份登录到[Azure 帐户中心](https://account.windowsazure.com/subscriptions)，并选择用于注册 Azure Stack 中心的 Azure 订阅。 你可以查看 Azure Stack 集线器使用情况数据、为每个已用资源支付的金额，如下图所示：

![计费流程](media/azure-stack-usage-reporting/pricing-details.png)

对于 ASDK，Azure Stack 集线器资源不收费，因此显示的价格为 $0.00。

## <a name="which-azure-stack-hub-deployments-are-charged"></a>哪些 Azure Stack 的中心部署需要付费？

资源使用情况对 ASDK 是免费的。 Azure Stack 中心多节点系统、工作负荷 Vm、存储服务和应用服务。

## <a name="are-users-charged-for-the-infrastructure-vms"></a>是否需要为基础结构 Vm 支付用户费用？

不。 某些 Azure Stack 中心资源提供程序 Vm 的使用情况数据会报告给 Azure，但对于这些 Vm，或在部署期间创建的 Vm，不收取任何费用来启用 Azure Stack 中心基础结构。  

用户只需为租户订阅下运行的 Vm 付费。 所有工作负载都必须在租户订阅下部署，才能符合 Azure Stack 中心的许可条款。

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-hub-how-do-i-do-it"></a>我有一个要在 Azure Stack 中心使用的 Windows Server 许可证，如何实现此目的？

使用现有的许可证可避免生成使用情况计量。 可在 Azure Stack 中心使用现有的 Windows Server 许可证，如《 [Azure Stack 集线器许可指南》](https://go.microsoft.com/fwlink/?LinkId=851536)中的 "使用现有软件与 Azure Stack 中心" 一节中所述。 为了使用现有许可证，客户必须按照[Windows server 许可证的混合权益](/azure/virtual-machines/windows/hybrid-use-benefit-licensing)中所述部署其 windows server 虚拟机。

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>为消耗的资源收取哪种订阅？

[向 Azure 注册 Azure Stack 集线器](azure-stack-registration.md)时提供的订阅。

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>使用情况数据报表支持哪种类型的订阅？

支持 Azure Stack 中心多节点、企业协议（EA）和 CSP 订阅。 对于 Azure Stack 开发工具包、企业协议（EA）、即用即付、CSP 和 MSDN 订阅支持使用情况数据报告。

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>使用情况数据报告是否适用于主权云中？

在 Azure Stack 开发工具包中，使用情况数据报告需要在全局 Azure 系统中创建的订阅。 在主权云（Azure 政府、Azure 德国和 Azure 中国世纪互联云）之一中创建的订阅无法注册到 Azure，因此它们不支持使用情况数据报告。

## <a name="why-doesnt-the-usage-reported-in-azure-stack-hub-match-the-report-generated-from-azure-account-center"></a>为什么 Azure Stack 中心中报告的使用情况与 Azure 帐户中心生成的报告不匹配？

Azure Stack 集线器使用情况 Api 报告的用量数据与 Azure 帐户中心中报告的使用情况数据之间始终存在延迟。 此延迟是将使用情况数据从 Azure Stack 中心上载到 Azure commerce 所需的时间。 由于此延迟，在午夜之前不久就会出现在 Azure 中。 如果使用[Azure Stack 集线器使用情况 api](azure-stack-provider-resource-api.md)，并将结果与在 Azure 计费门户中报告的使用情况进行比较，则可以看到不同之处。

## <a name="next-steps"></a>后续步骤

- [提供者使用情况 API](azure-stack-provider-resource-api.md)  
- [租户使用情况 API](azure-stack-tenant-resource-usage-api.md)
- [使用情况常见问题](azure-stack-usage-related-faq.md)
- [以云解决方案提供商的身份管理使用情况和计费](azure-stack-add-manage-billing-as-a-csp.md)
