---
title: 在 Azure Stack 中心创建计划
description: 了解如何在 Azure Stack Hub 中创建允许订阅者预配虚拟机的计划。
author: bryanla
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 20ee0c2b59f3a8038afc443f9b3c1b12d606d95d
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700827"
---
# <a name="create-a-plan-in-azure-stack-hub"></a>在 Azure Stack 中心创建计划

[Azure Stack 中心计划](azure-stack-overview.md)是一项或多项服务及其配额的分组。 作为提供商，可以创建提供给用户的计划。 然后，用户订阅你的产品/服务，以使用其所包括的计划、服务和配额。 此示例演示如何创建包含计算、网络和存储资源提供程序的计划。 此计划使订阅者能够设置虚拟机。

::: moniker range=">=azs-1902"
## <a name="create-a-plan-1902-and-later"></a>创建计划（1902及更高版本）

1. 登录到[Azure Stack 中心管理员门户](https://adminportal.local.azurestack.external)。

2. 若要创建用户可以订阅的计划和产品/服务，请选择 " **+ 创建资源**"，然后选择 " **+ 计划**"，然后选择 "**计划**"。
  
   ![在 Azure Stack 中心管理员门户中选择计划](media/azure-stack-create-plan/select-plan.png)

3. 此时将显示一个选项卡式用户界面，使您可以指定计划名称、添加服务并定义每个所选服务的配额。 最重要的是，你可以在决定创建产品/服务之前查看其详细信息。

   在 "**新建计划**" 窗口的 "**基本**信息" 选项卡下，输入**显示名称**和**资源名称**。 显示名称是操作员可以看到的计划的友好名称。 在管理员门户中，计划详细信息仅对操作员可见。

   ![在 Azure Stack 集线器中指定新计划的详细信息](media/azure-stack-create-plan/plan-name.png)

4. 创建新的**资源组**，或选择现有的资源组作为计划的容器。

   ![在 Azure Stack 中心为新计划指定资源组](media/azure-stack-create-plan/resource-group.png)

5. 选择 "**服务**" 选项卡，或者单击 "**下一步：服务 >** " 按钮，然后选中 " **microsoft**"、" **microsoft**"、"microsoft **" 和 "microsoft"** 复选框。
  
   ![在 Azure Stack Hub 中选择新计划的服务](media/azure-stack-create-plan/services.png)

6. 选择 "**配额**" 选项卡，或单击 "**下一步：配额 >** " 按钮。 在 " **Microsoft 存储**" 旁边，从下拉框中选择 "默认配额"，或选择 "**新建**" 以创建自定义配额。
  
   ![在 Azure Stack 集线器中指定新计划的配额](media/azure-stack-create-plan/quotas.png)

7. 如果要创建新的配额，请输入配额**名称**，然后指定配额值。 选择 **"确定"** 以创建配额。

   ![在 Azure Stack 中心为新计划创建新配额](media/azure-stack-create-plan/new-quota.png)

8. 重复步骤6和步骤7，为**Microsoft**和 "**计算**" 创建和分配配额。 当所有三个服务都分配了配额后，它们将类似于下一个示例。

   ![在 Azure Stack 中心内完成新计划的配额分配](media/azure-stack-create-plan/all-quotas-assigned.png)

9. 选择 "查看" 和 "**创建**" 以查看计划。 查看所有值和配额，确保它们正确无误。 使用接口，可以一次展开所选计划中的配额，以查看计划中每个配额的详细信息。 你还可以返回进行任何所需的编辑。

   ![在 Azure Stack 中心创建计划](media/azure-stack-create-plan/create.png)

10. 准备就绪后，选择 "**创建**" 以创建计划。

11. 若要查看新计划，请在左侧单击 "**所有服务**"，选择 "**计划**"，然后搜索计划并选择其名称。 如果资源的列表很长，请使用 "**搜索**" 按名称查找计划。
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-a-plan-1901-and-earlier"></a>创建计划（1901及更早版本）

1. 登录到[Azure Stack 中心管理员门户](https://adminportal.local.azurestack.external)。

2. 若要创建用户可以订阅的计划和产品/服务，请选择 " **+ 新建**"，然后选择 "+**计划**"，然后选择 "**计划**"。
  
   ![在 Azure Stack 中心管理员门户中选择计划](media/azure-stack-create-plan/select-plan1901.png)

3. 在 "**新建计划**" 下，输入**显示名称**和**资源名称**。 显示名称是用户可看到的计划的友好名称。 只有管理员才能看到资源名称，管理员使用该资源名称作为 Azure 资源管理器资源处理计划。

   ![在 Azure Stack 集线器中指定新计划的详细信息](media/azure-stack-create-plan/plan-name1901.png)

4. 创建新的**资源组**，或选择现有的资源组作为计划的容器。

   ![在 Azure Stack 中心为新计划指定资源组](media/azure-stack-create-plan/resource-group1901.png)

5. 依次选择 "**服务**"、" **microsoft**"、"microsoft"、" **microsoft" 和** **"microsoft" 复选框。** 接下来，选择 "**选择**" 以保存配置。 当鼠标悬停在每个选项上时，将显示复选框。
  
   ![在 Azure Stack Hub 中选择新计划的服务](media/azure-stack-create-plan/services1901.png)

6. 选择 "**配额**"、" **Microsoft （本地）** "，然后选择默认配额，或选择 "**创建新配额**" 来创建自定义配额。
  
   ![在 Azure Stack 集线器中指定新计划的配额](media/azure-stack-create-plan/quotas1901.png)

7. 如果要创建新的配额，请为配额输入**名称**> 指定配额值 > 选择 **"确定"** 。 "**创建配额**" 对话框将关闭。

   ![在 Azure Stack 中心为新计划创建新配额](media/azure-stack-create-plan/new-quota1901.png)

   然后选择已创建的新配额。 选择配额会将其分配，并关闭选择对话框。
  
   ![在 Azure Stack 中心分配新计划的配额](media/azure-stack-create-plan/assign-quota1901.png)

8. 重复步骤6和7，为 " **microsoft （本地）** " 和 " **microsoft**（本地）" 创建和分配配额。 当所有三个服务都分配了配额后，它们将类似于下一个示例。

   ![在 Azure Stack 中心内完成新计划的配额分配](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. 在 "**配额**" 下，选择 **"确定"** ，然后在 "**新建计划**" 下，选择 "**创建**" 以创建计划。

    ![在 Azure Stack 中心创建计划](media/azure-stack-create-plan/create1901.png)

10. 若要查看新计划，请选择 "**所有资源**"，然后搜索计划并选择其名称。 如果资源的列表很长，请使用 "**搜索**" 按名称查找计划。

    ![查看 Azure Stack 中心中的新计划](media/azure-stack-create-plan/plan-overview1901.png)
::: moniker-end

## <a name="next-steps"></a>后续步骤

* [创建产品/服务](azure-stack-create-offer.md)
