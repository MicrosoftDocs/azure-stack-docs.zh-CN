---
title: 在 Azure Stack Hub 中添加 MySQL 宿主服务器 |Microsoft Docs
description: 了解如何通过 MySQL 适配器资源提供程序添加用于预配的 MySQL 宿主服务器。
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
ms.date: 11/06/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: cf721c98b957d95e945d4979865c7d7b5aa169af
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75811203"
---
# <a name="add-mysql-hosting-servers-in-azure-stack-hub"></a>在 Azure Stack Hub 中添加 MySQL 宿主服务器

只要 MySQL 资源提供程序可以连接到实例，你就可以在[Azure Stack 集线器](azure-stack-overview.md)中的虚拟机（vm）上或 Azure Stack 中心环境外的 VM 上托管 MySQL 宿主服务器实例。

> [!NOTE]
> MySQL 资源提供程序应在默认提供程序订阅中创建，而 MySQL 宿主服务器应在可计费的用户订阅中创建。 不应使用资源提供程序服务器来托管用户数据库。

MySQL 版本5.6、5.7 和8.0 可用于宿主服务器。 MySQL RP 不支持 caching_sha2_password 身份验证;将在下一版本中添加。 MySQL 8.0 服务器必须配置为使用 mysql_native_password。 也支持 MariaDB。

## <a name="connect-to-a-mysql-hosting-server"></a>连接到 MySQL 宿主服务器

请确保您具有具有系统管理员特权的帐户的凭据。

> [!NOTE]
> 对于 MySQL 8.0 及更高版本，默认情况下不启用远程访问。 你需要创建新的用户帐户，并向 previledge 授予此用户帐户的远程访问权限，然后再将其添加为宿主服务器。

若要添加主机服务器，请执行以下步骤：

1. 以服务管理员身份登录到 Azure Stack 中心管理员门户。
2. 选择“所有服务”。
3. 在 "**管理资源**" 类别下，选择 " **MySQL 宿主服务器** >  **+ 添加**"。 将打开 "**添加 MySQL 宿主服务器**" 对话框，如以下屏幕截图所示。

   ![配置 MySQL 宿主服务器](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. 提供 MySQL 服务器实例的连接详细信息。

   * 对于**MySQL 宿主服务器名称**，请提供完全限定的域名（FQDN）或有效的 IPv4 地址。 请勿使用 short VM 名称。
   * Azure Stack 集线器 Marketplace 中提供的 Bitnami MySQL 映像的默认管理员**用户名**为*root*。
   * 如果你不知道根**密码**，请参阅[Bitnami 文档](https://docs.bitnami.com/azure/faq/#how-to-find-application-credentials)以了解如何获取它。
   * 未提供默认 MySQL 实例，因此必须指定**宿主服务器的大小（GB**）。 输入与数据库服务器的容量接近的大小。
   * 保留 "**订阅**" 的默认设置。
   * 对于**资源组**，请创建新的资源组，或使用现有组。

   > [!NOTE]
   > 如果可以通过租户和管理员 Azure 资源管理器访问 MySQL 实例，则可以将其置于资源提供程序的控制之下。 但 MySQL 实例**必须**以独占方式分配给资源提供程序。

5. 选择 " **sku** " 以打开 "**创建 SKU** " 对话框。

   ![创建 MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   SKU**名称**应反映 sku 的属性，以便用户可以将其数据库部署到相应的 sku。

6. 选择 **"确定"** 以创建 SKU。
   > [!NOTE]
   > Sku 最长可能需要一小时才能在门户中显示。 在部署并运行 SKU 之前，无法创建数据库。

7. 在 "**添加 MySQL 宿主服务器**" 下，选择 "**创建**"。

添加服务器时，将它们分配给新的或现有的 SKU，以区分服务产品。 例如，你可以有一个 MySQL enterprise 实例，它提供增加的数据库和自动备份。 你可以为组织中的不同部门保留这一高性能服务器。

## <a name="security-considerations-for-mysql"></a>MySQL 的安全注意事项

以下信息适用于 RP 和 MySQL 宿主服务器：

* 确保所有宿主服务器都配置为使用 TLS 1.1 进行通信。 请参阅[将 MySQL 配置为使用加密连接](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html)。
* 使用[透明数据加密](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html)。
* MySQL RP 不支持 caching_sha2_password 身份验证。

## <a name="increase-backend-database-capacity"></a>增加后端数据库容量

可以通过在 Azure Stack 中心门户中部署更多的 MySQL 服务器来增加后端数据库容量。 将这些服务器添加到新的或现有的 SKU。 如果将服务器添加到现有 SKU，请确保服务器特性与 SKU 中的其他服务器的特性相同。

## <a name="sku-notes"></a>SKU 说明
使用描述 SKU 中服务器功能的 SKU 名称，如容量和性能。 该名称用作帮助用户将其数据库部署到相应 SKU 的帮助。 例如，你可以使用 SKU 名称通过以下特性来区分服务产品：
  
* 高容量
* 高性能
* 高可用性

作为最佳做法，SKU 中的所有宿主服务器都应具有相同的资源和性能特征。

不能将 Sku 分配给特定用户或组。

若要编辑 SKU，请 > **MySQL 适配器** > **sku**中转到 "**所有服务**"。 选择要修改的 SKU，进行任何必要的更改，然后单击 "**保存**" 以保存更改。 

若要删除不再需要的 SKU，请在 > **MySQL 适配器** > **sku**中转到 "**所有服务**"。 右键单击 SKU 名称，然后选择 "**删除**" 将其删除。

> [!IMPORTANT]
> 最长可能需要一小时才能在用户门户中使用新 Sku。

## <a name="make-mysql-database-servers-available-to-your-users"></a>使 MySQL 数据库服务器可供用户使用

创建计划和产品/服务，使 MySQL 数据库服务器可供用户使用。 将 Mysqladapter.dbadapter 服务添加到计划并创建新的配额。 MySQL 不允许限制数据库的大小。

> [!IMPORTANT]
> 可能需要长达两个小时的时间，新配额才能在用户门户中使用，或在强制更改配额之前出现。

## <a name="next-steps"></a>后续步骤

[创建 MySQL 数据库](azure-stack-mysql-resource-provider-databases.md)
