---
title: 在 Azure Stack Hub 中创建包含套餐的订阅
description: 了解如何在 Azure Stack Hub 中创建包含套餐的新订阅，然后使用测试 VM 测试该套餐。
author: bryanla
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 11/13/2019
ms.openlocfilehash: 244d5e214e2d1d42e5697a5aafd51c17d97465f6
ms.sourcegitcommit: bdd4d529bd3e115a9f76eece62b1613448d5d020
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "77704890"
---
# <a name="tutorial-create-and-test-a-subscription-in-azure-stack-hub"></a>教程：在 Azure Stack Hub 中创建和测试订阅

本教程介绍如何创建包含套餐的订阅，然后如何对其进行测试。 测试时，需要以云管理员身份登录到 Azure Stack Hub 用户门户，订阅套餐，然后创建虚拟机 (VM)。

> [!TIP]
> 若要获得更高级的评估体验，可[为特定用户创建订阅](../operator/azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator)，然后在用户门户中以该用户的身份登录。

本教程介绍如何订阅 Azure Stack Hub 套餐。

学习内容：

> [!div class="checklist"]
> * 订阅产品 
> * 测试产品/服务

## <a name="subscribe-to-an-offer"></a>订阅产品

若要以用户身份订阅套餐，请登录 Azure Stack Hub 用户门户以查看 Azure Stack Hub 运营商提供的可用服务。

1. 登录到用户门户，并选择“获取订阅”。 

   ![获取订阅](media/azure-stack-subscribe-services/get-subscription.png)

2. 在“显示名称”字段中，键入订阅的名称。  然后选择“套餐”，在“选择套餐”部分选择某个可用套餐。   然后选择“创建”  。

   ![创建产品](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > 刷新用户门户以开始使用该订阅。

3. 若要查看已创建的订阅，请选择“所有服务”  。 然后，在“常规”  类别下选择“订阅”  ，然后选择新订阅。 订阅套餐之后，请刷新门户，以查看新服务是否已包含为新订阅的一部分。 在本示例中，我们已添加**虚拟机**。

   ![查看订阅](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>测试产品/服务

登录到用户门户后，可以使用新订阅功能预配 VM，以测试套餐。

> [!NOTE]
> 此项测试需要先将 Windows Server 2016 Datacenter VM 添加到 Azure Stack Hub 市场。

1. 登录到用户门户。

2. 在用户门户中，依次选择“虚拟机”、“添加”、“Windows Server 2016 Datacenter”和“创建”。    

3. 在“基本信息”部分，输入“名称”、“用户名”和“密码”，选择“订阅”，创建一个**资源组**，然后选择“确定”。      

4. 在“选择大小”部分中选择“A1 标准”，然后选择“选择”。     

5. 在“设置”边栏选项卡中接受默认值，然后选择“确定”。  

6. 在“摘要”部分中，选择“确定”以创建该 VM。    

7. 若要查看新 VM，请选择“虚拟机”  ，然后搜索新 VM 并选择其名称。

    ![所有资源](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> VM 部署需要几分钟时间才能完成。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从社区模板创建 VM](azure-stack-create-vm-template.md)
