---
title: 在 Azure Stack Hub 中创建 MySQL 数据库
description: 了解如何在 Azure Stack Hub 中创建和管理使用 MySQL 适配器资源提供程序预配的 MySQL 数据库。
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 2da344a5699357d008fcc4494992cfcc67047971
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77698872"
---
# <a name="create-mysql-databases-in-azure-stack-hub"></a>在 Azure Stack Hub 中创建 MySQL 数据库
订阅了包含 MySQL 数据库服务的套餐的 Azure Stack Hub 用户可以在用户门户中创建和管理自助式 MySQL 数据库。

## <a name="create-a-mysql-database"></a>创建 MySQL 数据库

1. 登录到 Azure Stack Hub 用户门户。
2. 选择“+ 创建资源”   > “数据 + 存储”   > “MySQL 数据库”   > “添加”  。
3. 在“创建 MySQL 数据库”下输入“数据库名称”，并根据需要为环境配置其他设置。 

    ![创建 MySQL 测试数据库](./media/azure-stack-mysql-rp-deploy/mysql-create-db-a.png)

4. 在“创建数据库”下，选择“SKU”。   在“选择 MySQL SKU”下，为数据库选取 SKU。 

    ![选择 MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >向 Azure Stack Hub 添加宿主服务器时，系统会为它们分配 SKU。 将在 SKU 的宿主服务器池中创建数据库。

5. 在“登录名”  下选择“配置所需的设置”。
6. 在“选择登录名”下，可以选择现有登录名，或者选择“+ 创建新登录名”，以便设置新登录名。    输入**数据库登录**名和**密码**，然后选择“确定”。 

    ![新建数据库用户名](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >MySQL 5.7 中的数据库登录名长度不可超过 32 个字符。 在早期版本中不可超过 16 个字符。

7. 选择“创建”，完成数据库的设置。 

部署数据库以后，请记下“概要”下的“连接字符串”。   可以将此字符串用于任何需要访问 MySQL 数据库的应用程序。

![获取 MySQL 数据库的连接字符串](./media/azure-stack-mysql-rp-deploy/mysql-db-created-a.png)

## <a name="update-the-administrative-password"></a>更新管理密码

若要修改密码，可以在 MySQL 服务器实例上更改密码。

1. 选择“管理资源” **“MySQL 宿主服务器”。**  >   选择宿主服务器。
2. 在“设置”下，选择“密码”。  
3. 在“密码”下输入  新密码，然后选择“保存”。 

![更新管理密码](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>后续步骤

了解如何[提供高度可用的 MySQL 数据库](azure-stack-tutorial-mysql.md)。
