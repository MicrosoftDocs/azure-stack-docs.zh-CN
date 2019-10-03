---
title: 在 Azure Stack 中委托套餐 | Microsoft Docs
description: 了解如何委托任务，如创建产品/服务和注册用户。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: eee3a928f8f3c6f376e9019af6da71a77ab09450
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829234"
---
# <a name="delegate-offers-in-azure-stack"></a>在 Azure Stack 中委托套餐

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

作为 Azure Stack 操作员，你可能希望让其他人负责注册用户和创建订阅。 例如，服务提供商可能需要经销商来代表他们注册和管理客户。 或者，如果你是企业的中心 IT 小组的成员，则可能需要委托其他 IT 工作人员来注册用户。

委托比你自己进行更容易联系和管理更多的用户，如下图所示：

![Azure Stack 中的委托级别](media/azure-stack-delegated-provider/image1.png)

通过委派，委派的提供商管理产品/服务（称为*委托产品*/服务），最终客户无需参与系统管理员的参与便可获取该产品/服务的订阅。

## <a name="delegation-roles"></a>委托角色

以下角色是委托的一部分：

* Azure Stack 操作员管理 Azure Stack 基础结构和创建套餐模板。 操作员委托他人向其租户提供套餐。

* 委托的 Azure Stack 操作员是名为“委托的提供商”的订阅中拥有“所有者”或“参与者”权限的用户。 他们可能属于其他组织，例如其他 Azure Active Directory (Azure AD) 租户。

* 用户可以注册并使用产品/服务来管理其工作负荷、创建 VM、存储数据，等等。

## <a name="delegation-steps"></a>委托步骤

设置委托的过程包括两个基本步骤：

1. **创建委托的提供商订阅**：让用户订阅仅包含订阅服务的套餐。 然后，订阅此套餐的用户可通过为其他用户注册委托的套餐，将这些套餐扩展到其他用户。

2. **将套餐委托给受委托的提供商**：此套餐可让委托的提供商创建订阅，或将套餐扩展到其用户。 委托的提供商现在可以接受该套餐，并将其提供给其他用户。

下图显示设置委托的步骤：

![创建委派的提供程序并使其能够在 Azure Stack 中注册用户的步骤](media/azure-stack-delegated-provider/image2.png)

### <a name="delegated-provider-requirements"></a>委托的提供商要求

若要充当委托的提供商，用户需要通过创建订阅来与主要提供商建立关系。 此订阅表明委托的提供商有权代表主提供商提供委托的套餐。

建立这种关系后，Azure Stack 操作员就可以将套餐委托给受委托的提供商。 委托的提供商可以接受该套餐，将其重命名（但不更改其实质）并提供给客户。

## <a name="delegation-walkthrough"></a>委托演练

以下部分提供有关设置委托的提供商、委托套餐，以及验证用户是否可以注册委托套餐的演练。

### <a name="set-up-roles"></a>设置角色

若要使用此演练，除了 Azure Stack 操作员帐户以外，还需要两个 Azure AD 帐户。 如果没有这两个帐户，则必须创建这些帐户。 这些帐户可以属于任何 Azure AD 用户，称为委托的提供商和用户。

| **角色** | **组织权限** |
| --- | --- |
| 委托的提供商 |“用户” |
| “用户” |“用户” |

 > [!NOTE]
 > 对于 CSP-经销商，创建此委托提供商要求这些用户位于租户目录（用户 Azure AD）中。 Azure Stack 运算符必须[首先](azure-stack-enable-multitenancy.md)将该租户 Azure AD 上，然后按照以下[步骤](azure-stack-csp-howto-register-tenants.md)设置使用情况和计费。

### <a name="identify-the-delegated-provider"></a>确定委托的提供商

1. 以 Azure Stack 操作员身份登录到管理员门户。

1. 创建可让用户成为委托提供商的套餐：

   a.  [创建计划](azure-stack-create-plan.md)。
       此计划应该只包括订阅服务。 本文使用名为 **PlanForDelegation** 的计划作为示例。

   b.  根据此计划[创建套餐](azure-stack-create-offer.md)。 本文使用名为 **OfferToDP** 的套餐作为示例。

   c.  依次选择“订阅”、“添加”、“新建租户订阅”，将委托的提供商作为订阅方添加到此套餐。

   ![将委托的提供程序作为订阅者添加到 Azure Stack 管理员门户](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > 对于所有 Azure Stack 套餐，可以选择将其公开给用户注册，也可以选择不公开，让 Azure Stack 操作员管理注册。 委托的提供商通常是一个小的组。 需要控制其成员，因此大多数情况下不应公开此产品/服务。

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Azure Stack 操作员创建委托的套餐

下一步是创建要委托的可供用户使用的计划和套餐。 最好是将此产品/服务定义为希望用户看到它，因为委托的提供商不能更改其包括的计划和配额。

1. 以 Azure Stack 操作员的身份[创建计划](azure-stack-create-plan.md)以及基于该计划的[套餐](azure-stack-create-offer.md)。 本文使用名为 **DelegatedOffer** 的套餐作为示例。

   > [!NOTE]
   > 此产品/服务不一定是公开的，但您可以将其公开。 但在大多数情况下，只希望委托提供商有权访问该产品/服务。 在你按照以下步骤中的说明委托专用产品/服务以后，委托的提供商即可对其进行访问。

2. 委托套餐。 转到“DelegatedOffer”。 在“设置”下，依次选择“委托的提供商”、“添加”。

3. 从下拉列表中选择委托的提供商的订阅，然后选择“委托”。

   ![在 Azure Stack 管理员门户中添加委托提供程序](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>委托的提供商自定义产品/服务

以委托的提供商身份登录到用户门户，然后使用委托的套餐作为模板来创建新的产品/服务。

1. 依次选择“+ 创建资源”、“租户套餐 + 计划”、“套餐”。

    ![在 Azure Stack 用户门户中创建新产品/服务](media/azure-stack-delegated-provider/image5.png)

2. 为产品/服务指定一个名称。 本示例使用 **ResellerOffer**。 选择委托的套餐作为模板，然后选择“创建”。

   ![在 Azure Stack 用户门户中分配名称](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >必须知道，委托的提供商只能选择委托给他们的套餐。 他们无法对这些套餐进行更改。 只有 Azure Stack 运算符可以更改这些产品/服务。 例如，只有操作员可以更改其计划和配额。 委托的提供商不会从基本计划和附加计划构造产品/服务。

3. 委托的提供商通过自己的门户 URL 公开这些套餐。 若要公开套餐，请依次选择“浏览”、“套餐”。 选择产品/服务，然后选择“更改状态”。

4. 目前，只能通过委托门户查看公开的已委托套餐。 若要查找和更改此 URL，请执行以下操作：

    a.  依次选择“浏览”、“所有服务”，然后在“常规”类别下面选择“订阅”。 选择委托的提供商订阅（例如， **DPSubscription**），然后选择 "**属性**"。

    b.  将门户 URL 复制到单独的位置，例如记事本。

    ![在 Azure Stack 用户门户中选择委托的提供程序订阅](media/azure-stack-delegated-provider/dpportaluri.png)  

   现已完成创建委托的套餐作为委托的提供商的过程。 以委托的提供商身份注销，并关闭浏览器窗口。

### <a name="sign-up-for-the-offer"></a>注册产品/服务

1. 在新的浏览器窗口中，转到上一步保存的委托的门户 URL。 以用户身份登录到门户。

   >[!NOTE]
   >委托的产品/服务不可见，除非你使用委派的门户。

1. 在仪表板中，选择“获取订阅”。 可以看到，只向用户提供由委托的提供商创建的委托的套餐。

   ![在 Azure Stack 用户门户中查看和选择产品/服务](media/azure-stack-delegated-provider/image8.png)

委托套餐的过程已完成。 现在，用户可以通过获取套餐订阅来注册此套餐。

## <a name="move-subscriptions-between-delegated-providers"></a>在委托的提供商之间移动订阅

如果需要，可在属于同一目录租户的新的或现有委托的提供商订阅之间移动订阅。 你可以使用 PowerShell cmdlet [AzsSubscription](/powershell/module/azs.subscriptions.admin)移动它们。

移动订阅在以下情况中非常有用：

* 登记将要充当委托的提供商角色的新团队成员，并想要将以前在“默认提供商订阅”中创建的用户订阅分配给此团队成员。
* 同一目录租户（Azure AD）中有多个委托提供商订阅，需要在这些订阅之间移动用户订阅。 当团队成员在团队之间移动，并且必须将其订阅分配给新团队时，可能会发生这种情况。

## <a name="next-steps"></a>后续步骤

* [预配 VM](../user/azure-stack-create-vm-template.md)
