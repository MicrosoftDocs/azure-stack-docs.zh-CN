---
title: 在 Azure Stack Hub 中添加 MySQL 宿主服务器
description: 了解如何添加 MySQL 宿主服务器以通过 MySQL 适配器资源提供程序进行预配。
author: bryanla
ms.topic: article
ms.date: 11/06/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: bbf96c0716d6bb9fdfca7ce0b52268281e6169c6
ms.sourcegitcommit: 980be7813e6f39fb59926174a5d3e0d392b04293
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414157"
---
# <a name="add-mysql-hosting-servers-in-azure-stack-hub"></a>在 Azure Stack Hub 中添加 MySQL 宿主服务器

可以在 [Azure Stack Hub](azure-stack-overview.md) 中的虚拟机 (VM) 上或者在 Azure Stack Hub 环境外部的 VM 上托管 MySQL 宿主服务器实例，前提是 MySQL 资源提供程序能够连接到该实例。

> [!NOTE]
> MySQL 资源提供程序应在默认提供程序订阅中创建，而 MySQL 宿主服务器则应在可计费用户订阅中创建。 资源提供程序服务器不应用于托管用户数据库。

可以将 MySQL 版本 5.6、5.7 和 8.0 用于宿主服务器。 MySQL RP 不支持 caching_sha2_password 身份验证。 必须将 MySQL 8.0 服务器配置为使用 mysql_native_password。

## <a name="prepare-a-mysql-hosting-server"></a>准备 MySQL 宿主服务器

### <a name="create-a-network-security-group-rule"></a>创建网络安全组规则

默认情况下，不会为 MySQL 配置到主机 VM 的公共访问权限。 要使 Azure Stack Hub MySQL 资源提供程序能够连接和管理 MySQL 服务器，需要创建入站网络安全组 (NSG) 规则。

1. 在管理员门户中，转到部署 MySQL 服务器时创建的资源组，并选择网络安全组 (default-subnet-sg)：

   ![在 Azure Stack Hub 管理员门户中选择网络安全组](media/azure-stack-tutorial-mysqlrp/img6.png)

2. 选择“入站安全规则”，并选择“添加” 。

    在“目标端口范围”中输入“3306”，并在“名称”和“说明”字段中提供说明   。

   ![开门](media/azure-stack-tutorial-mysqlrp/img7.png)

3. 选择“添加”以关闭“入站安全规则”对话框。

### <a name="configure-external-access-to-the-mysql-hosting-server"></a>配置对 MySQL 宿主服务器的外部访问

必须先启用外部访问，然后才能将 MySQL 服务器添加为 Azure Stack Hub MySQL 服务器主机。 以 Azure Stack Hub 市场中提供的 Bitnami MySQL 为例，你可以采取以下步骤配置外部访问。

1. 使用 SSH 客户端（此示例使用 [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)）从可以访问公共 IP 的计算机登录 MySQL 服务器。

    使用公共 IP 并使用你先前创建的用户名和应用程序密码（不带特殊字符）登录到 VM。

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)

2. 在 SSH 客户端窗口中，使用以下命令确保 bitnami 服务处于活动状态且正在运行。 出现提示时，再次提供 bitnami 密码：

   `sudo service bitnami status`

   ![检查 bitnami 服务](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. 如果 MySQL 宿主服务器是 8.0 或更高版本，则需要将身份验证方法更改为“mysql_native_password”。 如果 MySQL 版本低于 8.0，则可以跳过此步骤。

   以 Bitnami MySQL 为例，配置文件位于“/opt/bitnami/mysql/conf/my.cnf”下。 将属性“default_authentication_plugin”设置为值“mysql_native_password” 。
   ```
   [mysqld]
   default_authentication_plugin=mysql_native_password
   ```
   重新启动 bitnami 服务并确保 bitnami 服务正常运行。
   ```console
   sudo service bitnami restart
   sudo service bitnami status
   ```

4. 创建一个远程访问用户帐户，供 Azure Stack Hub MySQL 宿主服务器用于连接到 MySQL。

    运行以下命令以根用户身份登录 MySQL，使用记录在“~/bitnami_credentials”中的根密码。 创建一个新的管理员用户，并根据环境需要替换 \<username\> 和 \<password\> 。 在此示例中，创建的用户名为 **sqlsa** ，并使用强密码：

   ```sql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```

   ![创建管理员用户](media/azure-stack-tutorial-mysqlrp/bitnami3.png)

5. 确保已创建的 sql 用户“sqlsa”的插件是“mysql_native_password”，然后退出 SSH 客户端 。
   
   ```sql
   SELECT user,host,plugin from mysql.user;
   ```
6. 记录新的 MySQL 用户信息。

   Azure Stack Hub 操作员使用此 MySQL 服务器创建 MySQL 宿主服务器时，将使用此用户名和密码。


## <a name="connect-to-a-mysql-hosting-server"></a>连接到 MySQL 宿主服务器

确保已准备好拥有管理特权的帐户的凭据。

> [!NOTE]
> 对于 MySQL 8.0 及更高版本，默认情况下不启用远程访问。 需要创建新的用户帐户，并授予此用户帐户远程访问权限，然后再将其添加为宿主服务器。

若要添加宿主服务器，请执行以下步骤：

1. 以服务管理员身份登录到 Azure Stack Hub 管理员门户。
2. 选择“所有服务”。
3. 在“管理资源”类别下，选择“MySQL 宿主服务器” > “+添加”。 此时会打开“添加 MySQL 宿主服务器”对话框，如以下屏幕捕获所示。

   ![配置 MySQL 宿主服务器](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. 提供 MySQL 服务器实例的连接详细信息。

   * 对于“MySQL 宿主服务器名称”，请提供完全限定域名 (FQDN) 或有效的 IPv4 地址。 请勿使用短 VM 名称。
   * Azure Stack Hub 市场中提供的 Bitnami MySQL 映像的默认管理员 **用户名** 为 *root* 。
   * 如果不知道 root 的 **密码** ，请参阅 [Bitnami 文档](https://docs.bitnami.com/azure/faq/#how-to-find-application-credentials)，了解如何获取它。
   * 未提供默认的 MySQL 实例，因此需指定“宿主服务器的大小(GB)”。 输入接近数据库服务器容量的大小。
   * 保留“订阅”的默认设置。
   * 对于“资源组”，请创建新组或使用现有组。

   > [!NOTE]
   > 如果租户和管理 Azure 资源管理器可以访问 MySQL 实例，则可让资源提供程序控制此实例。 但是， **必须** 专门将 SQL 实例分配给资源提供程序。

5. 选择“SKU”，打开“创建 SKU”对话框。 

   ![创建 MySQL SKU](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   SKU **名称** 应反映 SKU 的属性，这样用户就能将其数据库部署到适当的 SKU。

6. 选择“确定”以创建 SKU。
   > [!NOTE]
   > SKU 最长可能需要在一小时后才显示在门户中。 在部署并运行 SKU 之前，无法创建数据库。

7. 在“添加 MySQL 宿主服务器”下，选择“创建”。 

添加服务器时，请将它们分配给新的或现有的 SKU，以区分服务套餐。 例如，可以通过一个 MySQL 企业实例来提供增加的数据库和自动备份。 可以将此高性能服务器保留给组织中的不同部门。

## <a name="security-considerations-for-mysql"></a>MySQL 安全注意事项

以下信息适用于 RP 和 MySQL 宿主服务器：

* 确保将所有宿主服务器配置为使用 TLS 1.1 进行通信。 请参阅 [Configuring MySQL to Use Encrypted Connections](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html)（将 MySQL 配置为使用加密连接）。
* 部署[透明数据加密](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html)。
* MySQL RP 不支持 caching_sha2_password 身份验证。

## <a name="increase-backend-database-capacity"></a>提高后端数据库容量

可以在 Azure Stack Hub 门户中部署更多的 MySQL 服务器，以便提高后端数据库容量。 将这些服务器添加到新的或现有的 SKU。 如果向现有的 SKU 添加服务器，请确保该服务器的特征与 SKU 中其他服务器的特征相同。

## <a name="sku-notes"></a>SKU 说明
使用可以描述 SKU 中服务器容量（例如容量和性能）的 SKU 名称。 名称可以协助用户将其数据库部署到相应的 SKU。 例如，可以使用 SKU 名称通过以下特征来区分服务产品/服务：
  
* 高容量
* 高性能
* 高可用性

最佳做法是使 SKU 中的所有宿主服务器具有相同的资源和性能特征。

无法将 SKU 分配到特定的用户或组。

若要编辑某个 SKU，请转到“所有服务” > “MySQL 适配器” > “SKU”。   选择要修改的 SKU，进行任何必要的更改，然后单击“保存”以保存更改。 

若要删除不再需要的 SKU，请转到“所有服务” > “MySQL 适配器” > “SKU”。   右键单击 SKU 名称，然后选择“删除”将其删除。

> [!IMPORTANT]
> 可能需要长达一小时的时间新的 SKU 才会在用户门户中可用。

## <a name="make-mysql-database-servers-available-to-your-users"></a>将 MySQL 数据库服务器提供给用户使用

创建计划和套餐，使用户能够使用 MySQL 数据库服务器。 将 Microsoft.MySqlAdapter 服务添加到计划，并创建新配额。 MySQL 不允许限制数据库的大小。

> [!IMPORTANT]
> 在用户门户中出现可用的新配额或者强制实施更改的配额可能需要长达两小时的时间。

## <a name="next-steps"></a>后续步骤

[创建 MySQL 数据库](azure-stack-mysql-resource-provider-databases.md)
