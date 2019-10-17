---
title: 提供订阅 Azure Stack 服务。
description: 了解如何使用产品/服务、计划和服务创建服务产品。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: tutorial
ms.date: 10/16/2019
ms.reviewer: shriramnat
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 87542483f2a35fba315258e9ba66b1792739033b
ms.sourcegitcommit: df20662e77a6ed0a7eba03f79eb53e8cd4471206
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72445351"
---
# <a name="tutorial-offer-a-service-to-users"></a>教程：向用户提供服务

本教程介绍了如何创建产品/服务的操作员。 产品/服务使服务可供用户使用。 订阅产品/服务后，用户有权在该产品/服务指定的服务内创建和部署资源。

本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 创建套餐
> * 创建计划
> * 向计划分配服务和配额
> * 向产品/服务分配计划

## <a name="overview"></a>概述

产品/服务包含一个或多个计划。 对一个或多个服务的访问权限的计划，通过指定每个服务的相应资源提供程序和配额。 可以将计划作为基础计划添加到产品/服务，或将其作为附加计划扩展。 若要了解详细信息，请参阅 "[服务、计划、产品/服务、订阅概述](service-plan-offer-subscription-overview.md)"。

![订阅、产品/服务和计划](media/azure-stack-key-features/image4.png)

### <a name="resource-providers"></a>资源提供程序

资源提供程序支持在服务中创建、部署和管理其资源。 常见的示例是 Microsoft. 计算资源提供程序，它提供了创建和部署虚拟机（Vm）的功能。 有关 Azure 资源管理模型的概述，请参阅[azure 资源管理器](/azure/azure-resource-manager/resource-group-overview)。

在 Azure Stack 中，有两种常规类别的资源提供程序：将资源部署为基础服务的资源提供程序，以及部署为外接程序服务的资源提供程序。

### <a name="foundational-services"></a>基础服务

>[!NOTE]
> 在本教程中，将了解如何基于基础服务创建产品/服务。 

以下资源提供程序支持基础服务，每次安装 Azure Stack 时，这些服务都可在本地使用：

| 资源提供程序 | 示例资源 |
| ----------------- | ------------------|
| Microsoft.Compute | 虚拟机、磁盘、虚拟机规模集 |
| Microsoft.KeyVault | 密钥保管库，机密 |
| Microsoft.Network | 虚拟网络、公共 IP 地址、负载均衡器 |
| Microsoft.Storage | 存储帐户、blob、队列、表 |

### <a name="add-on-services"></a>附加服务

>[!NOTE]
> 为了提供附加服务，必须首先将相应的资源提供程序安装到 Azure Stack Marketplace 中。 安装后，其资源将以与基础服务相同的方式向用户提供。 请参阅 TOC 的操作**方法指南**部分，了解支持附加服务服务的当前资源提供程序集。

加载项服务受 Azure Stack 部署后安装的资源提供程序支持。 示例包括：

| 资源提供程序 | 示例资源 |
| ----------------- | ------------------------- |
| Microsoft.Web | 应用服务函数应用、web 应用、API 应用 | 
| Mysqladapter.dbadapter | MySQL 宿主服务器，MySQL 数据库 | 
| Microsoft.sqladapter | SQL Server 宿主服务器、SQL Server 数据库 |

::: moniker range=">=azs-1902"
## <a name="create-an-offer"></a>创建套餐

在产品/服务创建过程中，将同时创建产品/服务和计划。 计划用作产品/服务的基本计划。 在计划创建期间，可以指定计划中提供的服务及其各自的配额。

1. 使用云管理员帐户登录到管理员门户。

   - 对于集成系统，URL 根据操作员的区域和外部域名，使用格式 https://adminportal.&lt ;*区域*&gt;. &lt;*FQDN*&gt; 。
   - 如果使用的是 Azure Stack 开发工具包，则 URL 为 @no__t。

   然后选择 " **+ 创建资源**>**产品** **/@no__t 服务"。**

   ![新套餐](media/tutorial-offer-services/1-create-resource-offer.png)

1. 在 "**基本**信息" 选项卡下的 "**创建新产品/服务**" 下，输入**显示名称**和**资源名称**，并选择现有资源组或创建新的**资源组**。 显示名称为产品/服务的友好名称。 只有云操作员可以看到资源名称，此名称是管理员使用该产品/服务作为 Azure 资源管理器资源的名称。

   ![显示名称](media/tutorial-offer-services/2-create-new-offer.png)

1. 选择 "**基本计划**" 选项卡，选择 "**创建新计划**" 以创建新计划。 该计划还将作为基本计划添加到产品/服务中。

   ![添加计划](media/tutorial-offer-services/3-create-new-offer-base-plans.png)

1. 在 "**基本**信息" 选项卡下的 "**新建计划**" 中，输入**显示名称**和**资源名称**。 显示名称是用户看到的计划的友好名称。 只有云操作员可以看到资源名称，这是云操作员用来将计划用作 Azure 资源管理器资源的名称。 **资源组**将设置为该产品/服务指定的资源组。

   ![计划显示名称](media/tutorial-offer-services/4-create-new-plan-basics.png)

1. 选择 "**服务**" 选项卡，你将看到已安装的资源提供程序中可用的服务列表。 选择 " **microsoft. 计算**"、" **microsoft**" 和 " **microsoft**"。 

   ![规划服务](media/tutorial-offer-services/5-create-new-plan-services.png)

1. 选择 "**配额**" 选项卡，可以看到已为此计划启用的服务列表。 单击 "**新建**" 以指定**Microsoft**的自定义配额。 需要配额**名称**;您可以接受或更改每个配额值。 完成后，选择 **"确定"** ，并对剩余的服务重复执行这些步骤。

   ![创建计算配额](media/tutorial-offer-services/6-create-new-plan-quotas.png)

1. 选择 "**查看**" 和 "创建" 选项卡。你应该会在顶部看到一个绿色的 "验证已通过" 横幅，指示已准备好创建新的基本计划。 选择**创建**。 还会看到一个通知，指示已创建计划。

   ![创建新计划](media/tutorial-offer-services/7-create-new-plan-review-create.png)

1. 返回到 "**创建新产品/服务**" 页的 "**基本计划**" 选项卡后，你会注意到已创建该计划。 请确保选择 "新计划" 作为基本计划包含在产品/服务中，然后选择 "**查看 + 创建**"。

   ![添加基本计划](media/tutorial-offer-services/8-create-new-offer-base-plans-done.png)

1. 在 "**查看**" 和 "创建" 选项卡上，你应该会在顶部看到绿色的 "验证通过" 横幅。 查看 "基本" 和 "基本计划" 信息，并在准备就绪时选择 "**创建**"。 

   ![新建产品/服务](media/tutorial-offer-services/9-create-new-offer-review-create.png)

1. 部署产品/服务后，最初会显示 "部署正在进行" 页，然后是 "部署已完成"。 单击 "**资源**" 列下产品/服务的名称。

   ![完成产品部署](media/tutorial-offer-services/10-offer-deployment-complete.png)


1. 请注意，显示产品/服务的标题仍是专用的，这会阻止用户订阅。 通过选择 "**更改状态**"，然后选择 "**公共**"，将其更改为 "公共"。

    ![公共状态](media/tutorial-offer-services/11-offer-change-state.png)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-offer-1901-and-earlier"></a>创建产品/服务（1901及更早版本）

在产品/服务创建过程中，将同时创建产品/服务和计划。 计划用作产品/服务的基本计划。 在计划创建期间，可以指定计划中提供的服务及其各自的配额。

1. 使用云管理员帐户登录到管理员门户。

   - 对于集成系统，URL 根据操作员的区域和外部域名，使用格式 https://adminportal.&lt ;*区域*&gt;. &lt;*FQDN*&gt; 。
   - 如果使用的是 Azure Stack 开发工具包，则 URL 为 @no__t。
   
   然后选择 " **+ 创建资源**>**产品** **/@no__t 服务"。**

   ![新套餐](media/tutorial-offer-services/image01.png)

1. 在 "**新产品/服务**" 中，输入 "**显示名称**" 和 "**资源名称**"，然后选择新的或现有的**资源组**。 显示名称为产品/服务的友好名称。 只有云操作员可以看到资源名称，此名称是管理员用于作为 Azure 资源管理器资源的产品/服务的名称。

   ![显示名称](media/tutorial-offer-services/image02.png)

1. 选择 "**基本计划**"，然后在 "**计划**" 部分中选择 "**添加**"，将新计划添加到产品/服务。

   ![添加计划](media/tutorial-offer-services/image03.png)

1. 在 "**新建计划**" 部分中，填写 "**显示名称**" 和 "**资源名称**"。 显示名称是用户看到的计划的友好名称。 只有云操作员可以看到资源名称，这是云操作员用来将计划用作 Azure 资源管理器资源的名称。

   ![计划显示名称](media/tutorial-offer-services/image04.png)

1. 选择“服务”。 从服务列表中，选择 " **microsoft**"、" **microsoft"** 、"Microsoft" 和 " **microsoft**"。 选择 "**选择**" 以将这些服务添加到计划。

   ![规划服务](media/tutorial-offer-services/image05.png)

1. 选择 "**配额**"，然后选择要为其创建配额的第一个服务。 对于 IaaS 配额，请使用以下示例作为配置计算、网络和存储服务的配额的指南。

   - 首先，为计算服务创建配额。 在 "命名空间" 列表中，选择 " **Microsoft** "，然后选择 "**创建新配额**"。

     ![创建新配额](media/tutorial-offer-services/image06.png)

   - 在 "**创建配额**" 中，输入配额的名称。 您可以更改或接受显示的任何配额值。 在此示例中，我们接受默认设置，然后选择 **"确定"** 。

     ![配额名称](media/tutorial-offer-services/image07.png)

   - 在 "命名空间" 列表中选择 " **Microsoft** "，然后选择创建的配额。 此步骤将配额链接到计算服务。

     ![选择配额](media/tutorial-offer-services/image08.png)

      为网络和存储服务重复以上步骤。 完成后，在 "**配额**" 中选择 **"确定"** 以保存所有配额。

1. 在 "**新建计划**" 中，选择 **"确定"** 。

1. 在 "**计划**" 下，选择新计划，然后**选择**。

1. 在 "**新产品/服务**" 中选择 "**创建**"。 创建产品/服务后，你将看到一条通知。

1. 在 "仪表板" 菜单上，选择 "**产品/服务**"，然后选择创建的产品/服务。

1. 选择 "**更改状态**"，然后选择 "**公共**"。

    ![公共状态](media/tutorial-offer-services/image09.png)
::: moniker-end
 
## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建套餐
> * 创建计划
> * 向计划分配服务和配额
> * 向产品/服务分配计划

转到下一教程，了解如何：
> [!div class="nextstepaction"]
> [测试本教程中提供的服务](tutorial-test-offer.md)
