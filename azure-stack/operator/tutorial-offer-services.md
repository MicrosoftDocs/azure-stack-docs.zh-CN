---
title: 为用户创建服务产品
titleSuffix: Azure Stack Hub
description: 了解如何使用套餐、计划和服务在 Azure Stack Hub 中创建服务产品。
author: BryanLa
ms.author: bryanla
ms.topic: tutorial
ms.date: 10/16/2019
ms.reviewer: shriramnat
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 772871cffa3d5394d1d8b939fa22ae91bc90d4ac
ms.sourcegitcommit: bdd4d529bd3e115a9f76eece62b1613448d5d020
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "80423903"
---
# <a name="create-a-service-offering-for-users-in-azure-stack-hub"></a>在 Azure Stack Hub 中为用户创建服务产品

本教程介绍操作员如何创建套餐。 套餐可以通过订阅的方式向用户提供服务。 订阅套餐之后，用户有权在套餐指定的服务中创建和部署资源。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建套餐。
> * 创建一个计划。
> * 将服务和配额分配到计划。
> * 将计划分配到套餐。

## <a name="overview"></a>概述

套餐由一个或多个计划组成。 计划可以通过指定每个服务的相应资源提供程序和配额，来授予对一个或多个服务的访问权限。 可将计划作为基本计划添加到套餐，或者将套餐扩展为附加计划。 有关详细信息，请参阅[服务、计划、套餐和订阅概述](service-plan-offer-subscription-overview.md)。

![Azure Stack Hub 中的订阅、套餐和计划](media/azure-stack-key-features/image4.png)

### <a name="resource-providers"></a>资源提供程序

资源提供程序支持以服务的形式创建、部署和管理其资源。 常见示例是可用于创建和部署虚拟机 (VM) 的 Microsoft.Compute 资源提供程序。 有关 Azure 资源管理模型的概述，请参阅 [Azure 资源管理器](/azure/azure-resource-manager/resource-group-overview)。

Azure Stack Hub 中有两个常规资源提供程序类别：一个将资源部署为基础服务，另一个将资源部署为增值服务。

### <a name="foundational-services"></a>基本服务

>[!NOTE]
> 本教程将介绍如何根据基础服务创建套餐。 

以下资源提供程序支持基础服务，每次安装 Azure Stack Hub 都会原生提供这些资源提供程序：

| 资源提供程序 | 示例资源 |
| ----------------- | ------------------|
| Microsoft.Compute | VM、磁盘、虚拟机规模集 |
| Microsoft.KeyVault | Key Vault、机密 |
| Microsoft.Network | 虚拟网络、公共 IP 地址、负载均衡器 |
| Microsoft.Storage | 存储帐户、Blob、队列、表 |

### <a name="value-add-services"></a>增值服务

>[!NOTE]
> 若要提供增值服务，必须先在 Azure Stack Hub 市场中安装相应的资源提供程序。 安装之后，其资源将通过与基础服务相同的方式提供给用户。 有关目前支持增值服务产品的资源提供程序集，请参阅目录中的**操作指南**部分。

部署 Azure Stack Hub 后安装的资源提供程序支持增值服务。 示例包括：

| 资源提供程序 | 示例资源 |
| ----------------- | ------------------------- |
| Microsoft.Web | 应用服务函数应用、Web 应用、API 应用 | 
| Microsoft.MySqlAdapter | MySQL 托管服务器、MySQL 数据库 | 
| Microsoft.SqlAdapter | SQL Server 托管服务器、SQL Server 数据库 |
| Microsoft.EventHub | 事件中心 |

::: moniker range=">=azs-1902"

## <a name="create-an-offer"></a>创建产品

在创建套餐的过程中，需同时创建套餐和计划。 计划用作套餐的基本计划。 在计划创建过程中，可以指定在计划中提供的服务及其各自的配额。

1. 使用云管理员帐户登录到管理员门户。

    - 对于集成系统，URL 根据操作员的区域和外部域名而有所不同。 URL 使用格式`https://adminportal.<region>.<FQDN>`。
    - 如果使用的是 Azure Stack 开发工具包，则 URL 为 `https://adminportal.local.azurestack.external`。

    然后选择“+ 创建资源”“套餐 + 计划”“套餐”。**** > **** > ****

    ![Azure Stack 中心管理门户中的新产品/服务](media/tutorial-offer-services/1-create-resource-offer.png)

1. 在“创建新套餐”中的“基本信息”选项卡下，输入**显示名称**和**资源名称**，然后选择现有的或创建新的**资源组**。******** “显示名称”是套餐的友好名称。 只有云操作员可以看到资源名称，管理员可以使用该名称将套餐作为 Azure 资源管理器资源进行处理。

   ![Azure Stack 集线器管理门户中的显示名称](media/tutorial-offer-services/2-create-new-offer.png)

1. 选择 "**基本计划**" 选项卡，然后选择 "**创建新计划**" 以创建新计划。 计划也会作为基本计划添加到套餐。

   ![在 Azure Stack 集线器管理门户中添加计划](media/tutorial-offer-services/3-create-new-offer-base-plans.png)

1. 在“新建计划”中的“基本信息”选项卡下，输入**显示名称**和**资源名称**。******** 显示名称是用户可以看到的计划友好名称。 只有云操作员可以看到资源名称，云操作员可以使用该名称将计划作为 Azure 资源管理器资源处理。 “资源组”将设置为针对套餐指定的资源组。****

   ![Azure Stack 中心管理门户中的计划显示名称](media/tutorial-offer-services/4-create-new-plan-basics.png)

1. 选择“服务”选项卡，此时会看到安装资源提供程序后提供的服务列表。**** 选择“Microsoft.Compute”、“Microsoft.Network”和“Microsoft.Storage”。************

   ![在 Azure Stack Hub 管理门户中规划服务](media/tutorial-offer-services/5-create-new-plan-services.png)

1. 选择“配额”选项卡，此时会看到为此计划启用的服务列表。**** 选择 "**新建**" 以指定**Microsoft**的自定义配额。 配额的“名称”是必填字段；可以接受或更改每个配额值。**** 完成后选择“确定”，然后对剩余的服务重复上述步骤。****

   ![在 Azure Stack Hub 管理门户中创建计算配额](media/tutorial-offer-services/6-create-new-plan-quotas.png)

1. 选择 "**查看**" 和 "创建" 选项卡。你应该会在顶部看到一个绿色的 "验证已通过" 横幅，指示已准备好创建新的基本计划。 选择“创建”。  此时应该也会显示一条通知指出计划已创建。

   ![在 Azure Stack Hub 管理门户中创建新计划](media/tutorial-offer-services/7-create-new-plan-review-create.png)

1. 返回到“创建新套餐”页的“基本计划”选项卡之后，将会发现计划已创建。******** 确保已选择要作为基本计划包含在套餐中的新计划，然后选择“查看 + 创建”。****

   ![在 Azure Stack 集线器管理门户中添加基本计划](media/tutorial-offer-services/8-create-new-offer-base-plans-done.png)

1. 在“查看 + 创建”选项卡的顶部，应会显示绿色的“通过验证”横幅。**** 检查“基本信息”和“基本计划”中的信息，并在准备就绪时选择“创建”。****

   ![在 Azure Stack Hub 管理门户中创建新产品/服务](media/tutorial-offer-services/9-create-new-offer-review-create.png)

1. 一开始会显示“部署正在进行中”页，套餐部署完成后，随即显示“部署已完成”。 在 "**资源**" 列下选择产品/服务的名称。

   ![在 Azure Stack Hub 管理门户中完成提供部署](media/tutorial-offer-services/10-offer-deployment-complete.png)

1. 请注意横幅，其中显示套餐仍属于私人，因此用户无法订阅它。 通过选择 "**更改状态**"，然后选择 "**公共**"，将其更改为 "公共"。

    ![Azure Stack 集线器管理门户中的公共状态](media/tutorial-offer-services/11-offer-change-state.png)

::: moniker-end

::: moniker range="<=azs-1901"

## <a name="create-an-offer-1901-and-earlier"></a>创建套餐（1901 和更低版本）

在创建套餐的过程中，需同时创建套餐和计划。 计划用作套餐的基本计划。 在计划创建过程中，可以指定在计划中提供的服务及其各自的配额。

1. 使用云管理员帐户登录到管理员门户。

    - 对于集成系统，URL 根据操作员的区域和外部域名（使用格式`https://adminportal.<region>.<FQDN>`）而有所不同。
    - 如果使用的是 Azure Stack 开发工具包，则 URL 为 <https://adminportal.local.azurestack.external>。

    然后选择“+ 创建资源”“套餐 + 计划”“套餐”。**** > **** > ****

    ![Azure Stack 中心管理门户中的新产品/服务](media/tutorial-offer-services/image01.png)

1. 在“新建套餐”中，输入“显示名称”和“资源名称”，然后选择新的或现有的**资源组**。************ “显示名称”是套餐的友好名称。 只有云操作员可以看到资源名称，管理员可以使用该名称将套餐作为 Azure 资源管理器资源进行处理。

   ![Azure Stack 集线器管理门户中的显示名称](media/tutorial-offer-services/image02.png)

1. 选择“基本计划”，在“计划”部分选择“添加”，将新计划添加到套餐。************

   ![在 Azure Stack 集线器管理门户中添加计划](media/tutorial-offer-services/image03.png)

1. 在“新建计划”部分中，填写“显示名称”和“资源名称”。************ 显示名称是用户可以看到的计划友好名称。 只有云操作员可以看到资源名称，云操作员可以使用该名称将计划作为 Azure 资源管理器资源处理。

   ![Azure Stack 中心管理门户中的计划显示名称](media/tutorial-offer-services/image04.png)

1. 选择“服务”。**** 在“服务”列表中，选择“Microsoft.Compute”、“Microsoft.Network”和“Microsoft.Storage”。************ 选择“选择”，将这些服务添加到计划。****

   ![在 Azure Stack Hub 管理门户中规划服务](media/tutorial-offer-services/image05.png)

1. 选择“配额”，然后选择要为其创建配额的第一个服务。**** 对于 IaaS 配额，请使用以下示例作为指导，配置“计算”、“网络”和“存储服务”的配额。

    - 首先为“计算”服务创建配额。 在命名空间列表中，选择“Microsoft.Compute”，然后选择“创建新配额”。********
    
      ![创建新配额](media/tutorial-offer-services/image06.png)

   - 在“创建配额”中，输入配额的名称。**** 可以更改或接受显示的任何配额值。 在此示例中，我们接受默认设置，并选择“确定”。****
   
      ![配额名称](media/tutorial-offer-services/image07.png)
       
    - 在命名空间列表中选择“Microsoft.Compute”，然后选择创建的配额。**** 此步骤会将该配额链接到“计算”服务。
    
      ![选择配额](media/tutorial-offer-services/image08.png)
        
        针对“网络”和“存储”服务重复上述步骤。 完成后，在“配额”中选择“确定”以保存所有配额。********

1. 在“新建计划”中，选择“确定”。********

1. 在“计划”下面选择新计划，然后选择“选择”。********

1. 在“新建套餐”中，选择“创建”。******** 创建套餐后，会看到通知。

1. 在仪表板菜单中选择“套餐”，然后选择创建的套餐。****

1. 依次选择“更改状态”、“公共”。********

    ![公共状态](media/tutorial-offer-services/image09.png)

::: moniker-end
 
## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建套餐。
> * 创建一个计划。
> * 向计划分配服务和配额。
> * 向产品/服务分配计划。

转到下一教程，了解如何执行以下操作：
> [!div class="nextstepaction"]
> [测试本教程中提供的服务](tutorial-test-offer.md)
