---
title: 在 Azure Stack 中创建套餐 | Microsoft Docs
description: 作为云管理员，了解如何在 Azure Stack 中为用户创建套餐。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: a2bb84cef5c24cd84653092a34dea7f0e20b624e
ms.sourcegitcommit: e51cdc84a09250e8fa701bb2cb09de38d7de2c07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66836931"
---
# <a name="create-an-offer-in-azure-stack"></a>在 Azure Stack 中创建套餐

[套餐](azure-stack-overview.md)是提供者提供给用户购买或订阅的一个或多个计划的组合。 本文介绍如何创建包含[创建的计划](azure-stack-create-plan.md)的套餐。 订阅方可以使用此套餐设置虚拟机 (VM)。

## <a name="create-an-offer-1902-and-later"></a>创建套餐（1902 和更高版本）

1. 登录到 [Azure Stack 管理员门户](https://adminportal.local.azurestack.external)，依次选择“+ 创建资源”、“套餐 + 计划”、“套餐”。   

   ![创建产品](media/azure-stack-create-offer/offers.png)

2. 此时会显示标签式用户界面，可在其中定义套餐名称、添加现有的或创建新的基本计划和附加计划。 最重要的是，在决定创建套餐之前，可以先查看其详细信息。

   在中**基础知识**选项卡上，输入**显示名称**和一个**资源名称**，然后在**资源组**，选择**创建新**或**使用现有**。 显示名称是套餐的易记名称。 此易记名称是用户在用户门户中订阅套餐时可看到的有关该套餐的唯一信息。 请使用直观名称，以帮助用户了解该套餐附带了什么功能。 只有管理员可以看到资源名称。 管理员使用此名称将该产品/服务作为 Azure 资源管理器资源处理。 在此选项卡中，还可以选择公开此套餐，或者将其保密（默认设置）。 以后还可以[更改套餐的公开或私密状态](#change-the-state-of-an-offer)。

   ![新建产品/服务](media/azure-stack-create-offer/new-offer.png)
  
3. 选择**基本计划**选项卡上，或单击**下一步：基本计划 >** 按钮。 选择要包含在套餐中的计划。

   ![选择计划](media/azure-stack-create-offer/select-plan.png)

4. 此时，可以创建附加计划来修改基本计划，但此操作是可选的。 在下一篇文章 [Azure Stack 附加计划](create-add-on-plan.md)中，我们将创建附加计划。

5. 选择“查看 + 创建”选项卡。  查看套餐摘要，确保所有值正确。 在界面中可以逐个展开所选计划中的配额，以查看计划中每个配额的详细信息，并返回进行任何必要的编辑。

6. 选择“创建”以创建套餐。 

   ![查看并创建](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>更改套餐的状态

创建套餐后，可以更改其状态。 必须将套餐设为“公共”，用户才能在订阅时获得完整视图。  产品/服务可以是：

- **公共**：对用户可见。
- **专用**：仅对云管理员可见。 起草计划或套餐时，或者当云管理员想要[为用户创建每个订阅](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)时，此设置非常有用。
- **已解除授权**：已对新订阅方关闭。 云管理员可以解除套餐的授权来防止将来订阅，但不影响当前订阅方。

  > [!TIP]  
  > 产品/服务的更改不会对用户立即可见。 若要查看所做的更改，用户可能需要先注销，然后重新登录到用户门户，这样才能看到新的套餐。

可通过两种方式更改套餐的状态：

1. 在“所有资源”中选择套餐的名称。  在该套餐的“概述”屏幕上，选择“更改状态”。   选择要使用的状态（例如“公开”）。 

   ![选择状态](media/azure-stack-create-offer/change-state.png)

2. 选择**产品/服务设置**。 选择要使用的状态（例如“公开”），然后选择“保存”。  

   ![选择辅助功能状态](media/azure-stack-create-offer/offer-settings.png)

## <a name="create-an-offer-1901-and-earlier"></a>创建套餐（1901 和更低版本）

1. 登录到 [Azure Stack 管理员门户](https://adminportal.local.azurestack.external)，依次选择“+ 创建资源”、“租户套餐 + 计划”、“套餐”。   

   ![创建产品](media/azure-stack-create-offer/image01.png)
  
2. 在“新建套餐”下，输入“显示名称”和“资源名称”，然后在“资源组”下选择“新建”或“使用现有项”。       显示名称是套餐的易记名称。 此易记名称是用户在订阅套餐时可看到的有关该套餐的唯一信息。 请使用直观名称，以帮助用户了解该套餐附带了什么功能。 只有管理员可以看到资源名称。 管理员使用此名称将该产品/服务作为 Azure 资源管理器资源处理。

   ![新建产品/服务](media/azure-stack-create-offer/image01a.png)
  
3. 选择“基本计划”打开“计划”。   选择要包含在套餐中的计划，然后选择“选择”。  若要创建套餐，请选择“创建”。 

   ![选择计划](media/azure-stack-create-offer/image02.png)
  
4. 创建套餐后，可以更改其状态。 必须将套餐设为“公共”，用户才能在订阅时获得完整视图。  产品/服务可以是：

   - **公共**：对用户可见。
   - **专用**：仅对云管理员可见。 起草计划或套餐时，或者当云管理员想要[为用户创建每个订阅](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)时，此设置非常有用。
   - **已解除授权**：已对新订阅方关闭。 云管理员可以解除套餐的授权来防止将来订阅，但不影响当前订阅方。

   > [!TIP]  
   > 产品/服务的更改不会对用户立即可见。 若要查看所做的更改，用户可能需要先注销，然后重新登录到用户门户，这样才能看到新的套餐。

   在套餐的概览屏幕上，选择“辅助功能状态”。  选择要使用的状态（例如“公共”），然后选择“保存”。  

     ![选择状态](media/azure-stack-create-offer/change-stage-1807.png)

     或者，选择“更改状态”  ，然后选择状态。

    ![选择辅助功能状态](media/azure-stack-create-offer/change-stage-select-1807.png)

> [!NOTE]
> 还可以使用 PowerShell 来创建默认套餐、计划和配额。 有关详细信息，请参阅 [Azure Stack PowerShell 模块 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0)。

## <a name="next-steps"></a>后续步骤

- [创建订阅](azure-stack-subscribe-plan-provision-vm.md)
- [预配虚拟机](../user/azure-stack-create-vm-template.md)
