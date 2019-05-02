---
title: 将 Web 和 API 应用提供给 Azure Stack 用户使用 | Microsoft Docs
description: 有关安装应用服务资源提供程序并创建套餐，使 Azure Stack 用户能够创建 Web 和 API 应用的教程。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: jeffgilb
ms.reviewer: anwestg
ms.custom: mvc
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: aa897867c4f9735369cc0dec1c4d8527273e2f7f
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64293153"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>教程：将 Web 和 API 应用提供给 Azure Stack 用户使用

Azure Stack 云管理员可以创建套餐，使用户（租户）能够创建 Azure Functions、Web 和 API 应用程序。 通过向用户提供这些基于云的按需应用程序的访问权限，可以节省用户的时间和资源。

若要设置此功能，需要：

> [!div class="checklist"]
> * 部署应用服务资源提供程序
> * 创建产品
> * 测试产品/服务

## <a name="deploy-the-app-service-resource-provider"></a>部署应用服务资源提供程序

1. [准备 Azure Stack 开发工具包主机](azure-stack-app-service-before-you-get-started.md)。 这包括部署 SQL Server 资源提供程序（创建某些应用的先决条件）。
2. [下载安装程序和帮助器脚本](azure-stack-app-service-deploy.md)。
3. [运行帮助器脚本以创建所需的证书](azure-stack-app-service-deploy.md)。
4. [安装应用服务资源提供程序](azure-stack-app-service-deploy.md)（需要在数小时之后才能安装完资源提供程序并显示所有辅助角色）。
5. [验证安装](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation)。

## <a name="create-an-offer"></a>创建产品

例如，可以创建一个产品/服务来让用户创建 DNN Web 内容管理系统。 这需要用到已通过安装 SQL Server 资源提供程序启用的 SQL Server 服务。

1.  [设置配额](azure-stack-plan-offer-quota-overview.md )并将其命名为 *AppServiceQuota*。 在“命名空间”字段中选择“Microsoft.Web”。
2.  [创建计划](azure-stack-create-plan.md)。 将计划命名为 *TestAppServicePlan*，并依次选择“Microsoft.SQL”服务和“AppService 配额”配额。

    > [!NOTE]
    > 若要让用户创建其他应用，可能需要在计划中包含其他服务。 例如，Azure Functions 要求计划中必须包含 **Microsoft.Storage** 服务，而 Wordpress 则要求包含 **Microsoft.MySQL**。

3.  [创建套餐](azure-stack-create-offer.md)，将其命名为 **TestAppServiceOffer**，然后选择“TestAppServicePlan”计划。

## <a name="test-the-offer"></a>测试产品/服务

部署应用服务资源提供程序并创建套餐后，可以用户身份登录并订阅该套餐，然后创建应用。

本示例将创建一个 DNN 平台内容管理系统。 先创建 SQL 数据库，然后再创建 DNN Web 应用。

### <a name="subscribe-to-the-offer"></a>订阅套餐

1. 以租户身份登录到 Azure Stack 门户 (https://portal.local.azurestack.external)。
2. 选择“获取订阅”，在“显示名称”下输入 **TestAppServiceSubscription**  >  选择“选择套餐” > “TestAppServiceOffer” > “创建”。

### <a name="create-a-sql-database"></a>创建 SQL 数据库

1. 选择“+” > “数据 + 存储” > “SQL 数据库”。
2. 将以下字段除外的其他字段保留默认值：

    - **数据库名称**：DNNdb
    - **最大大小(MB)**：100
    - **订阅**：TestAppServiceOffer
    - **资源组**：DNN-RG

3. 选择“登录设置”，输入数据库的凭据，然后选择“确定”。 本教程稍后要用到这些凭据。
4. 在“SKU”下，选择为 SQL 宿主服务器创建的 SQL SKU，然后选择“确定”。
5. 选择“创建”。

### <a name="create-a-dnn-app"></a>创建 DNN 应用

1. 选择“+” > “全部查看” > “DNN 平台预览” > “创建”。
2. 在“应用名称”下输入 *DNNapp*，在“订阅”下选择“TestAppServiceOffer”。
3. 选择“配置所需的设置” > “新建”，输入**应用服务计划**的名称。
4. 选择“定价层” > “F1 免费” > “选择” > “确定”。
5. 选择“数据库”并输入前面创建的 SQL 数据库的凭据。
6. 选择“创建”。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 部署应用服务资源提供程序
> * 创建产品
> * 测试产品/服务

转到下一教程，了解如何执行以下操作：

> [!div class="nextstepaction"]
> [将应用部署到 Azure 和 Azure Stack](../user/azure-stack-solution-pipeline.md)
