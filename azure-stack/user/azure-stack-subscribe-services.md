---
title: 在 Azure Stack 中心创建包含产品/服务的订阅
description: 了解如何使用 Azure Stack 集线器中的产品/服务创建新订阅，并使用测试 VM 测试产品/服务。
author: bryanla
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: ea0bf7287fd0463aa7b57a24386a30f7f3a1b754
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884327"
---
# <a name="tutorial-create-and-test-a-subscription-in-azure-stack-hub"></a>教程：在 Azure Stack 中心创建和测试订阅

本教程介绍如何创建包含产品/服务的订阅，以及如何对其进行测试。 对于测试，你以云管理员身份登录到 Azure Stack 集线器用户门户，订阅产品/服务，然后创建虚拟机（VM）。

> [!TIP]
> 为了获得更高级的评估体验，你可以为[特定用户创建订阅](../operator/azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)，并在用户门户中以该用户身份登录。

本教程演示如何订阅 Azure Stack 中心产品/服务。

学习内容：

> [!div class="checklist"]
> * 订阅套餐 
> * 测试产品/服务

## <a name="subscribe-to-an-offer"></a>订阅套餐

若要以用户身份订阅产品/服务，请登录到 Azure Stack 集线器用户门户以检查 Azure Stack 中心操作员提供的可用服务。

1. 登录到用户门户，然后选择 "**获取订阅**"。

   ![获取订阅](media/azure-stack-subscribe-services/get-subscription.png)

2. 在 "**显示名称**" 字段中，键入订阅的名称。 然后选择 "**产品/服务**"，在 "**选择产品/服务**" 部分中选择一个可用产品/服务。 然后选择“创建”。

   ![创建套餐](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > 刷新用户门户，开始使用你的订阅。

3. 若要查看创建的订阅，请选择 "**所有服务**"。 然后，在 "**常规**" 类别下选择 "**订阅**"，然后选择新订阅。 订阅产品/服务后，刷新门户以查看新的服务是否已包括在新订阅中。 在此示例中，添加了**虚拟机**。

   ![查看订阅](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>测试产品/服务

登录到用户门户时，请通过使用新的订阅功能预配 VM 来测试产品/服务。

> [!NOTE]
> 此测试要求先将 Windows Server 2016 Datacenter VM 添加到 Azure Stack 中心市场。

1. 登录到用户门户。

2. 在用户门户中，依次选择 "**虚拟机**"、"**添加**"、" **Windows Server 2016 Datacenter**"，然后选择 "**创建**"。

3. 在 "**基本**信息" 部分中，键入 "**名称**"、"**用户名**" 和 "**密码**"，选择 "**订阅**"，创建一个资源组（或选择现有的**资源组**），然后选择 **"确定"** 。

4. 在 "**选择大小**" 部分中，选择 " **A1 标准**"，然后选择 "**选择**"。  

5. 在 "**设置**" 边栏选项卡中，接受默认值，然后选择 **"确定"** 。

6. 在 "**摘要**" 部分中，选择 **"确定"** 以创建 VM。  

7. 若要查看新 VM，请选择 "**虚拟机**"，然后搜索新 vm，然后选择其名称。

    ![所有资源](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> VM 部署需要几分钟才能完成。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从社区模板创建 VM](azure-stack-create-vm-template.md)
