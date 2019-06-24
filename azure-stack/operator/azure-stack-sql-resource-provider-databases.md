---
title: 在 Azure Stack 上使用 SQL Adapter 资源提供程序提供的数据库 | Microsoft Docs
description: 如何创建和管理使用 SQL 适配器资源提供程序预配的 SQL 数据库
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: a8ebc50be9b1c3e1eaae491b6d8f8916f900e500
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308461"
---
# <a name="create-sql-databases"></a>创建 SQL 数据库

可以在用户门户中创建和管理自助服务数据库。 Azure Stack 用户需要一个包含套餐的订阅，该套餐应包含 SQL 数据库服务。

1. 登录到 [Azure Stack](azure-stack-overview.md) 用户门户。

2. 选择“+ 新建”  &gt;“数据 + 存储”  &gt;“SQL Server 数据库”  &gt;“添加”  。

3. 在“创建数据库”下输入所需信息，例如“数据库名称”和“最大大小(MB)”。   

   >[!NOTE]
   >数据库大小必须至少为 64 MB，此数字在部署数据库后可以提高。

   根据需要为环境配置其他设置。

4. 在“创建数据库”下，选择“SKU”。   在“选择 SKU”下，为数据库选择 SKU。 

   ![创建数据库](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >向 Azure Stack 添加宿主服务器时，系统会为它们分配 SKU。 将在 SKU 的宿主服务器池中创建数据库。

5. 选择“登录名”。 
6. 在“选择登录名”下选择现有登录名，或者选择“+ 创建新登录名”。  
7. 在“新建登录名”下，输入一个名称作为  **数据库登录名**，然后输入一个**密码**。

   >[!NOTE]
   >这些设置是仅为访问此数据库创建的 SQL 身份验证凭据。 登录用户名必须全局唯一。 可以对使用同一 SKU 的其他数据库重用登录设置。

   ![新建数据库用户名](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. 选择“确定”  ，完成数据库的部署。

在“概要”（在数据库部署后显示）下，记下“连接字符串”。   可以将此字符串用于任何需要访问 SQL Server 数据库的应用程序。

![检索连接字符串](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>SQL Always On 数据库

根据设计，Always On 数据库的处理方式不同于在单独的服务器环境中的情况。 有关详细信息，请参阅 [Azure 虚拟机上的 SQL Server Always On 可用性组简介](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)。

### <a name="verify-sql-always-on-databases"></a>验证 SQL Always On 数据库

以下屏幕捕获显示了如何使用 SQL Server Management Studio 在 SQL Always On 中查看数据库状态。

![AlwaysOn 数据库状态](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

AlwaysOn 数据库应显示为已进行同步，在所有 SQL 实例中可用并显示在“可用性组”中。 在上一屏幕捕获中，数据库示例为 newdb1，其状态为“newdb1 (已同步)”。 

### <a name="delete-an-alwayson-database"></a>删除 AlwaysOn 数据库

从资源提供程序中删除 SQL AlwaysOn 数据库时，SQL 会从主副本和可用性组中删除数据库。

然后，SQL 会将该数据库置于另一副本中并让其处于“正在还原”状态，在受到触发之前并不删除该数据库。 如果未删除数据库，次要副本将转为“未进行同步”状态。

## <a name="next-steps"></a>后续步骤

[维护 SQL Server 资源提供程序](azure-stack-sql-resource-provider-maintain.md)
