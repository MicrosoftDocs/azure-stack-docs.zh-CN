---
title: 在 Azure Stack Hub 中创建套餐
description: 了解如何在 Azure Stack Hub 中为用户创建套餐。
author: bryanla
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: fe2cb87c666e8c3447eca46bdcb49c71514bccdc
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704482"
---
# <a name="create-an-offer-in-azure-stack-hub"></a>在 Azure Stack Hub 中创建套餐

[套餐](azure-stack-overview.md)是提供者提供给用户购买或订阅的一个或多个计划的组合。 本文介绍如何创建包含[创建的计划](azure-stack-create-plan.md)的套餐。 订阅方可以使用此套餐设置虚拟机 (VM)。

::: moniker range=">=azs-1902"
## <a name="create-an-offer-1902-and-later"></a>创建套餐（1902 和更高版本）

1. 登录到 [Azure Stack Hub 管理员门户](https://adminportal.local.azurestack.external)，依次选择“+ 创建资源”、“套餐 + 计划”、“套餐”。   

   ![在 Azure Stack Hub 中创建套餐](media/azure-stack-create-offer/offers.png)

2. 此时将显示一个选项卡式用户界面，你可以在其中定义套餐名称。 还可以添加现有计划或创建新的基本计划和附加计划。 最重要的是，在决定创建套餐之前，可以先查看其详细信息。

   在“基本信息”选项卡中，输入“显示名称”和“资源名称”，然后在“资源组”下选择“新建”或“使用现有项”。       显示名称是套餐的易记名称。 此易记名称是用户在用户门户中订阅套餐时可看到的有关该套餐的唯一信息。 请使用直观名称，以帮助用户了解该套餐附带了什么功能。 只有管理员可以看到资源名称。 管理员使用此名称将该产品/服务作为 Azure 资源管理器资源处理。 在此选项卡中，还可以选择将此套餐设置为公开或私密。 默认设置是私密。 随时可以[更改套餐的公开或私密状态](#change-the-state-of-an-offer)。

   ![在 Azure Stack Hub 中新建套餐](media/azure-stack-create-offer/new-offer.png)
  
3. 选择“基本计划”  选项卡或单击“下一步:  基本计划 >”按钮。 选择要包含在套餐中的计划。

   ![选择要包含在 Azure Stack Hub 套餐中的计划](media/azure-stack-create-offer/select-plan.png)

4. 此时，可以创建附加计划来修改基本计划，但此操作是可选的。 在下一篇文章 [Azure Stack Hub 附加计划](create-add-on-plan.md)中，你将有机会创建附加计划。

5. 选择“查看 + 创建”选项卡。  查看套餐摘要，确保所有值正确。 在界面中可以逐个展开所选计划中的配额，以查看计划中每个配额的详细信息。 你也可以返回进行任何必要的编辑。

6. 选择“创建”以创建套餐。 

   ![在 Azure Stack Hub 中查看和创建套餐](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>更改套餐的状态

创建套餐后，可以更改其状态。 必须将套餐设为“公共”，用户才能在订阅时获得完整视图。  产品/服务可以是：

- **公共**：对用户可见。
- **专用**：仅对云管理员可见。 起草计划或套餐时，或者当云管理员想要[为用户创建每个订阅](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)时，此设置非常有用。
- **已解除授权**：已对新订阅方关闭。 云管理员可以解除套餐的授权来防止将来订阅，但不影响当前订阅方。

  > [!TIP]  
  > 套餐的更改不会对用户立即可见。 若要查看所做的更改，用户可能需要先注销，然后重新登录到用户门户，这样才能看到新的套餐。

可通过两种方式更改套餐的状态：

1. 在“所有资源”中选择套餐的名称。  在该套餐的“概述”屏幕上，选择“更改状态”。   选择要使用的状态（例如“公开”）。 

   ![更改 Azure Stack Hub 套餐的状态](media/azure-stack-create-offer/change-state.png)

2. 选择“套餐设置”。  选择要使用的状态（例如“公开”），然后选择“保存”。  

   ![Azure Stack Hub 套餐设置](media/azure-stack-create-offer/offer-settings.png)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-offer-1901-and-earlier"></a>创建套餐（1901 和更低版本）

1. 登录到 [Azure Stack Hub 管理员门户](https://adminportal.local.azurestack.external)，依次选择“+ 创建资源”、“租户套餐 + 计划”、“套餐”。   

   ![在 Azure Stack Hub 中创建套餐](media/azure-stack-create-offer/image01.png)
  
2. 在“新建套餐”下，输入“显示名称”和“资源名称”，然后在“资源组”下选择“新建”或“使用现有项”。       显示名称是套餐的易记名称。 此易记名称是用户在订阅套餐时可看到的有关该套餐的唯一信息。 请使用直观名称，以帮助用户了解该套餐附带了什么功能。 只有管理员可以看到资源名称。 管理员使用此名称将该产品/服务作为 Azure 资源管理器资源处理。

   ![在 Azure Stack Hub 中新建套餐](media/azure-stack-create-offer/image01a.png)
  
3. 选择“基本计划”打开“计划”。   选择要包含在套餐中的计划，然后选择“选择”。  若要创建套餐，请选择“创建”。 

   ![选择要包含在 Azure Stack Hub 套餐中的计划](media/azure-stack-create-offer/image02.png)
  
4. 创建套餐后，可以更改其状态。 必须将套餐设为“公共”，用户才能在订阅时获得完整视图。  产品/服务可以是：

   - **公共**：对用户可见。
   - **专用**：仅对云管理员可见。 起草计划或套餐时，或者当云管理员想要[为用户创建每个订阅](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)时，此设置非常有用。
   - **已解除授权**：已对新订阅方关闭。 云管理员可以解除套餐的授权来防止将来订阅，但不影响当前订阅方。

   > [!TIP]  
   > 套餐的更改不会对用户立即可见。 若要查看所做的更改，用户可能需要先注销，然后重新登录到用户门户，这样才能看到新的套餐。

   在套餐的概览屏幕上，选择“辅助功能状态”。  选择要使用的状态（例如“公共”），然后选择“保存”。  

     ![更改 Azure Stack Hub 套餐的状态](media/azure-stack-create-offer/change-stage-1807.png)

     或者，选择“更改状态”  ，然后选择状态。

    ![为 Azure Stack Hub 套餐选择可访问性状态](media/azure-stack-create-offer/change-stage-select-1807.png)

> [!NOTE]
> 还可以使用 PowerShell 来创建默认套餐、计划和配额。 有关详细信息，请参阅 [Azure Stack Hub PowerShell 模块 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0)。
::: moniker-end

## <a name="next-steps"></a>后续步骤

- 若要了解如何修改套餐并为用户提供附加计划，请继续阅读[创建附加计划](create-add-on-plan.md)（可选）
- 否则，请跳至[订阅套餐](azure-stack-subscribe-plan-provision-vm.md)