---
title: 将 SQL 数据库提供给 Azure Stack 用户使用 | Microsoft Docs
description: 有关安装 SQL Server 资源提供程序并创建套餐，使 Azure Stack 用户能够创建 SQL 数据库的教程。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: quying
ms.lastreviewed: 11/05/2018
ms.custom: mvc
ms.openlocfilehash: cc3af256adbcea8e6055b237945dc2286762437e
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267999"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>教程：将 SQL 数据库提供给 Azure Stack 用户使用

Azure Stack 云管理员可以创建套餐，使用户（租户）能够创建 SQL 数据库，以配合其云原生应用、网站和工作负荷使用。 通过向用户提供这些基于云的按需自定义数据库的访问权限，可以节省用户的时间和资源。 若要设置此功能，需要：

> [!div class="checklist"]
> * 部署 SQL Server 资源提供程序
> * 创建产品
> * 测试产品/服务

## <a name="deploy-the-sql-server-resource-provider"></a>部署 SQL Server 资源提供程序

[在 Azure Stack 中使用 SQL 数据库](azure-stack-sql-resource-provider-deploy.md)一文中详细介绍了部署过程，该过程包括以下主要步骤：

1. [部署 SQL 资源提供程序](azure-stack-sql-resource-provider-deploy.md)。
2. [验证部署](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal)。
3. 通过连接到宿主 SQL 服务器来提供容量。 有关详细信息，请参阅[添加宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>创建产品

1.  [设置配额](azure-stack-plan-offer-quota-overview.md )并将其命名为 *SQLServerQuota*。 在“命名空间”字段中选择“Microsoft.SQLAdapter”。  
2.  [创建计划](azure-stack-create-plan.md)。 将计划命名为 *TestSQLServerPlan*，并选择“Microsoft.SQLAdapter”服务和“SQLServerQuota”配额。  

    > [!NOTE]
    > 若要让用户创建其他应用，可能需要在计划中包含其他服务。 例如，Azure Functions 要求计划中必须包含 **Microsoft.Storage** 服务，而 Wordpress 则要求包含 **Microsoft.MySQLAdapter**。

3.  [创建套餐](azure-stack-create-offer.md)，将其命名为 **TestSQLServerOffer**，然后选择“TestSQLServerPlan”计划。 

## <a name="test-the-offer"></a>测试产品/服务

部署 SQL Server 资源提供程序并创建套餐后，可以用户身份登录并订阅该产品/服务，然后创建数据库。

### <a name="subscribe-to-the-offer"></a>订阅套餐

1. 以租户身份登录到 Azure Stack 门户 (https://portal.local.azurestack.external)。
2. 选择“获取订阅”，然后在“显示名称”下输入 **TestSQLServerSubscription**。  
3. 选择“选择套餐” > “TestSQLServerOffer” > “创建”。   
4. 选择“所有服务” > “订阅” > “TestSQLServerSubscription” > “资源提供程序”。    
5. 选择“Microsoft.SQLAdapter”提供程序旁边的“注册”。  

### <a name="create-a-sql-database"></a>创建 SQL 数据库

1. 选择“+” > “数据 + 存储” > “SQL 数据库”。   
2. 保留默认值，或者对以下字段使用以下示例值：
    - **数据库名称**：SQLdb
    - **最大大小(MB)** ：100
    - **订阅**：TestSQLOffer
    - **资源组**：SQL-RG
3. 选择“登录设置”，输入数据库的凭据，然后选择“确定”。  
4. 选择“SKU”，选择为 SQL 宿主服务器创建的 SQL SKU，然后选择“确定”。  
5. 选择“创建”  。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 部署 SQL Server 资源提供程序
> * 创建产品
> * 测试产品/服务

转到下一教程，了解如何执行以下操作：

> [!div class="nextstepaction"]
> [将 Web、移动和 API 应用提供给用户使用]( azure-stack-tutorial-app-service.md)