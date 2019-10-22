---
title: 在 Azure Stack 中创建 MySQL 数据库 |Microsoft Docs
description: 了解如何创建和管理使用 Azure Stack 中的 MySQL 适配器资源提供程序预配的 MySQL 数据库。
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 9daaea395ee28da79da1d6fd553d2e0b30e28156
ms.sourcegitcommit: a23b80b57668615c341c370b70d0a106a37a02da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2019
ms.locfileid: "72682232"
---
# <a name="create-mysql-databases-in-azure-stack"></a>在 Azure Stack 中创建 MySQL 数据库
订阅了包含 MySQL 数据库服务的产品/服务的 Azure Stack 用户可以在用户门户中创建和管理自助服务 MySQL 数据库。

## <a name="create-a-mysql-database"></a>创建 MySQL 数据库

1. 登录到 Azure Stack 用户门户。
2. 选择 " **+ 创建资源**"  > **数据 + 存储** > **MySQL 数据库** >  "**添加**"。
3. 在 "**创建 MySQL 数据库**" 下，输入数据库名称，并根据环境要求配置其他设置。

    ![创建测试 MySQL 数据库](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. 在 "**创建数据库**" 下，选择**SKU**。 在 "**选择 MYSQL sku**" 下，选择数据库的 SKU。

    ![选择 MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >将宿主服务器添加到 Azure Stack 时，会为它们分配 SKU。 数据库在 SKU 中的宿主服务器池中创建。

5. 在 "**登录**" 下，选择 "***配置所需设置***"。
6. 在 "**选择登录名**" 下，可以选择现有的登录名，也可以选择 " **+ 创建新登录**名" 来设置新登录名。  输入**数据库登录**名和**密码**，然后选择 **"确定"** 。

    ![创建新的数据库登录名](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >在 MySQL 5.7 中，数据库登录名的长度不能超过32个字符。 在早期版本中，不能超过16个字符。

7. 选择 "**创建**" 以完成数据库的设置。

部署数据库后，请记下**Essentials**下的**连接字符串**。 你可以在任何需要访问 MySQL 数据库的应用程序中使用此字符串。

![获取 MySQL 数据库的连接字符串](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>更新管理密码

可以通过在 MySQL server 实例上更改密码来修改密码。

1. 选择  > **MySQL 宿主服务器**的**管理资源**。 选择宿主服务器。
2. 在 "**设置**" 下，选择 "**密码**"。
3. 在 "**密码**" 下，输入新密码，然后选择 "**保存**"。

![更新管理员密码](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>后续步骤

了解如何[提供高度可用的 MySQL 数据库](azure-stack-tutorial-mysql.md)。
