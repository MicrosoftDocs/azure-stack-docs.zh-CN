---
title: 使用门户创建事件中心群集
description: 了解如何使用 Azure Stack Hub 用户门户创建事件中心群集。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: quickstart
ms.date: 01/22/2020
ms.reviewer: bryanla
ms.lastreviewed: 08/15/2020
ms.openlocfilehash: 7a5c801f85f7dc4ada1ec155078ba02d9feb26de
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97343413"
---
# <a name="quickstart-create-an-event-hubs-cluster-using-the-azure-stack-hub-portal"></a>快速入门：使用 Azure Stack Hub 门户创建事件中心群集

本快速入门介绍如何使用 Azure Stack Hub 用户门户创建事件中心群集。 

事件中心群集提供单租户部署来满足苛刻的流式处理需求。 事件中心群集每秒能够引入数百万个事件，且提供有保障的容量和亚秒级的延迟。 事件中心群集包含所有常用功能，这些功能与 Azure 事件中心版本提供的功能非常接近。

## <a name="prerequisites"></a>先决条件

完成本快速入门之前，必须确保你的订阅中可以使用事件中心服务。 如果不可用，请联系管理员[在 Azure Stack Hub 资源提供程序上安装事件中心](../operator/event-hubs-rp-overview.md)。 安装步骤还包括创建包含事件中心服务的产品/服务。 

产品/服务可用后，管理员可以创建或更新订阅以包含事件中心。 或者，你可以[订阅新产品/服务并创建自己的订阅](azure-stack-subscribe-services.md)。

必须在资源组作用域或更高级别为你的帐户分配至少 " [参与者" 权限](/azure/role-based-access-control/rbac-and-directory-admin-roles#azure-roles) 才能创建新群集。

## <a name="overview"></a>概述

事件中心群集是通过指定容量单位 (CU) 创建的。 CU 是预先分配的 CPU、存储和内存资源量。 事件中心群集按 CPU/小时计费。 在群集创建过程中选择 CU 数量（群集大小）时，会显示群集使用的核心 (CPU) 数。 有关群集资源使用情况的更多详细信息，请参阅[如何为 Azure Stack Hub 上的事件中心进行容量规划](../operator/event-hubs-rp-capacity-planning.md)。 

本快速入门介绍如何使用 Azure Stack Hub 用户门户来执行以下操作：
- 创建单 CU 事件中心群集。
- 在群集中创建命名空间。
- 在命名空间中创建事件中心。
- 删除事件中心群集

## <a name="create-an-event-hubs-cluster"></a>创建事件中心群集

事件中心群集提供唯一的范围容器，可以在该容器中创建一个或多个命名空间。 完成以下步骤以创建事件中心群集： 

1. 登录到 Azure Stack Hub 用户门户。
2. 在左侧导航窗格中选择 **“所有服务”** ，在搜索栏中输入“事件中心群集”，然后从结果列表中选择“事件中心群集”项。
3. 进入 **“事件中心群集”** 页后，从顶部菜单中选择 **“+ 添加”** 。 **“创建事件中心群集”** 面板会在右侧打开。
4. 在 **“创建事件中心群集”** 页的  **“基本”**  选项卡上：  
   - **群集名称**：输入名称。 系统会立即检查该名称是否可用。 如果可用，字段的右侧会显示一个勾号。 
   - **订阅**：选择要在其中创建群集的订阅。 
   - **资源组**：创建/选择要在其中创建群集的资源组。 
   - **位置**：可以保留默认的当前位置，也可以更改为其他位置。
   - **群集容量**：对于此快速入门，请保留默认值 1 CU。
      [!INCLUDE [event-hubs-scale](../includes/event-hubs-scale.md)]
   - 选择页面底部的 **“下一步:** 标记 >”按钮以继续。 可能需要等待系统完全预配资源。 

   [![创建事件中心群集 - 基本](media/event-hubs-quickstart-cluster-portal/1-create-cluster-basics.png)](media/event-hubs-quickstart-cluster-portal/1-create-cluster-basics.png#lightbox)

5. 在 **“标记”** 选项卡上： 
   - （可选）为资源标记输入名称/值对。  
   - 选择页面底部的 **“下一步:** 查看 + 创建 >”按钮以继续。 

   [![创建事件中心群集 - 标记](media/event-hubs-quickstart-cluster-portal/1-create-cluster-tags.png)](media/event-hubs-quickstart-cluster-portal/1-create-cluster-tags.png#lightbox)

6. 在 **“查看 + 创建”** 选项卡上，还应在页面顶部看到“验证成功”横幅。 查看详细信息后，在准备好创建群集时选择 **“创建”** 。 

   [![创建事件中心群集 - 查看 + 创建](media/event-hubs-quickstart-cluster-portal/1-create-cluster-review.png)](media/event-hubs-quickstart-cluster-portal/1-create-cluster-review.png#lightbox)

   >[!NOTE]
   > 事件中心群集部署可能需要几分钟才能完成，通常至少需要 45 分钟。

7. 在部署期间，会显示 **“部署正在进行”** 状态页。 部署完成后，页面将更改为 **“部署已完成”** 。 在继续下一部分之前，请选择 **“转到资源”** 按钮来查看新群集。

   [![创建事件中心群集 - 部署完成](media/event-hubs-quickstart-cluster-portal/1-deployment-complete.png)](media/event-hubs-quickstart-cluster-portal/1-deployment-complete.png#lightbox)


## <a name="create-a-namespace"></a>创建命名空间

现在，你可以在新群集中创建命名空间：

1. 在“事件中心群集”的“概述”页上，从顶部菜单中选择“+ 命名空间”  **** 。 

   [![事件中心群集 - 选择群集](media/event-hubs-quickstart-cluster-portal/2-view-cluster.png)](media/event-hubs-quickstart-cluster-portal/2-view-cluster.png#lightbox)

2. 在 **“在群集中创建命名空间”** 面板上：

   - **名称**：输入命名空间的名称。 系统会立即检查该名称是否可用。 如果可用，字段的右侧会显示一个勾号。 
   - **属性/值列表**：命名空间继承以下属性： 
     - 订阅 ID 
     - 资源组 
     - 位置 
     - 群集名称 

   - 选择 **“创建”**  以创建命名空间：

   [![事件中心群集 - 创建命名空间](media/event-hubs-quickstart-cluster-portal/2-view-cluster-create-namespace.png)](media/event-hubs-quickstart-cluster-portal/2-view-cluster-create-namespace.png#lightbox)

3. 你会看到，新的命名空间显示在 **“群集命名空间”** 下方。 继续下一部分之前，请选择新命名空间的链接。 

   [![事件中心群集 - 查看命名空间](media/event-hubs-quickstart-cluster-portal/2-view-cluster-with-namespace.png)](media/event-hubs-quickstart-cluster-portal/2-view-cluster-with-namespace.png#lightbox)

## <a name="create-an-event-hub"></a>创建事件中心

1. 在“事件中心”命名空间的“概述”页上，从顶部菜单中选择“+ 事件中心”  。  

   [![事件中心 - 命名空间概述](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview.png)](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview.png#lightbox)

2. 在 **“创建事件中心”** 面板上：
   - **名称**：输入事件中心的名称。 名称只能包含字母、数字、句点、连字符和下划线。 名称必须以字母或数字开头和结尾。 系统会立即检查该名称是否可用。 如果可用，字段的右侧会显示一个勾号。
   - 选择 **“创建”**  以创建事件中心。

   [![事件中心 - 命名空间概述 - 创建事件中心](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview-create-event-hub.png)](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview-create-event-hub.png#lightbox)

## <a name="delete-an-event-hubs-cluster"></a>删除事件中心群集

删除群集的方法如下：

1. 再次从左侧导航窗格中选择 **“所有服务”** 。 在搜索栏中输入“事件中心群集”，然后从结果列表中选择 **“事件中心群集”** 项。
2. 在 **“事件中心群集”** 页上，找到并选择先前创建的群集。

   [![事件中心群集-查找要删除的群集](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-clusters.png)](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-clusters.png#lightbox)

3. 在 **“事件中心群集”** 的 **“概述”页上**：
   - 从顶部菜单中选择 **“删除”** 。  
   - **“删除群集”** 面板会在右侧打开，并显示删除确认消息。 
   - 输入群集的名称，然后选择 **“删除”**  以删除群集。 

   [![事件中心群集 - 删除群集](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-delete.png)](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-delete.png#lightbox)

## <a name="next-steps"></a>后续步骤

本快速入门介绍如何使用门户创建事件中心群集。 接下来，从下面列出的第一部分开始，继续学习“发送和接收事件”快速入门：  

> [!div class="nextstepaction"]
> [向事件中心发送事件或从事件中心接收事件](/azure/event-hubs/get-started-dotnet-standard-send-v2)
