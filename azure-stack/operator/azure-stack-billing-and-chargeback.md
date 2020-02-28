---
title: Azure Stack 中心中的客户计费和退款
description: 了解 Azure Stack 集线器用户如何计费资源使用情况，以及如何访问计费信息以进行分析和按使用计费。
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: b5de9aa3723d16ab4c80510ab5e18d0300ebde04
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77695387"
---
# <a name="usage-and-billing-in-azure-stack-hub"></a>Azure Stack 中心中的使用情况和计费

本文介绍了如何为资源使用情况计费 Azure Stack 集线器用户，以及如何访问计费信息进行分析和计费。

Azure Stack 中心收集使用的资源的使用数据并对其进行分组，然后将此数据转发到 Azure Commerce。 Azure Commerce 为你提供 Azure Stack 集线器使用方式，就像对 Azure 使用计费一样。

你还可以获取使用情况数据，并使用计费适配器将其导出到你自己的计费或使用计费系统，或将其导出到 Microsoft Power BI 等商业智能工具。

## <a name="usage-pipeline"></a>使用管道

Azure Stack 集线器中的每个资源提供程序都按资源使用情况发布使用数据。 使用情况服务定期（每小时和每天）聚合使用情况数据并将其存储在使用情况数据库中。 Azure Stack 中心操作员和用户可以通过 Azure Stack 中心资源使用情况 Api 访问存储的使用情况数据。

如果已向[azure 注册 Azure Stack 集线器实例](azure-stack-registration.md)，Azure Stack 中心将配置为将使用情况数据发送到 azure Commerce。 将数据上传到 Azure 后，可以通过计费门户或使用 Azure 资源使用情况 Api 来访问数据。 有关向 Azure 报告的使用情况数据的详细信息，请参阅[使用情况数据报告](azure-stack-usage-reporting.md)。  

下图显示了使用情况管道中的关键组件：

![使用管道](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>我可以找到哪些使用情况信息以及如何操作？

Azure Stack 中心资源提供程序（例如计算、存储和网络）每个订阅按小时为间隔生成使用情况数据。 使用情况数据包含有关使用的资源的信息，例如资源名称、所用订阅和使用的数量。 若要了解有关计量的 ID 资源，请参阅[使用情况 API 常见问题解答](azure-stack-usage-related-faq.md)。

收集使用情况数据后，会将其[报告给 Azure](azure-stack-usage-reporting.md)以生成帐单，这可以通过 Azure 计费门户查看。

> [!NOTE]  
> 对于 Azure Stack 开发工具包（ASDK）以及在容量模型下许可的 Azure Stack 中心集成系统用户，不需要使用数据报告。 若要详细了解 Azure Stack 中心中的许可，请参阅[打包和定价数据表](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。

Azure 计费门户显示了应计费资源的使用情况数据。 除了计费资源外，Azure Stack 中心还会捕获更广泛资源的使用情况数据，可通过 REST Api 或 PowerShell cmdlet 在 Azure Stack 中心环境中访问这些资源。 Azure Stack 中心操作员可以获取所有用户订阅的使用情况数据。 单个用户只能获取其自己的使用情况详细信息。

## <a name="usage-reporting-for-multi-tenant-cloud-solution-providers"></a>多租户云解决方案提供商的使用情况报告

使用 Azure Stack 集线器的多租户云解决方案提供程序（CSP）可能需要单独报告每个客户的使用情况，以便提供商可以将使用情况计费到不同的 Azure 订阅。

每个客户都有不同 Azure Active Directory （Azure AD）租户表示的标识。 Azure Stack 中心支持为每个 Azure AD 租户分配一个 CSP 订阅。 可以将租户及其订阅添加到基本 Azure Stack 中心注册。 基本注册针对所有 Azure Stack 中心实例完成。 如果订阅未注册到租户，用户仍可使用 Azure Stack 中心，并将其使用情况发送到用于基本注册的订阅。

## <a name="next-steps"></a>后续步骤

- [向 Azure Stack 集线器注册](azure-stack-registration.md)
- [向 Azure 报告 Azure Stack 集线器使用情况数据](azure-stack-usage-reporting.md)
- [提供程序资源使用情况 API](azure-stack-provider-resource-api.md)
- [租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md)
- [使用情况相关的常见问题](azure-stack-usage-related-faq.md)
