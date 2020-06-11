---
title: 测试 Azure Stack Hub 服务产品。
description: 了解如何通过创建订阅并部署资源来测试服务套餐。
author: BryanLa
ms.author: bryanla
ms.topic: tutorial
ms.date: 10/13/2019
ms.reviewer: shriramnat
ms.lastreviewed: 10/06/2019
ms.openlocfilehash: b0dd6b5013a19c120d1d8e69594406292ee6ac6c
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84631359"
---
# <a name="tutorial-test-a-service-offering"></a>教程：测试服务套餐

在上一篇教程中，你已为用户创建套餐。 本教程介绍如何使用该套餐创建订阅，以此测试该套餐。 然后，创建资源并将其部署到订阅提供的基础服务。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建订阅
> * 创建并部署资源

## <a name="prerequisites"></a>先决条件

在开始学习本教程之前，必须满意以下先决条件：

- 完成[向用户提供服务](tutorial-offer-services.md)教程。 此教程介绍了如何创建本教程所用的套餐。

- 使用本教程中订阅的套餐可以部署虚拟机 (VM) 资源。 若要测试 VM 部署，必须先从 Azure 市场下载 VM 映像，并在 Azure Stack Hub 市场中提供该映像。 有关说明，请参阅[将市场项从 Azure 下载到 Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)。 

## <a name="subscribe-to-the-offer"></a>订阅套餐

1. 使用用户帐户登录到用户门户 

   - 对于集成系统，URL 根据操作员所在的区域和外部域名的不同而异，格式为 https://portal.&lt;*region*&gt;.&lt;*FQDN*&gt; 。
   - 如果使用的是 Azure Stack 开发工具包，则门户地址为 `https://portal.local.azurestack.external` 。

1. 选择“获取订阅”磁贴。

   ![获取订阅](media/tutorial-test-offer/1-get-subscription.png)

1. 在“获取订阅”中的“显示名称”字段内输入新订阅的名称。  选择“套餐”，然后从“选择套餐”列表中选择在上一篇教程中创建的套餐。  选择“创建” 。

   ![创建产品](media/tutorial-test-offer/2-create-subscription.png)

1. 若要查看订阅，请选择“所有服务”，然后在“常规”类别下选择“订阅”。 选择新订阅以查看与其关联的套餐和套餐属性。

   >[!NOTE]
   >订阅套餐之后，可能需要刷新门户才能看到哪些服务包含在新订阅中。

::: moniker range=">=azs-1902"
## <a name="deploy-a-storage-account-resource"></a>部署存储帐户资源

在用户门户中，可以使用上一部分中创建的订阅来预配存储帐户。

1. 使用用户帐户登录到用户门户。

1. 选择“+创建资源”>“数据 + 存储”>“存储帐户 - Blob、文件、表、队列”。  

1. 在“创建存储帐户”中提供以下信息：
  
   - 输入**名称**
   - 选择新**订阅**
   - 选择一个**资源组**（或创建新的资源组）。 
   - 选择“创建”以创建存储帐户。

1. 部署开始后，你将返回到仪表板。 若要查看新存储帐户，请选择“所有资源”。 搜索该存储帐户，然后从搜索结果中选择其名称。 可在此处管理存储帐户及其内容。

## <a name="deploy-a-virtual-machine-resource"></a>部署虚拟机资源

在用户门户中，可以使用上一部分中创建的订阅来预配虚拟机。

1. 使用用户帐户登录到用户门户。

1. 选择 " **+ 创建资源** > " "**计算**" > **\<image-name\>** ，其中 "映像名称" 是你在 "先决条件" 中下载的虚拟机的名称。
1. 在“创建虚拟机”/“基本信息”中提供以下信息： 
  
   - 输入 VM 的**名称**。
   - 输入一个**用户名**作为管理员帐户。
   - 对于 Linux VM，请选择“密码”作为“身份验证类型”。
   - 输入管理员帐户的**密码**，并在“确认密码”中输入相同的密码。
   - 选择新**订阅**。
   - 选择一个**资源组**（或创建新的资源组）。 
   - 选择“确定”以验证此信息并继续。

1. 在“选择大小”中，根据需要筛选列表，选择 VM SKU，然后选择“选择”。   
1. 在“设置”中的“选择公共入站端口”下，选择要打开的端口，然后选择“确定”。  
   > [!NOTE]
   > 例如，选择“RDP (3389)”可在 VM 运行时远程连接到该 VM。
1. 在“摘要”中查看所做的选择，然后选择“确定”以创建虚拟机。   
1. 部署开始后，你将返回到仪表板。 若要查看新虚拟机，请选择“所有资源”。 搜索该虚拟机，然后从搜索结果中选择其名称。 在此处可以访问和管理虚拟机。
   > [!NOTE]
   > 完整部署和启动 VM 可能需要几分钟的时间。 VM 可供使用之后，[状态](/azure/virtual-machines/windows/states-lifecycle)将更改为“正在运行”。

::: moniker-end

::: moniker range="<=azs-1901"
## <a name="deploy-a-virtual-machine-resource-1901-and-earlier"></a>部署虚拟机资源（1901 和更低版本）

在用户门户中使用新订阅预配虚拟机。

1. 使用用户帐户登录到用户门户。

1. 在仪表板上，选择“+创建资源”>“计算”>“Windows Server 2016 Datacenter Eval”，然后选择“创建”。   

1. 在“基本信息”中提供以下信息：
  
   - 输入**名称**
   - 输入**用户名**
   - 输入**密码**
   - 选择新**订阅**
   - 创建**资源组**（或选择现有的资源组）。 
   - 选择“确定”保存此信息。

1. 在“选择大小”中，选择“A1 标准”，然后选择“选择”  。  
1. 在“设置”中，选择“虚拟网络”。 

1. 在“选择虚拟网络”中，选择“新建”。 

1. 在“创建虚拟网络”中接受所有默认值，然后选择“确定”。 

1. 在“设置”中选择“确定”，以保存网络配置。 

1. 在“摘要”中，选择“确定”创建虚拟机。   

1. 若要查看新虚拟机，请选择“所有资源”。 搜索该虚拟机，然后从搜索结果中选择其名称。
::: moniker-end

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建订阅
> * 创建并部署资源 

接下来，请了解如何为增值服务部署资源提供程序。 使用这些资源提供程序可为计划中的用户提供更多的服务：

- [在 Azure Stack Hub 上提供 SQL](azure-stack-sql-resource-provider.md)
- [在 Azure Stack Hub 上提供 MySQL](azure-stack-mysql-resource-provider.md)
- [在 Azure Stack Hub 上提供应用服务](azure-stack-app-service-overview.md)
- [提供 Azure Stack Hub 上的事件中心](event-hubs-rp-overview.md)
