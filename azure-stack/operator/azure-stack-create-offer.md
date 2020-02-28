---
title: 在 Azure Stack 中心创建产品/服务
description: 了解如何在 Azure Stack Hub 中为用户创建产品/服务。
author: bryanla
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: fe2cb87c666e8c3447eca46bdcb49c71514bccdc
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704482"
---
# <a name="create-an-offer-in-azure-stack-hub"></a>在 Azure Stack 中心创建产品/服务

[套餐](azure-stack-overview.md)是提供者提供给用户购买或订阅的一个或多个计划。 本文介绍如何创建包含[所创建计划](azure-stack-create-plan.md)的套餐。 此套餐允许订阅者设置虚拟机 (VM)。

::: moniker range=">=azs-1902"
## <a name="create-an-offer-1902-and-later"></a>创建产品/服务（1902及更高版本）

1. 登录到[Azure Stack 中心管理员门户](https://adminportal.local.azurestack.external)，选择 " **+ 创建资源**"，然后选择 " **+ 计划**"，然后**提供**。

   ![在 Azure Stack 中心创建产品/服务](media/azure-stack-create-offer/offers.png)

2. 此时将显示一个选项卡式用户界面，您可以在其中定义产品/服务名称。 你还可以添加现有的或创建新的基础计划和外接程序计划。 最重要的是，你可以在决定创建产品/服务之前查看其详细信息。

   在 "**基本**信息" 选项卡中，输入**显示名称**和**资源名称**，然后在 "**资源组**" 下，选择 "**新建**" 或 "**使用现有**"。 显示名称是产品/服务的友好名称。 此友好名称是用户在用户门户中订阅产品/服务时看到的有关该产品/服务的唯一信息。 使用直观的名称，帮助用户了解产品/服务附带的内容。 只有管理员才能看到资源名称。 管理员使用该名称作为 Azure 资源管理器资源来处理该产品/服务。 在此选项卡中，你还可以选择将此产品/服务设为公共，或将其保留为专用。 默认设置为 "专用"。 随时可以[更改产品/服务的公共或私有状态](#change-the-state-of-an-offer)。

   ![Azure Stack 中心中的新产品/服务](media/azure-stack-create-offer/new-offer.png)
  
3. 选择 "**基本计划**" 选项卡，或单击 "**下一步：基本计划" >** 按钮。 选择要在产品/服务中包括的计划。

   ![选择要纳入 Azure Stack 中心产品/服务的计划](media/azure-stack-create-offer/select-plan.png)

4. 此时，您可以创建一个外接程序计划来修改基本计划，但这是可选的。 你可以在下一篇文章[Azure Stack 中心附加](create-add-on-plan.md)计划中创建外接程序计划。

5. 选择 "**查看**" 和 "创建" 选项卡。查看产品/服务摘要，以确保所有值都正确。 使用接口，可以一次展开所选计划中的配额，以查看计划中每个配额的详细信息。 你还可以返回进行任何所需的编辑。

6. 选择 "**创建**" 以创建产品/服务。

   ![在 Azure Stack 中心查看和创建产品/服务](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>更改产品/服务的状态

创建产品/服务后，可以更改其状态。 必须将产品/服务设为**公共**的，以便用户在订阅时获得完整视图。 产品/服务可以：

- **Public**：对用户可见。
- **专用**：仅对云管理员可见。 当起草计划或产品/服务时，或者云管理员想要为[用户创建每个订阅](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)时，此设置很有用。
- 已**取消：已**关闭新的订阅服务器。 云管理员可以解除服务的授权，以防止将来订阅，但不影响当前订阅者。

  > [!TIP]  
  > 对产品/服务的更改不会对用户立即可见。 若要查看所做的更改，用户可能需要注销并重新登录到用户门户，才能看到新的产品/服务。

有两种方法可以更改产品/服务的状态：

1. 在 "**所有资源**" 中，选择产品/服务的名称。 在产品/服务的 "**概述**" 屏幕上，选择 "**更改状态**"。 选择要使用的状态（例如，"**公用**"）。

   ![更改 Azure Stack 中心产品/服务的状态](media/azure-stack-create-offer/change-state.png)

2. 选择 "**产品/服务设置**"。 选择要使用的状态（例如 "**公共**"），然后选择 "**保存**"。

   ![Azure Stack 中心产品/服务设置](media/azure-stack-create-offer/offer-settings.png)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-offer-1901-and-earlier"></a>创建产品/服务（1901及更早版本）

1. 登录到[Azure Stack 中心管理员门户](https://adminportal.local.azurestack.external)，选择 " **+ 创建资源**"，然后选择 "**租户产品 + 计划**"，然后**提供**。

   ![在 Azure Stack 中心创建产品/服务](media/azure-stack-create-offer/image01.png)
  
2. 在 "**新建产品/服务**" 下，输入**显示名称**和**资源名称**，然后在 "**资源组**" 下，选择 "**新建**" 或 "**使用现有**"。 显示名称是产品/服务的友好名称。 此友好名称是用户订阅产品/服务时可看到的有关该产品/服务的唯一信息。 使用直观的名称，帮助用户了解产品/服务附带的内容。 只有管理员才能看到资源名称。 管理员使用该名称作为 Azure 资源管理器资源来处理该产品/服务。

   ![Azure Stack 中心中的新产品/服务](media/azure-stack-create-offer/image01a.png)
  
3. 选择 "**基本计划**" 以打开**计划**。 选择要包含在产品/服务中的计划，然后选择 "**选择**"。 若要创建产品/服务，请选择 "**创建**"。

   ![选择要纳入 Azure Stack 中心产品/服务的计划](media/azure-stack-create-offer/image02.png)
  
4. 创建产品/服务后，可以更改其状态。 必须将产品/服务设为**公共**的，以便用户在订阅时获得完整视图。 产品/服务可以：

   - **Public**：对用户可见。
   - **专用**：仅对云管理员可见。 当起草计划或产品/服务时，或者云管理员想要为[用户创建每个订阅](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)时，此设置很有用。
   - 已**取消：已**关闭新的订阅服务器。 云管理员可以解除服务的授权，以防止将来订阅，但不影响当前订阅者。

   > [!TIP]  
   > 对产品/服务的更改不会对用户立即可见。 若要查看所做的更改，用户可能需要注销并重新登录到用户门户，才能看到新的产品/服务。

   在产品/服务的 "概述" 屏幕上，选择 "**辅助功能状态**"。 选择要使用的状态（例如 "**公共**"），然后选择 "**保存**"。

     ![更改 Azure Stack 中心产品/服务的状态](media/azure-stack-create-offer/change-stage-1807.png)

     作为替代方法，请选择 "**更改状态**"，然后选择状态。

    ![为 Azure Stack 中心产品/服务选择辅助功能状态](media/azure-stack-create-offer/change-stage-select-1807.png)

> [!NOTE]
> 你还可以使用 PowerShell 来创建默认产品/服务、计划和配额。 有关详细信息，请参阅[Azure Stack Hub PowerShell 模块 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0)。
::: moniker-end

## <a name="next-steps"></a>后续步骤

- 若要了解如何修改产品/服务并向用户提供附加计划，请继续[创建附加计划](create-add-on-plan.md)（可选）
- 否则，跳转到[订阅产品/服务](azure-stack-subscribe-plan-provision-vm.md)