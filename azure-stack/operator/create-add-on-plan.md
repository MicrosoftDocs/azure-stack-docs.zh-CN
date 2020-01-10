---
title: 本文介绍如何更新 Azure Stack 中心产品和计划 |Microsoft Docs
description: 本文介绍如何查看和修改现有的 Azure Stack 中心产品/服务和计划。
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/05/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 4a215edbd9c7b2feed7e82331ae6a0287e35d54d
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75812478"
---
# <a name="azure-stack-hub-add-on-plans"></a>Azure Stack 中心附加计划

作为 Azure Stack 中心操作员，你可以创建外接程序计划，以在你想要提供其他服务或扩展*计算机*、*存储*或*网络*配额（超出基本计划初始提议）时修改[基本计划](azure-stack-create-plan.md)。 外接程序计划修改基本计划，并是用户可以选择在其订阅中启用的可选扩展。

有时，单个计划中的所有内容都是最佳组合。 其他时候，你可能想要创建基本计划，然后使用外接程序计划提供其他服务。 例如，你可以决定将 IaaS 服务作为基本计划的一部分提供，并将所有 PaaS 服务视为附加计划。

使用外接程序计划的另一个原因是帮助监视资源使用情况。 为此，可以从包含相对较小配额（具体取决于所需服务）的基本计划开始。 然后，当用户达到容量时，他们将收到根据分配的计划使用资源分配的警告。 用户可以从此处选择提供附加资源的附加计划。

> [!NOTE]
> 如果不想使用加载项计划来扩展配额，还可以选择[编辑配额的原始配置](azure-stack-quota-types.md#edit-a-quota)。

附加计划[的创建方式](azure-stack-create-plan.md)与基本计划相同。 这两者之间的差异是在将计划添加到产品/服务时确定的。 它被指定为基本计划或外接程序计划。 将外接程序计划添加到现有产品/服务时，订阅中可能需要长达一小时的时间。

::: moniker range=">=azs-1902"
## <a name="create-an-add-on-plan-1902-and-later"></a>创建外接程序计划（1902及更高版本）

1. 以云管理员身份登录到 Azure Stack 中心管理员门户。
2. 按照[创建新的基本计划](azure-stack-create-plan.md)所用的相同步骤创建新的计划，该服务以前未提供。
3. 在管理员门户中，单击 "**产品/服务**"，然后选择要使用外接程序计划更新的产品/服务。

   ![创建附加计划](media/create-add-on-plan/add-on1.png)

4. 在产品/服务属性的底部，选择 "**外接程序计划**"。 单击“添加”。

    ![创建附加计划](media/create-add-on-plan/add-on2.png)

5. 选择要添加的计划。 在此示例中，计划称为**20-storageaccounts**。 选择计划后，单击 "**选择**" 将计划添加到产品/服务。 你应会收到一条通知，指出已成功将计划添加到产品/服务。

    ![创建附加计划](media/create-add-on-plan/add-on3.png)

6. 查看产品/服务附带的附加计划列表，以验证是否列出了新的外接程序计划。

    [![创建附加计划](media/create-add-on-plan/add-on4.png "创建附加计划")](media/create-add-on-plan/add-on4lg.png#lightbox)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-add-on-plan-1901-and-earlier"></a>创建外接程序计划（1901及更早版本）

1. 以云管理员身份登录到 Azure Stack 中心管理员门户。
2. 按照[创建新的基本计划](azure-stack-create-plan.md)所用的相同步骤创建新的计划，该服务以前未提供。 在此示例中，Key Vault （**KeyVault**）服务将包含在新计划中。
3. 在管理员门户中，单击 "**产品/服务**"，然后选择要使用外接程序计划更新的产品/服务。

   ![创建附加计划](media/create-add-on-plan/1.PNG)

4. 滚动到 "产品/服务属性" 的底部，选择 "**外接程序计划**"。 单击“添加”。

    ![创建附加计划](media/create-add-on-plan/2.PNG)

5. 选择要添加的计划。 在此示例中，计划称为**密钥保管库计划**。 选择计划后，单击 "**选择**" 将计划添加到产品/服务。 你应会收到一条通知，指出已成功将计划添加到产品/服务。

    ![创建附加计划](media/create-add-on-plan/3.PNG)

6. 查看产品/服务附带的附加计划列表，以验证是否列出了新的外接程序计划。

    ![创建附加计划](media/create-add-on-plan/4.PNG)
::: moniker-end

## <a name="next-steps"></a>后续步骤

* [创建产品/服务](azure-stack-create-offer.md)
