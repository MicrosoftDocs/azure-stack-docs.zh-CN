---
title: Azure Stack Hub 集成系统的 Azure 联网部署决策
description: 确定部署规划决策，以便对 Azure Stack Hub 集成系统（包括计费和标识）进行 Azure 连接型部署。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wfayed
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: b8f8dfe95e50b81c7032e2e2348c2d4e6a6d888d
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "78364762"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-hub-integrated-systems"></a>Azure Stack Hub 集成系统的 Azure 联网部署规划决策
在决定[如何将 Azure Stack Hub 集成到混合云环境](azure-stack-connection-models.md)后，即可完成 Azure Stack Hub 部署决策。

部署连接到 Azure 的 Azure Stack Hub 意味着可以将 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 用于标识存储。 也可从以下计费模式进行选择：即用即付或按容量付费。 联网部署是默认选项，因为它可以让客户充分利用 Azure Stack Hub，尤其是在使用涉及到 Azure 和 Azure Stack Hub 的混合云方案的情况下。

## <a name="choose-an-identity-store"></a>选择标识存储
使用连接型部署时，可以选择将 Azure AD 或 AD FS 用于标识存储。 没有 Internet 连接的断开连接型部署只能使用 AD FS。

所做的标识存储选择不影响租户虚拟机 (VM)。 租户 VM 可以选择要连接到的标识存储，具体取决于其将来的配置情况：Azure AD、加入 Windows Server Active Directory 域、工作组，等等。 这与 Azure Stack Hub 标识提供者决策无关。

例如，如果在 Azure Stack Hub 基础上部署 IaaS 租户 VM，需要它们加入公司 Active Directory 域并使用其中的帐户，则仍可这样做。 不需将此处选择的 Azure AD 标识存储用于这些帐户。

### <a name="azure-ad-identity-store"></a>Azure AD 标识存储
将 Azure AD 用于标识存储时，需要两个 Azure AD 帐户：全局管理员帐户和计费帐户。 这两个帐户可以相同，也可以不同。 虽然使用同一用户帐户可能更简单，适用于 Azure 帐户数有限的情况，但考虑到业务需求，某些情况下也可能需要使用两个帐户：

1. **全局管理员帐户**（仅连接型部署需要） 这种 Azure 帐户用于为 Azure AD 中的 Azure Stack Hub 基础结构服务创建应用和服务主体。 此帐户必须拥有 Azure Stack Hub 系统部署时所在目录的管理员权限。 它将成为 Azure AD 用户的“云操作员”全局管理员，用于以下任务：

    - 为所有需要与 Azure AD 和图形 API 交互的 Azure Stack Hub 服务预配和委托应用和服务主体。
    - 充当服务管理员帐户。 此帐户是默认提供商订阅（可以稍后更改）的所有者。 可以使用此帐户登录到 Azure Stack Hub 管理员门户，并且可以使用它在 Azure Stack Hub 中创建套餐和计划、设置配额，以及执行其他管理功能。

2. **计费帐户**（连接型部署和断开连接型部署都需要）。 此 Azure 帐户用来在 Azure Stack Hub 集成系统和 Azure 商务后端之间建立计费关系。 这是将对其收取 Azure Stack Hub 费用的帐户。 此帐户还将用于提供市场中的项目和其他混合方案。

### <a name="ad-fs-identity-store"></a>AD FS 标识存储
如果需要将自己的标识存储（例如公司 Active Directory）用于服务管理员帐户，请选择此选项。  

## <a name="choose-a-billing-model"></a>选择计费模型
可以选择“即用即付”或“容量”计费模型。******** 即用即付计费模型部署必须能够通过到 Azure 的连接报告使用情况，每 30 天至少报告一次。 因此，即用即付计费模型仅适用于已连接的部署。  

### <a name="pay-as-you-use"></a>即用即付
使用即用即付计费模式时，将对 Azure 订阅收取使用费用。 仅在使用 Azure Stack 中心服务时付费。 如果这是决定要用的模型，则需一个 Azure 订阅，以及与该订阅相关联的帐户 ID（例如，serviceadmin@contoso.onmicrosoft.com）。 支持 EA、CSP、CSL 订阅。 使用情况报告在[Azure Stack Hub 注册](azure-stack-registration.md)期间进行配置。

> [!NOTE]
> 大多数情况下，企业客户会使用 EA 订阅，服务提供商会使用 CSP 或 CSL 订阅。

若要使用 CSP 订阅，请查看下表以确定要使用的 CSP 订阅，因为正确的方法取决于具体的 CSP 方案：

|方案|域和订阅选项|
|-----|-----|
|你是**直接 Csp 合作伙伴**或**间接 csp 提供商**，你将运行 Azure Stack 中心|使用 CSL（通用服务层）订阅。<br>     、<br>在合作伙伴中心创建带描述性名称的 Azure AD 租户。 例如，&lt;你的组织>CSPAdmin，带有关联的 Azure CSP 订阅。|
|你是一个**间接 CSP 经销商**，你将运行 Azure Stack 中心|要求间接 CSP 提供商使用合作伙伴中心为你的组织创建一个 Azure AD 租户，带有关联的 Azure CSP 订阅。|

### <a name="capacity-based-billing"></a>基于容量的计费
如果决定使用容量计费模型，则必须根据系统容量购买 Azure Stack 集线器容量计划 SKU。 你需要知道 Azure Stack 集线器中的物理内核数，以购买正确的数量。

容量计费需要企业协议 (EA) Azure 订阅才能注册。 原因是注册设置了市场中项目的可用性，这需要 Azure 订阅。 此订阅不用于 Azure Stack 集线器使用情况。

## <a name="learn-more"></a>了解详细信息
- 有关用例、购买、合作伙伴和 OEM 硬件供应商的信息，请参阅[Azure Stack 中心](https://azure.microsoft.com/overview/azure-stack/)产品页。
- 有关 Azure Stack 中心集成系统的路线图和地域可用性的信息，请参阅白皮书： [Azure Stack 中心： Azure 的扩展](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)。 
- 若要了解有关 Microsoft Azure Stack 中心打包和定价的详细信息，请[下载 .pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。 

## <a name="next-steps"></a>后续步骤
[数据中心网络集成](azure-stack-network.md)