---
title: 创建 SQL 数据库
titleSuffix: Azure Stack Hub
description: 了解如何创建和管理使用 SQL 资源提供程序适配器预配的 SQL 数据库。
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 0d1e58fffc7bc5fcc6a2c54ad409752314d1d36b
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77697393"
---
# <a name="create-sql-databases"></a>创建 SQL 数据库

可以在用户门户中创建和管理自助服务数据库。 Azure Stack Hub 用户需要一个包含套餐的订阅，该套餐应包含 SQL 数据库服务。

1. 登录到 [Azure Stack Hub](azure-stack-overview.md) 用户门户。

2. 选择“+ 新建”  &gt;“数据 + 存储”  &gt;“SQL Server 数据库”  &gt;“添加”  。

3. 在“创建数据库”下输入所需信息，例如“数据库名称”和“最大大小(MB)”。   

   >[!NOTE]
   >数据库大小必须至少为 64 MB，此大小在部署数据库后可以增加。

   根据需要为环境配置其他设置。

4. 在“创建数据库”下，选择“SKU”。   在“选择 SKU”下，为数据库选择 SKU。 

   ![在 Azure Stack Hub 用户门户中创建数据库。](./media/azure-stack-sql-rp-deploy/newsqldba.png)

   >[!NOTE]
   >向 Azure Stack Hub 添加宿主服务器时，系统会为它们分配 SKU。 将在 SKU 的宿主服务器池中创建数据库。

5. 选择“登录”。 

6. 在“选择登录名”下选择现有登录名，或者选择“+ 创建新登录名”。  

7. 在“新建登录名”下，输入一个名称作为  **数据库登录名**，然后输入一个**密码**。

   >[!NOTE]
   >这些设置是仅为访问此数据库创建的 SQL 身份验证凭据。 登录用户名必须全局唯一。 可以对使用同一 SKU 的其他数据库重用登录设置。

   ![在 Azure Stack Hub 用户门户中创建新的数据库登录](./media/azure-stack-sql-rp-deploy/create-new-login-a.png)

8. 选择“确定”  ，完成数据库的部署。

在“概要”（在数据库部署后显示）下，记下“连接字符串”。   可以在任何需要访问 SQL Server 数据库的应用中使用此字符串。

![检索 SQL Server 数据库的连接字符串](./media/azure-stack-sql-rp-deploy/sql-db-settings-a.png)

## <a name="sql-always-on-databases"></a>SQL Always On 数据库

根据设计，Always On 数据库的处理方式不同于在单独的服务器环境中的情况。 有关详细信息，请参阅 [Azure 虚拟机上的 SQL Server Always On 可用性组简介](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)。

### <a name="verify-sql-always-on-databases"></a>验证 SQL Always On 数据库

以下屏幕捕获显示了如何使用 SQL Server Management Studio 在 SQL Always On 中查看数据库状态。

![SQL Server Management Studio 中的 AlwaysOn 数据库状态](./media/azure-stack-sql-rp-deploy/verify-always-on.png)

AlwaysOn 数据库应显示为“已同步”  且在所有 SQL 实例上可用，并显示在**可用性组**中。 在上一屏幕截图中，数据库示例为 newdb1，其状态为“newdb1 (已同步)”。 

### <a name="delete-an-always-on-database"></a>删除 Always On 数据库

从资源提供程序中删除 SQL AlwaysOn 数据库时，SQL 会从**主**副本和可用性组中删除该数据库。

然后，SQL 会将该数据库置于另一副本中并将其设为“正在还原”  状态，并不删除该数据库（除非被触发）。 如果未删除该数据库，次要副本将转为“未进行同步”  状态。

## <a name="next-steps"></a>后续步骤

了解如何[提供高度可用的 SQL 数据库](azure-stack-tutorial-sql.md)
