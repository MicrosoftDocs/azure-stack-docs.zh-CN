---
title: Azure Stack 集线器集成系统的 Azure 连接的部署决策
description: 确定 Azure Stack 集线器集成系统的 Azure 连接部署的部署规划决策，包括计费和标识。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wfayed
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: b8f8dfe95e50b81c7032e2e2348c2d4e6a6d888d
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364762"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-hub-integrated-systems"></a>Azure Stack 集线器集成系统的 Azure 连接的部署规划决策
确定[将 Azure Stack 中心集成到混合云环境的方式](azure-stack-connection-models.md)后，可以完成 Azure Stack 中心部署决策。

部署连接到 Azure Azure Stack 集线器意味着可以为标识存储提供 Azure Active Directory （Azure AD）或 Active Directory 联合身份验证服务（AD FS）。 你还可以从计费模型中进行选择： "即用即付" 或 "基于容量"。 已连接的部署是默认选项，因为它允许客户从 Azure Stack 中心获得最大价值，尤其适用于涉及 Azure 和 Azure Stack 中心的混合云方案。

## <a name="choose-an-identity-store"></a>选择标识存储
使用连接的部署，可以为标识存储选择 Azure AD 或 AD FS。 断开连接的部署（不带 internet 连接）只能使用 AD FS。

你的标识存储选择与租户虚拟机（Vm）无关。 租户 Vm 可能会根据配置方式选择要连接的标识存储： Azure AD、Windows Server Active Directory 加入域、工作组等。 这与 Azure Stack 中心标识提供程序决策无关。

例如，如果将 IaaS 租户 Vm 部署在 Azure Stack 中心之上，并希望它们加入公司 Active Directory 域并使用其中的帐户，则仍可。 不需要使用在此处为这些帐户选择的 Azure AD 标识存储。

### <a name="azure-ad-identity-store"></a>Azure AD 标识存储
为标识存储使用 Azure AD 需要两个 Azure AD 帐户：全局管理员帐户和计费帐户。 这些帐户可以是相同的帐户，也可以是不同的帐户。 当使用相同的用户帐户时，如果你拥有有限数量的 Azure 帐户，则你的业务需求可能会建议使用两个帐户：

1. **全局管理员帐户**（仅适用于连接的部署）。 这是一个 Azure 帐户，用于在 Azure AD 中创建 Azure Stack 集线器基础结构服务的应用和服务主体。 此帐户必须对要在其下部署 Azure Stack 中心系统的目录具有目录管理员权限。 它将成为 Azure AD 用户的 "云操作员" 全局管理员，并用于以下任务：

    - 为需要与 Azure AD 和图形 API 交互的所有 Azure Stack 中心服务预配和委托应用程序和服务主体。
    - 作为服务管理员帐户。 此帐户是默认提供程序订阅（你稍后可以更改）的所有者。 你可以使用此帐户登录到 Azure Stack 中心管理员门户，并使用它来创建产品/服务和计划、设置配额，以及在 Azure Stack 中心中执行其他管理功能。

2. **计费帐户**（连接和断开连接的部署都需要）。 此 Azure 帐户用于建立 Azure Stack 中心集成系统与 Azure commerce 后端之间的计费关系。 这是对 Azure Stack 中心费用计费的帐户。 此帐户还将用于在 marketplace 和其他混合方案中提供项目。

### <a name="ad-fs-identity-store"></a>AD FS 标识存储
如果要为服务管理员帐户使用自己的标识存储（如公司 Active Directory），请选择此选项。  

## <a name="choose-a-billing-model"></a>选择计费模型
您可以选择 "即**用即付**" 或 "**容量**计费模型"。 即用即付计费模型部署必须至少每隔30天通过与 Azure 的连接报告使用情况。 因此，即用即付计费模型仅适用于已连接的部署。  

### <a name="pay-as-you-use"></a>即用即付
使用即用即付计费模式时，将对 Azure 订阅收取使用费用。 仅在使用 Azure Stack 中心服务时付费。 如果这是你决定的模型，你将需要一个 Azure 订阅以及与该订阅关联的帐户 ID （例如 serviceadmin@contoso.onmicrosoft.com）。 支持 EA、CSP 和 CSL 订阅。 使用情况报告在[Azure Stack Hub 注册](azure-stack-registration.md)期间进行配置。

> [!NOTE]
> 在大多数情况下，企业客户将使用 EA 订阅，服务提供商将使用 CSP 或 CSL 订阅。

如果要使用 CSP 订阅，请查看下表以确定要使用哪个 CSP 订阅，因为正确的方法取决于确切的 CSP 方案：

|场景|域和订阅选项|
|-----|-----|
|你是**直接 Csp 合作伙伴**或**间接 csp 提供商**，你将运行 Azure Stack 中心|使用 CSL （常见服务层）订阅。<br>     或<br>在合作伙伴中心创建一个具有描述性名称的 Azure AD 租户。 例如，&lt;你的组织 > CSPAdmin 与它相关联的 Azure CSP 订阅。|
|你是一个**间接 CSP 经销商**，你将运行 Azure Stack 中心|要求间接 CSP 提供商使用合作伙伴中心为你的组织创建一个与之关联的 Azure CSP 订阅的 Azure AD 租户。|

### <a name="capacity-based-billing"></a>基于容量的计费
如果决定使用容量计费模型，则必须根据系统容量购买 Azure Stack 集线器容量计划 SKU。 你需要知道 Azure Stack 集线器中的物理内核数，以购买正确的数量。

容量计费要求使用企业协议（EA） Azure 订阅进行注册。 原因是注册在 Marketplace 中设置了项目的可用性，这需要 Azure 订阅。 此订阅不用于 Azure Stack 集线器使用情况。

## <a name="learn-more"></a>了解详细信息
- 有关用例、购买、合作伙伴和 OEM 硬件供应商的信息，请参阅[Azure Stack 中心](https://azure.microsoft.com/overview/azure-stack/)产品页。
- 有关 Azure Stack 中心集成系统的路线图和地域可用性的信息，请参阅白皮书： [Azure Stack 中心： Azure 的扩展](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)。 
- 若要了解有关 Microsoft Azure Stack 中心打包和定价的详细信息，请[下载 .pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。 

## <a name="next-steps"></a>后续步骤
[数据中心网络集成](azure-stack-network.md)