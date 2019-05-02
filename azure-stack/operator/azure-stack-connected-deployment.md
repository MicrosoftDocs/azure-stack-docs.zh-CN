---
title: Azure Stack 集成系统的 Azure 连接型部署决策 | Microsoft Docs
description: 确定多节点 Azure Stack Azure 连接型部署的部署计划决策。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: a39ab2eb6d08f478ed68aed2bc753e5225185fe3
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64290216"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Azure Stack 集成系统的 Azure 连接型部署计划决策
在决定[如何将 Azure Stack 集成到混合云环境](azure-stack-connection-models.md)后，可以完成 Azure Stack 部署决策。

部署连接到 Azure 的 Azure Stack 意味着可以将 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 用于标识存储。 也可从以下计费模式进行选择：即用即付或按容量付费。 联网部署是默认选项，因为这样可以让客户充分利用 Azure Stack，尤其是在使用涉及到 Azure 和 Azure Stack 的混合云方案的情况下。 

## <a name="choose-an-identity-store"></a>选择标识存储
使用连接型部署时，可以选择将 Azure AD 或 AD FS 用于标识存储。 没有 Internet 连接的断开连接型部署只能使用 AD FS。

所做的标识存储选择不影响租户虚拟机 (VM)。 租户 VM 可以选择要连接到的标识存储，具体取决于其将来的配置情况：Azure AD、加入 Windows Server Active Directory 域、工作组，等等。这与 Azure Stack 标识提供者决策无关。 

例如，如果在 Azure Stack 基础上部署 IaaS 租户 VM，需要它们加入公司 Active Directory 域并使用其中的帐户，则仍可这样做。 不需将此处选择的 Azure AD 标识存储用于这些帐户。

### <a name="azure-ad-identity-store"></a>Azure AD 标识存储
将 Azure AD 用于标识存储时，需要两个 Azure AD 帐户：全局管理员帐户和计费帐户。 这两个帐户可以相同，也可以不同。 虽然使用同一用户帐户可能更简单，适用于 Azure 帐户数有限的情况，但考虑到业务需求，某些情况下也可能需要使用两个帐户：

1. **全局管理员帐户**（仅连接型部署需要） 这种 Azure 帐户用于为 Azure Active Directory 中的 Azure Stack 基础结构服务创建应用程序和服务主体。 此帐户必须拥有 Azure Stack 系统部署时所在目录的管理员权限。 它将成为 Azure AD 租户的“云操作员”全局管理员，可用于以下操作： 
    - 预配和委托应用程序和服务主体，这适用于所有需要与 Azure Active Directory 和图形 API 交互的 Azure Stack 服务。 
    - 充当服务管理员帐户。 这是默认提供商订阅（可以稍后更改）的所有者。 可以使用此帐户登录到 Azure Stack 管理门户，可以使用它在 Azure Stack 中创建套餐和计划、设置配额，以及执行其他管理功能。
2. **计费帐户**（连接型部署和断开连接型部署都需要）。 此 Azure 帐户用来在 Azure Stack 集成系统和 Azure 商务后端之间建立计费关系。 这是将对其收取 Azure Stack 费用的帐户。 此帐户还将用于提供市场中的项目和其他混合方案。 

### <a name="ad-fs-identity-store"></a>AD FS 标识存储
如果需要将自己的标识存储（例如公司 Active Directory）用于服务管理员帐户，请选择此选项。  

## <a name="choose-a-billing-model"></a>选择计费模型
可以选择“即用即付”或“容量”计费模型。 即用即付计费模型部署必须能够通过到 Azure 的连接报告使用情况，每 30 天至少报告一次。 因此，即用即付计费模型仅适用于连接型部署。  

### <a name="pay-as-you-use"></a>即用即付
使用即用即付计费模型时，使用费用计到 Azure 订阅中。 只有在使用 Azure Stack 服务时才需付费。 如果这是决定要用的模型，则需一个 Azure 订阅，以及与该订阅相关联的帐户 ID（例如，serviceadmin@contoso.onmicrosoft.com）。 支持 EA、CSP、CSL 订阅。 使用情况报告在 [Azure Stack 注册](azure-stack-registration.md)期间配置。

> [!NOTE]
> 大多数情况下，企业客户会使用 EA 订阅，服务提供商会使用 CSP 或 CSL 订阅。

若要使用 CSP 订阅，请查看下表以确定要使用的 CSP 订阅，因为正确的方法取决于具体的 CSP 方案：

|场景|域和订阅选项|
|-----|-----|
|你是**直接 CSP 合作伙伴**或**间接 CSP 提供商**，你将操作 Azure Stack|使用 CSL（通用服务层）订阅。<br>     或<br>在合作伙伴中心创建带描述性名称的 Azure AD 租户。 例如，&lt;你的组织>CSPAdmin，带有关联的 Azure CSP 订阅。|
|你是**间接 CSP 经销商**，并将操作 Azure Stack|要求间接 CSP 提供商使用合作伙伴中心为你的组织创建一个 Azure AD 租户，带有关联的 Azure CSP 订阅。|

### <a name="capacity-based-billing"></a>按容量计费
如果决定使用容量计费模型，则必须根据系统的容量购买 Azure Stack 容量计划 SKU。 需要知道 Azure Stack 中的物理核心数才能确保购买的数量是正确的。 

容量计费需要企业协议 (EA) Azure 订阅才能注册。 原因是注册设置了市场中项目的可用性，这需要 Azure 订阅。 此订阅不适用于 Azure Stack 使用情况。

## <a name="learn-more"></a>了解详细信息
- 有关用例、购买、合作伙伴和 OEM 硬件供应商的信息，请参阅 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 产品页。
- 有关 Azure Stack 集成系统的路线图和上市区域的信息，请参阅白皮书：[Azure Stack：Azure 的扩展](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)。 
- 若要了解有关 Microsoft Azure Stack 打包和定价的详细信息[下载此.pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。 

## <a name="next-steps"></a>后续步骤
[数据中心网络集成](azure-stack-network.md)