---
title: 使用门户创建事件中心群集
description: 了解如何使用 Azure Stack 集线器用户门户创建事件中心群集。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: quickstart
ms.date: 01/22/2020
ms.reviewer: bryanla
ms.lastreviewed: 01/22/2020
ms.openlocfilehash: 6af9ef6f562c9b31d7310b7f35d7b6b0533472ea
ms.sourcegitcommit: b824c7b9af9ba415ca4fe8d15673b521362f0abb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80479253"
---
# <a name="quickstart-create-an-event-hubs-cluster-using-the-azure-stack-hub-portal"></a>快速入门：使用 Azure Stack 中心门户创建事件中心群集

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

本快速入门介绍如何使用 Azure Stack 集线器用户门户创建事件中心群集。 

事件中心群集提供单租户部署，满足最苛刻的流式处理需求。 事件中心群集可以每秒传入数百万个事件，并可保证容量和亚秒级延迟。 事件中心群集包含所有常用功能，并与 Azure 事件中心版提供的功能之间的奇偶校验。

## <a name="prerequisites"></a>必备条件

在完成本快速入门之前，必须确保你的订阅中已提供事件中心服务。 否则，请与管理员合作，[在 Azure Stack Hub 资源提供程序上安装事件中心](../operator/event-hubs-rp-overview.md)。 安装步骤还介绍了如何创建包含事件中心服务的产品/服务。 

提供产品/服务后，管理员可以创建或更新订阅，使其包含事件中心。 或者，你可以[订阅新产品/服务并创建自己的订阅](azure-stack-subscribe-services.md)。

## <a name="overview"></a>概述

事件中心群集是通过指定容量单位（Cu）创建的。 CU 是预先分配的 CPU、存储量和内存资源量。 事件中心群集按 CPU/小时计费。 在群集创建过程中选择 Cu （群集大小）时，将显示群集使用的内核（Cpu）数。 有关群集资源使用情况的更多详细信息，请参阅[如何在 Azure Stack 中心为事件中心执行容量规划](../operator/event-hubs-rp-capacity-planning.md)。 

本快速入门介绍如何使用 Azure Stack 集线器用户门户来执行以下操作：
- 创建1个 CU 事件中心群集。
- 在群集中创建命名空间。
- 在命名空间中创建事件中心。
- 删除事件中心群集

## <a name="create-an-event-hubs-cluster"></a>创建事件中心群集

事件中心群集提供唯一的范围容器，可以在该容器中创建一个或多个命名空间。 完成以下步骤以创建事件中心群集： 

1. 登录到 Azure Stack 集线器用户门户。
2. 从左侧导航窗格中选择 " **所有 服务**"，在搜索栏中输入 "事件中心群集"，然后从 "结果" 列表中选择 "**事件中心群集**" 项。
3. 在 "**事件中心群集**" 页上，选择顶部菜单中的 " **+ 添加**"。 "**创建事件中心群集**" 面板将在右侧打开。
4. 在 " **创建事件中心群集** " 页的 "**基本**信息" 选项卡上：  
   - **群集名称**：输入名称。 系统会立即检查该名称是否可用。 如果可用，则复选标记将显示在字段的右端。 
   - **订阅**：选择要在其中创建群集的订阅。 
   - **资源组**：创建/选择要在其中创建群集的资源组。 
   - 选择页面底部的 " **标记 >** " "按钮以继续。 可能需要等待系统完全预配资源。 

   [![创建事件中心群集-基础](media/event-hubs-quickstart-cluster-portal/1-create-cluster-basics.png)](media/event-hubs-quickstart-cluster-portal/1-create-cluster-basics.png#lightbox)

5. 在 " **标记**" 选项卡上： 
   - （可选）为资源标记输入名称/值对。  
   - 选择 **下一步： "查看 + 创建 >**  " 按钮继续。 

   [![创建事件中心群集-标记](media/event-hubs-quickstart-cluster-portal/1-create-cluster-tags.png)](media/event-hubs-quickstart-cluster-portal/1-create-cluster-tags.png#lightbox)

6. 在 " **查看**" 和 "创建" 选项卡上，还应在页面顶部看到 "验证成功" 横幅。 查看详细信息后 **，选择 "准备创建群集**"。 

   [![创建事件中心群集-查看和创建](media/event-hubs-quickstart-cluster-portal/1-create-cluster-review.png)](media/event-hubs-quickstart-cluster-portal/1-create-cluster-review.png#lightbox)

   >[!NOTE]
   > 事件中心群集部署可能需要几分钟才能完成，通常至少为45分钟。

7. 在部署过程中，你**会看到部署正在进行**中。 部署完成后，页面将更改为**完成部署**。 继续下一节之前，请选择 "**中转到资源**" 按钮查看新群集。

   [![创建事件中心群集-部署完成](media/event-hubs-quickstart-cluster-portal/1-deployment-complete.png)](media/event-hubs-quickstart-cluster-portal/1-deployment-complete.png#lightbox)


## <a name="create-a-namespace"></a>创建命名空间

现在，你可以在新群集中创建命名空间：

1. 在**事件中心群集**的 "**概述**" 页上，从顶部菜单中选择 " **+ 命名空间** "。 

   [![事件中心群集-选择群集](media/event-hubs-quickstart-cluster-portal/2-view-cluster.png)](media/event-hubs-quickstart-cluster-portal/2-view-cluster.png#lightbox)

2. 在 "群集" 面板中的 "**创建命名空间**"：

   - **名称**：输入命名空间的名称。 系统会立即检查该名称是否可用。 如果可用，则复选标记将显示在字段的右端。 
   - **属性/值列表**：命名空间继承以下属性： 
     - 订阅 ID 
     - 资源组 
     - 位置 
     - 群集名称 

   - 选择 " **创建** 创建命名空间：

   [![事件中心群集-创建命名空间](media/event-hubs-quickstart-cluster-portal/2-view-cluster-create-namespace.png)](media/event-hubs-quickstart-cluster-portal/2-view-cluster-create-namespace.png#lightbox)

3. 记下 "**群集命名空间**" 下的新命名空间。 继续下一节之前，请选择新命名空间的链接。 

   [![事件中心群集-查看命名空间](media/event-hubs-quickstart-cluster-portal/2-view-cluster-with-namespace.png)](media/event-hubs-quickstart-cluster-portal/2-view-cluster-with-namespace.png#lightbox)

## <a name="create-an-event-hub"></a>创建事件中心

1. 在**事件中心命名空间**的 "**概述**" 页上，从顶部菜单中选择 " **+ 事件中心**"。  

   [![事件中心-命名空间概述](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview.png)](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview.png#lightbox)

2. 在 "**创建事件中心**" 面板上：
   - **名称**：输入事件中心的名称。 名称只能包含字母、数字、句点、连字符和下划线。 名称必须以字母或数字开头和结尾。 系统会立即检查该名称是否可用。 如果可用，则复选标记将显示在字段的右端。
   - 选择 " **创建** " 创建事件中心。

   [![事件中心-命名空间概述创建事件中心](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview-create-event-hub.png)](media/event-hubs-quickstart-cluster-portal/3-event-hubs-namespace-overview-create-event-hub.png#lightbox)

## <a name="delete-an-event-hubs-cluster"></a>删除事件中心群集

删除群集：

1. 再次从左侧导航窗格中选择 " **所有服务**" 。 在搜索栏中输入 "事件中心群集"，然后从 "结果" 列表中选择 "事件**中心群集**" 项。
2. 在 "**事件中心群集**" 页上，找到并选择之前创建的群集。

   [![事件中心群集-删除群集](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-clusters.png)](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-clusters.png#lightbox)

3. 在**事件中心群集**的 "**概述**" 页上：
   - 从顶部菜单中选择 " **删除** "。  
   - 此时会在右侧打开 "**删除群集**" 面板，其中包含删除确认消息。 
   - 输入群集的名称，然后选择 " **删除**" 删除群集。 

   [![事件中心群集-删除群集](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-delete.png)](media/event-hubs-quickstart-cluster-portal/4-delete-cluster-delete.png#lightbox)

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何使用门户创建事件中心群集。 现在，从第一个快速入门开始，开始 "发送和接收事件" 快速入门：  

> [!div class="nextstepaction"]
> [向事件中心发送事件或从事件中心接收事件](/azure/event-hubs/get-started-dotnet-standard-send-v2)
