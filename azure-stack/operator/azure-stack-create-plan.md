---
title: 在 Azure Stack 中创建计划 | Microsoft Docs
description: 作为云管理员，创建一个让订阅方预配虚拟机的计划。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: b120346d489f676919cb05863f81db9bfb102634
ms.sourcegitcommit: e51cdc84a09250e8fa701bb2cb09de38d7de2c07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2019
ms.locfileid: "66836990"
---
# <a name="create-a-plan-in-azure-stack"></a>在 Azure Stack 中创建计划

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

[Azure Stack 计划](azure-stack-overview.md)是分组的一个或多个服务和其配额。 作为提供商，可以创建提供给用户的计划。 而用户可以订阅你的套餐，以使用套餐中包含的计划、服务和配额。 此示例演示如何创建一个包括计算、网络和存储资源提供程序的计划。 订阅方使用此计划可以预配虚拟机。

## <a name="create-a-plan-1902-and-later"></a>创建计划（1902 和更高版本）

1. 登录到 [Azure Stack 管理员门户](https://adminportal.local.azurestack.external)。

2. 若要创建用户可以订阅的计划和套餐，请依次选择“创建资源”、“套餐 + 计划”、“计划”。   
  
   ![选择计划](media/azure-stack-create-plan/select-plan.png)

3. 此时会显示标签式用户界面，在其中可为选定的每个服务指定计划名称、添加服务以及定义配额。 最重要的是，在决定创建套餐之前，可以先查看其详细信息。

   在“新建计划”窗口中的“基本信息”选项卡下，输入**显示名称**和**资源名称**。   显示名称是计划的易记名称，操作员可以看到它。 请注意，在管理员门户中，计划详细信息仅向操作员显示。

   ![指定详细信息](media/azure-stack-create-plan/plan-name.png)

4. 创建一个新**资源组**或选择一个现有资源组，作为计划的容器。

   ![指定资源组](media/azure-stack-create-plan/resource-group.png)

5. 选择**Services**选项卡上，或单击**下一步：服务 >** 按钮，并选择复选框**Microsoft.Compute**， **Microsoft.Network**，并且**Microsoft.Storage**。
  
   ![选择服务](media/azure-stack-create-plan/services.png)

6. 选择**配额**选项卡上，或单击**下一步：配额 >** 按钮。 在“Microsoft.Storage”的旁边，从下拉框中选择默认配额，或选择“新建”以创建自定义的配额。  
  
   ![配额](media/azure-stack-create-plan/quotas.png)

7. 如果新建配额，请输入配额的**名称**，然后指定配额值。 选择“确定”以创建配额。 

   ![新建配额](media/azure-stack-create-plan/new-quota.png)

8. 重复步骤 6 到 7，为“Microsoft.Network”和“Microsoft.Compute”创建并分配配额。   为所有三个服务分配配额后，这些服务将如以下示例所示。

   ![完成配额分配](media/azure-stack-create-plan/all-quotas-assigned.png)

9. 选择“查看 + 创建”以查看计划。  查看所有值和配额以确保正确。 注意每个服务/配额对左侧的展开箭头。 使用一个新的功能可以逐个展开所选计划中的配额，以查看计划中每个配额的详细信息，并返回进行任何必要的编辑。

   ![创建计划](media/azure-stack-create-plan/create.png)

10. 准备就绪后，选择“创建”以创建计划。 

11. 若要查看新计划，在左侧单击**所有服务**，选择**计划**，然后搜索该计划，并选择其名称。 如果资源列表很长，可使用“搜索”  来通过名称定位你的计划。

## <a name="create-a-plan-1901-and-earlier"></a>创建计划（1901 和更低版本）

1. 登录到 [Azure Stack 管理员门户](https://adminportal.local.azurestack.external)。

2. 若要创建计划和用户可以订阅的产品/服务，请选择 **+ 新建**，然后**产品/服务 + 计划**，然后**计划**。
  
   ![选择计划](media/azure-stack-create-plan/select-plan1901.png)

3. 在“新建计划”下，输入**显示名称**和**资源名称**。  显示名称是计划的易记名称，用户可以看到它。 只有管理员可以看到资源名称，管理员使用此名称将计划作为 Azure 资源管理器资源进行处理。

   ![指定详细信息](media/azure-stack-create-plan/plan-name1901.png)

4. 创建一个新**资源组**或选择一个现有资源组，作为计划的容器。

   ![指定资源组](media/azure-stack-create-plan/resource-group1901.png)

5. 选择“服务”  ，然后选择与“Microsoft.Compute”  、“Microsoft.Network”  和“Microsoft.Storage”  对应的复选框。 接下来，选择“选择”  以保存配置。 将鼠标指针悬停在每个选项上时会显示复选框。
  
   ![选择服务](media/azure-stack-create-plan/services1901.png)

6. 选择“配额”  、“Microsoft.Storage (本地)”  ，然后选择默认配额，或选择“新建配额”  来创建自定义的配额。
  
   ![配额](media/azure-stack-create-plan/quotas1901.png)

7. 如果要新建配额，请输入配额的**名称** > 指定配额值 > 选择“确定”  。 “创建配额”  对话框关闭。

   ![新建配额](media/azure-stack-create-plan/new-quota1901.png)

   然后选择创建的新配额。 选择配额会分配它并关闭选择对话框。
  
   ![分配配额](media/azure-stack-create-plan/assign-quota1901.png)

8. 重复步骤 6 和 7 来为“Microsoft.Network (本地)”  和“Microsoft.Compute (本地)”  创建并分配配额。 为所有三个服务分配配额后，这些服务将如以下示例所示。

   ![完成配额分配](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. 在“配额”  下选择“确定”  ，然后在“新建计划”  下选择“创建”  以创建计划。

    ![创建计划](media/azure-stack-create-plan/create1901.png)

10. 若要查看新计划，请选择“所有资源”  ，然后搜索该计划并选择其名称。 如果资源列表很长，可使用“搜索”  来通过名称定位你的计划。

    ![检查计划](media/azure-stack-create-plan/plan-overview1901.png)

## <a name="next-steps"></a>后续步骤

* [创建产品/服务](azure-stack-create-offer.md)
