---
title: 测试 Azure Stack 中心服务产品。
description: 了解如何通过创建订阅和部署资源来测试服务产品。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: tutorial
ms.date: 10/13/2019
ms.reviewer: shriramnat
ms.lastreviewed: 10/06/2019
ms.openlocfilehash: fd14a6099c9906a0b37c8227164b213235dcfee3
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820927"
---
# <a name="tutorial-test-a-service-offering"></a>教程：测试服务产品

在上一教程中，你为用户创建了一个产品/服务。 本教程演示如何通过使用它创建订阅来测试该产品/服务。 然后创建资源并将其部署到由订阅授权的基础服务。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 创建订阅
> * 创建和部署资源

## <a name="prerequisites"></a>必备组件

开始本教程之前，必须先完成以下先决条件：

- 完成为[用户提供服务](tutorial-offer-services.md)教程教程。 在本教程中，你将了解如何创建本教程使用的产品/服务。

- 你在本教程中订阅的产品/服务可用于部署虚拟机（VM）资源。 若要测试 VM 部署，必须先通过从 Azure Marketplace 下载 VM 映像，使其在 Azure Stack Hub Marketplace 中可用。 有关说明，请参阅[将 marketplace 项从 Azure 下载到 Azure Stack 中心](azure-stack-download-azure-marketplace-item.md)。 

## <a name="subscribe-to-the-offer"></a>订阅产品/服务

1. 使用用户帐户登录到用户门户 

   - 对于集成系统，URL 根据操作员的区域和外部域名，使用格式 https://portal.&lt ;*区域*&gt; 。&lt;*FQDN*&gt; 。
   - 如果使用的是 Azure Stack 开发工具包，则门户地址为 [https://portal.local.azurestack.external](https://portal.local.azurestack.external )。

1. 选择 "**获取订阅**" 磁贴。

   ![获取订阅](media/tutorial-test-offer/1-get-subscription.png)

1. 在 "**获取订阅**" 的 "**显示名称**" 字段中，输入新订阅的名称。 选择 "**产品/服务**"，然后从 "**选择产品/服务**" 列表中选择你在上一教程中创建的产品/服务。 选择“创建”。

   ![创建套餐](media/tutorial-test-offer/2-create-subscription.png)

1. 若要查看订阅，请选择 "**所有服务**"，然后在 "**常规**" 类别下选择 "**订阅**"。 选择新订阅以查看与其关联的产品/服务及其属性。

   >[!NOTE]
   >订阅产品/服务后，你可能必须刷新门户以查看哪些服务是新订阅的一部分。

::: moniker range=">=azs-1902"
## <a name="deploy-a-storage-account-resource"></a>部署存储帐户资源

在用户门户中，你可以使用在上一部分中创建的订阅来预配存储帐户。

1. 使用用户帐户登录到用户门户。

1. 选择 " **+ 创建资源**" >**数据 + 存储**>**存储帐户-blob、文件、表、队列**。

1. 在 "**创建存储帐户**" 中，提供以下信息：
  
   - 输入**名称**
   - 选择新**订阅**
   - 选择一个**资源组**（或创建一个）。 
   - 选择“创建”以创建存储帐户。

1. 部署开始后，返回到仪表板。 若要查看新的存储帐户，请选择 "**所有资源**"。 搜索存储帐户，然后从搜索结果中选择它的名称。 在此处，你可以管理存储帐户及其内容。

## <a name="deploy-a-virtual-machine-resource"></a>部署虚拟机资源

在用户门户中，你可以使用在上一部分中创建的订阅来预配虚拟机。

1. 使用用户帐户登录到用户门户。

1. 选择 " **+ 创建资源**" >**计算**> **\<映像名称\>** ，其中 "映像名称" 是你在 "先决条件" 中下载的虚拟机的名称。
1. 在 "**创建虚拟机**/**基础知识**中，提供以下信息：
  
   - 输入 VM 的“名称”。
   - 输入管理员帐户的**用户名**。
   - 对于 Linux Vm，请选择 "密码" 作为 "**身份验证类型**"。
   - 为管理员帐户输入密码，并为 "**确认密码**" 输入相同的**密码**。
   - 选择新**订阅**。
   - 选择一个**资源组**（或创建一个）。 
   - 选择 **"确定"** 以验证此信息并继续。

1. 在 "**选择大小**" 中，根据需要筛选列表，选择 VM SKU，然后选择 "**选择**"。  
1. 在 "**设置**" 中，指定要在 "**选择公共入站端口**" 下打开的端口，然后选择 **"确定"** 。
   > [!NOTE]
   > 例如，选择 "RDP （3389）" 可在 VM 运行时远程连接到该 VM。
1. 在 "**摘要**" 中，查看你的选择，然后选择 **"确定"** 以创建虚拟机。  
1. 部署开始后，返回到仪表板。 若要查看新虚拟机，请选择 "**所有资源**"。 搜索虚拟机，然后从搜索结果中选择其名称。 在此处，你可以访问和管理虚拟机。
   > [!NOTE]
   > 完全部署和启动 VM 可能需要几分钟时间。 VM 准备好使用后，[状态](/azure/virtual-machines/windows/states-lifecycle)将更改为 "正在运行"。

::: moniker-end

::: moniker range="<=azs-1901"
## <a name="deploy-a-virtual-machine-resource-1901-and-earlier"></a>部署虚拟机资源（1901及更早版本）

在用户门户中，你可以使用新订阅预配虚拟机。

1. 使用用户帐户登录到用户门户。

1. 在仪表板上，选择 " **+ 创建资源**" >**计算**"> **Windows Server 2016 Datacenter Eval**"，然后选择 "**创建**"。

1. 在 "**基本**信息" 中，提供以下信息：
  
   - 输入**名称**
   - 输入**用户名**
   - 输入**密码**
   - 选择新**订阅**
   - 创建资源组（或选择现有的**资源组**）。 
   - 选择 **"确定"** 保存此信息。

1. 在 "**选择大小**" 中，选择 " **A1 标准**"，然后**选择**。  
1. 在 "**设置**" 中，选择 "**虚拟网络**"。

1. 在 "**选择虚拟网络**" 中，选择 "**新建**"。

1. 在 "**创建虚拟网络**" 中，接受所有默认值，然后选择 **"确定"** 。

1. 在 "**设置**" 中选择 **"确定"** 以保存网络配置。

1. 在 "**摘要**" 中，选择 **"确定"** 以创建虚拟机。  

1. 若要查看新虚拟机，请选择 "**所有资源**"。 搜索虚拟机，然后从搜索结果中选择其名称。
::: moniker-end

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建订阅
> * 创建和部署资源 

接下来，了解如何为增值服务部署资源提供程序。 它们允许你为计划中的用户提供更多服务：

- [在 Azure Stack 集线器上提供 SQL](azure-stack-sql-resource-provider.md)
- [Azure Stack 中心提供 MySQL](azure-stack-mysql-resource-provider.md)
- [在 Azure Stack 中心提供应用服务](azure-stack-app-service-overview.md)
