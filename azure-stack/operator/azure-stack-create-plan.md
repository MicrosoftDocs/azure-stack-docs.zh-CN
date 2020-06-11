---
title: 在 Azure Stack Hub 中创建计划
description: 了解如何在 Azure Stack Hub 中创建可让订阅者预配虚拟机的计划。
author: bryanla
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: c762f4c8c75fbfd6755c25877c52fbb68c88f7ca
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84631124"
---
# <a name="create-a-plan-in-azure-stack-hub"></a>在 Azure Stack Hub 中创建计划

[Azure Stack Hub 计划](azure-stack-overview.md)是一个或多个服务及其配额的分组。 作为提供商，可以创建提供给用户的计划。 而用户可以订阅你的套餐，以使用套餐中包含的计划、服务和配额。 此示例演示如何创建一个包括计算、网络和存储资源提供程序的计划。 订阅方使用此计划可以预配虚拟机。

::: moniker range=">=azs-1902"
## <a name="create-a-plan-1902-and-later"></a>创建计划（1902 和更高版本）

1. 登录到 Azure Stack 中心管理员门户 `https://adminportal.local.azurestack.external` 。

2. 若要创建用户可以订阅的计划和套餐，请依次选择“创建资源”、“套餐 + 计划”、“计划”。   
  
   ![在 Azure Stack Hub 管理员门户中选择计划](media/azure-stack-create-plan/select-plan.png)

3. 此时会显示标签式用户界面，在其中可为选定的每个服务指定计划名称、添加服务以及定义配额。 最重要的是，在决定创建套餐之前，可以先查看其详细信息。

   在“新建计划”窗口中的“基本信息”选项卡下，输入**显示名称**和**资源名称**。   显示名称是计划的易记名称，操作员可以看到它。 在管理员门户中，计划详细信息仅向操作员显示。

   ![在 Azure Stack Hub 中指定新计划的详细信息](media/azure-stack-create-plan/plan-name.png)

4. 创建一个新**资源组**或选择一个现有资源组，作为计划的容器。

   ![在 Azure Stack Hub 中为新计划指定资源组](media/azure-stack-create-plan/resource-group.png)

5. 选择“服务”  选项卡，或单击“下一步:  服务 >”按钮，然后选择与 **Microsoft.Compute**、**Microsoft.Network**和 **Microsoft.Storage** 对应的复选框。
  
   ![在 Azure Stack Hub 中为新计划选择服务](media/azure-stack-create-plan/services.png)

6. 选择“配额”  选项卡，或单击“下一步:  配额 >”按钮。 在“Microsoft.Storage”的旁边，从下拉框中选择默认配额，或选择“新建”以创建自定义的配额。  
  
   ![在 Azure Stack Hub 中为新计划指定配额](media/azure-stack-create-plan/quotas.png)

7. 如果新建配额，请输入配额的**名称**，然后指定配额值。 选择“确定”以创建配额。 

   ![在 Azure Stack Hub 中为新计划创建新配额](media/azure-stack-create-plan/new-quota.png)

8. 重复步骤 6 到 7，为“Microsoft.Network”和“Microsoft.Compute”创建并分配配额。   为所有三个服务分配配额后，这些服务将如以下示例所示。

   ![在 Azure Stack Hub 中完成新计划的配额分配](media/azure-stack-create-plan/all-quotas-assigned.png)

9. 选择“查看 + 创建”以查看计划。  查看所有值和配额以确保正确。 在界面中可以逐个展开所选计划中的配额，以查看计划中每个配额的详细信息。 你也可以返回进行任何必要的编辑。

   ![在 Azure Stack Hub 中创建计划](media/azure-stack-create-plan/create.png)

10. 准备就绪后，选择“创建”以创建计划。 

11. 若要查看新计划，请在左侧单击“所有服务”  ，选择“计划”  ，然后搜索计划并选择其名称。 如果资源列表很长，可使用“搜索”  来通过名称定位你的计划。
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-a-plan-1901-and-earlier"></a>创建计划（1901 和更低版本）

1. 登录到 Azure Stack 中心管理员门户 `https://adminportal.local.azurestack.external` 。

2. 若要创建用户可以订阅的计划和套餐，请依次选择“新建”、“套餐 + 计划”、“计划”。   
  
   ![在 Azure Stack Hub 管理员门户中选择计划](media/azure-stack-create-plan/select-plan1901.png)

3. 在“新建计划”下，输入**显示名称**和**资源名称**。  显示名称是计划的易记名称，用户可以看到它。 只有管理员可以看到资源名称，管理员使用此名称将计划作为 Azure 资源管理器资源进行处理。

   ![在 Azure Stack Hub 中指定新计划的详细信息](media/azure-stack-create-plan/plan-name1901.png)

4. 创建一个新**资源组**或选择一个现有资源组，作为计划的容器。

   ![在 Azure Stack Hub 中为新计划指定资源组](media/azure-stack-create-plan/resource-group1901.png)

5. 选择“服务”  ，然后选择与“Microsoft.Compute”  、“Microsoft.Network”  和“Microsoft.Storage”  对应的复选框。 接下来，选择“选择”  以保存配置。 将鼠标指针悬停在每个选项上时会显示复选框。
  
   ![在 Azure Stack Hub 中为新计划选择服务](media/azure-stack-create-plan/services1901.png)

6. 选择“配额”  、“Microsoft.Storage (本地)”  ，然后选择默认配额，或选择“新建配额”  来创建自定义的配额。
  
   ![在 Azure Stack Hub 中为新计划指定配额](media/azure-stack-create-plan/quotas1901.png)

7. 如果要新建配额，请输入配额的**名称** > 指定配额值 > 选择“确定”  。 “创建配额”  对话框关闭。

   ![在 Azure Stack Hub 中为新计划创建新配额](media/azure-stack-create-plan/new-quota1901.png)

   然后选择创建的新配额。 选择配额会分配它并关闭选择对话框。
  
   ![在 Azure Stack Hub 中为新计划分配配额](media/azure-stack-create-plan/assign-quota1901.png)

8. 重复步骤 6 和 7 来为“Microsoft.Network (本地)”  和“Microsoft.Compute (本地)”  创建并分配配额。 为所有三个服务分配配额后，这些服务将如以下示例所示。

   ![在 Azure Stack Hub 中完成新计划的配额分配](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. 在“配额”  下选择“确定”  ，然后在“新建计划”  下选择“创建”  以创建计划。

    ![在 Azure Stack Hub 中创建计划](media/azure-stack-create-plan/create1901.png)

10. 若要查看新计划，请选择“所有资源”  ，然后搜索该计划并选择其名称。 如果资源列表很长，可使用“搜索”  来通过名称定位你的计划。

    ![在 Azure Stack Hub 中查看新计划](media/azure-stack-create-plan/plan-overview1901.png)
::: moniker-end

## <a name="next-steps"></a>后续步骤

* [创建产品/服务](azure-stack-create-offer.md)
