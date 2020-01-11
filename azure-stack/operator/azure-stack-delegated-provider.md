---
title: 在 Azure Stack 中心内委托服务 |Microsoft Docs
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
ms.openlocfilehash: 8e9ad6917a6f8db571fc41b88fb9ef29e25d247c
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882634"
---
# <a name="delegate-offers-in-azure-stack-hub"></a>Azure Stack 中心内委托服务

作为 Azure Stack 中心操作员，你可能想要让其他人负责注册用户和创建订阅。 例如，如果你是服务提供商，你可能希望经销商以你的名义注册客户并进行管理。 或者，如果你是企业中的中心 IT 小组的成员，则可能需要将用户注册委派给其他 IT 人员。

委托使你可以更轻松地访问和管理超过你自己的用户，如下图所示：

![Azure Stack 中心中的委托级别](media/azure-stack-delegated-provider/image1.png)

通过委派，委派的提供商管理产品/服务（称为*委托产品*/服务），最终客户无需参与系统管理员的参与便可获取该产品/服务的订阅。

## <a name="delegation-roles"></a>委托角色

以下角色属于委托：

* *Azure Stack 中心操作员*管理 Azure Stack 中心基础结构，并创建产品/服务模板。 操作员将其他人委托给其租户。

* 委托的 Azure Stack 中心操作员是订阅中的 "*所有者*" 或 "*参与者*" 权限的*用户。* 它们可以属于其他组织，如其他 Azure Active Directory （Azure AD）租户。

* *用户*注册了产品/服务，并使用它们来管理工作负荷、创建 vm、存储数据等。

## <a name="delegation-steps"></a>委托步骤

设置委派的步骤有两个：

1. **创建委派的提供商订阅**：仅将用户订阅到包含订阅服务的产品/服务。 然后，订阅此产品/服务的用户可以向其他用户注册这些产品/服务。

2. 将**产品/服务委托给委托提供商**：此产品/服务使委托的提供商能够创建订阅或向其用户扩展产品/服务。 委托的提供商现在可以接受该产品/服务，并将其提供给其他用户。

下图显示了设置委派的步骤：

![创建委派的提供程序并使其能够在 Azure Stack 中心注册用户的步骤](media/azure-stack-delegated-provider/image2.png)

### <a name="delegated-provider-requirements"></a>委托的提供程序要求

若要充当委托的提供商，用户可以通过创建订阅与主提供者建立关系。 此订阅标识委派的提供程序，使其拥有代表主提供者显示委托服务的权限。

建立此关系后，Azure Stack 中心操作员可以将产品/服务委托给委托的提供商。 委托的提供商可以接受该产品/服务，对其进行重命名（但不要更改其物质），并为其客户提供该服务。

## <a name="delegation-walkthrough"></a>委托演练

以下各节提供了有关设置委托提供程序、委托产品/服务以及验证用户是否可以注册委托产品/服务的演练。

### <a name="set-up-roles"></a>设置角色

若要使用本演练，除了 Azure Stack 中心操作员帐户外，还需要两个 Azure AD 帐户。 如果没有这两个帐户，则必须创建这些帐户。 这些帐户可以属于任何 Azure AD 用户，并且称为委托的提供商和用户。

| **角色** | **组织权限** |
| --- | --- |
| 委托的提供程序 |用户 |
| 用户 |用户 |

 > [!NOTE]
 > 对于 CSP-经销商，创建此委托提供商要求这些用户位于租户目录（用户 Azure AD）中。 Azure Stack 中心操作员必须[首先](azure-stack-enable-multitenancy.md)将该租户 Azure AD 上，然后按照以下[步骤](azure-stack-csp-howto-register-tenants.md)设置使用情况和计费。

### <a name="identify-the-delegated-provider"></a>标识委派的提供程序

1. 以 Azure Stack 中心操作员身份登录到管理员门户。

1. 创建一个产品/服务，使用户能够成为委派的提供商：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  [创建计划](azure-stack-create-plan.md)。
       此计划应仅包含订阅服务。 本文使用名为**为 planfordelegation**的计划作为示例。

   b.保留“数据库类型”设置，即设置为“共享”。  根据此计划[创建产品/服务](azure-stack-create-offer.md)。 本文使用名为**为 offertodp**的产品/服务作为示例。

   c.  依次选择 "**订阅**"、"**添加**"，然后选择 "**新建租户订阅**"，将委托提供程序作为订阅者添加到此产品/服务。

   ![将委托的提供程序作为订阅者添加到 Azure Stack 中心管理员门户](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > 与所有 Azure Stack 中心提供的产品/服务一样，你可以选择将该产品/服务设为公共的，让用户注册它，或将其保留为私密，让 Azure Stack 中心操作员管理注册。 委托的提供程序通常是一个较小的组。 需要控制谁有权接受该服务，因此在大多数情况下，使此提议保密。

### <a name="azure-stack-hub-operator-creates-the-delegated-offer"></a>Azure Stack 中心操作员创建委托的产品/服务

下一步是创建要委派的计划和产品/服务，并且用户将使用。 最好是将此产品/服务定义为希望用户看到它，因为委托的提供商不能更改其包括的计划和配额。

1. 作为 Azure Stack 中心操作员，根据计划[创建计划](azure-stack-create-plan.md)和[产品/服务](azure-stack-create-offer.md)。 本文使用名为**为 delegatedoffer**的产品/服务作为示例。

   > [!NOTE]
   > 此产品/服务不一定是公开的，但您可以将其公开。 但在大多数情况下，只希望委托提供商有权访问该产品/服务。 按照以下步骤中的说明委托专用产品/服务后，委托的提供商可以访问它。

2. 委托提议。 请参阅**为 delegatedoffer**。 在 "**设置**" 下，选择 "**委托提供程序**"，然后选择 "**添加**"。

3. 从下拉列表中选择委托的提供商的订阅，然后选择 "**委托**"。

   ![在 Azure Stack 中心管理员门户中添加委托提供程序](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>委托提供商自定义产品/服务

以委托的提供商的身份登录到用户门户，然后使用委托的产品/服务作为模板来创建新产品/服务。

1. 依次选择 " **+ 创建资源**" 和 "**租户产品**/服务"，然后选择 "**产品/服务**"。

    ![在 Azure Stack Hub 用户门户中创建新产品/服务](media/azure-stack-delegated-provider/image5.png)

2. 为产品/服务指定名称。 此示例使用**ResellerOffer**。 选择要基于其创建委托的产品/服务，然后选择 "**创建**"。

   ![在 Azure Stack 集线器用户门户中分配名称](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >必须了解，委托的提供商只能选择委托给他们的产品/服务。 它们不能对这些产品/服务进行更改。 只有 Azure Stack 中心操作员可以更改这些产品/服务。 例如，只有操作员可以更改其计划和配额。 委托的提供商不会从基本计划和附加计划构造产品/服务。

3. 委托的提供商可以通过其自己的门户 URL 公开这些产品/服务。 若要公开产品/服务，请选择 "**浏览**"，然后选择 "**产品/服务**"。 选择产品/服务，并选择 "**更改状态**"。

4. 公开委托的产品/服务现在只能通过委托门户来查看。 查找和更改此 URL：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  选择 "**浏览**"，然后选择 "**所有服务**"，然后在 "**常规**" 类别下选择 "**订阅**"。 选择委托的提供商订阅（例如， **DPSubscription**），然后选择 "**属性**"。

    b.保留“数据库类型”设置，即设置为“共享”。  将门户 URL 复制到一个单独的位置，例如 "记事本"。

    ![在 Azure Stack Hub 用户门户中选择委托的提供商订阅](media/azure-stack-delegated-provider/dpportaluri.png)  

   已完成创建委托的产品/服务作为委托提供商。 以委派的提供者身份注销并关闭浏览器窗口。

### <a name="sign-up-for-the-offer"></a>注册产品/服务

1. 在新的浏览器窗口中，请参阅上一步中保存的委派门户 URL。 以用户身份登录到门户。

   >[!NOTE]
   >委托的产品/服务不可见，除非你使用委派的门户。

1. 在仪表板中，选择 "**获取订阅**"。 你将看到，只向用户显示委派的提供者创建的委托的产品/服务。

   ![在 Azure Stack 集线器用户门户中查看和选择产品/服务](media/azure-stack-delegated-provider/image8.png)

委托产品/服务的过程已完成。 现在，用户可以通过获取该产品/服务的订阅来注册此产品/服务。

## <a name="move-subscriptions-between-delegated-providers"></a>在委派的提供商之间移动订阅

如果需要，可以在属于同一目录租户的新的或现有的委托提供程序订阅之间移动订阅。 你可以使用 PowerShell cmdlet [AzsSubscription](/powershell/module/azs.subscriptions.admin)移动它们。

移动订阅在以下情况中非常有用：

* 你将参与委派的提供者角色的新团队成员，并且你想要分配给以前在默认提供程序订阅中创建的此团队成员用户订阅。
* 同一目录租户（Azure AD）中有多个委托提供商订阅，需要在这些订阅之间移动用户订阅。 如果团队成员在团队之间移动，并且必须将其订阅分配给新团队，则可能会发生这种情况。

## <a name="next-steps"></a>后续步骤

* [预配 VM](../user/azure-stack-create-vm-template.md)
