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
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77697393"
---
# <a name="create-sql-databases"></a>创建 SQL 数据库

可在用户门户中创建和管理自助服务数据库。 Azure Stack 集线器用户需要具有包含 SQL 数据库服务的产品/服务的订阅。

1. 登录到[Azure Stack 集线器](azure-stack-overview.md)用户门户。

2. 选择 " **+ 新建**" &gt;**数据 + 存储**&gt; **SQL Server 数据库**&gt; "**添加**"。

3. 在 "**创建数据库**" 下，输入所需的信息，如**数据库名称**和**最大大小（MB**）。

   >[!NOTE]
   >数据库的大小必须至少为 64 MB，这可以在部署数据库后增加。

   根据环境的需要配置其他设置。

4. 在 "**创建数据库**" 下，选择**SKU**。 在 "**选择 sku**" 下，选择数据库的 sku。

   ![在 Azure Stack Hub 用户门户中创建数据库。](./media/azure-stack-sql-rp-deploy/newsqldba.png)

   >[!NOTE]
   >将托管服务器添加到 Azure Stack 中心时，会为它们分配 SKU。 数据库在 SKU 中的宿主服务器池中创建。

5. 选择 "**登录**"。

6. 在 "**选择登录名**" 下，选择现有登录名，或选择 " **+ 创建新登录名**"。

7. 在 "**新建登录**名" 下，输入**数据库登录**名和**密码**。

   >[!NOTE]
   >这些设置是为仅对此数据库的访问而创建的 SQL 身份验证凭据。 登录名用户名必须是全局唯一的。 可以对使用相同 SKU 的其他数据库重用登录设置。

   ![在 Azure Stack Hub 用户门户中创建新的数据库登录名](./media/azure-stack-sql-rp-deploy/create-new-login-a.png)

8. 选择 **"确定"** 完成数据库的部署。

在部署数据库后显示的 " **Essentials**" 下，记下**连接字符串**。 您可以在任何需要访问 SQL Server 数据库的应用程序中使用此字符串。

![检索 SQL Server 数据库的连接字符串](./media/azure-stack-sql-rp-deploy/sql-db-settings-a.png)

## <a name="sql-always-on-databases"></a>SQL Always On 数据库

按照设计，Always On 数据库的处理方式与独立服务器环境中不同。 有关详细信息，请参阅[Azure 虚拟机上的 SQL Server Always On 可用性组简介](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)。

### <a name="verify-sql-always-on-databases"></a>验证 SQL Always On 数据库

以下屏幕截图显示了如何使用 SQL Server Management Studio 来查看 SQL Always On 中的数据库状态。

![SQL Server Management Studio 中的 AlwaysOn 数据库状态](./media/azure-stack-sql-rp-deploy/verify-always-on.png)

Always On 数据库应显示为已**同步**并且可用于所有 SQL 实例，并显示在**可用性组**中。 在前面的屏幕截图中，数据库示例为 newdb1，其状态为**newdb1 （已同步）** 。

### <a name="delete-an-always-on-database"></a>删除 Always On 数据库

从资源提供程序中删除 SQL Always On 数据库时，SQL 会从**主**副本和可用性组中删除该数据库。

然后，SQL 会将数据库置于其他副本上的**还原**状态，并且不会删除数据库（除非触发）。 如果不删除数据库，则辅助副本将进入 "**未同步**" 状态。

## <a name="next-steps"></a>后续步骤

了解如何[提供高度可用的 SQL 数据库](azure-stack-tutorial-sql.md)
