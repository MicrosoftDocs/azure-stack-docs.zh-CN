---
title: 提供在 Azure Stack 中高度可用的 SQL 数据库 |Microsoft Docs
description: 了解如何使用 Azure Stack 主机计算机和高可用性 SQL AlwaysOn 数据库创建 SQL Server 资源提供程序。
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
ms.date: 02/25/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: 466f5c487dc9ea11b9a925fc89796d8550742c65
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64301832"
---
# <a name="tutorial-offer-highly-available-sql-databases"></a>教程：提供高度可用的 SQL 数据库

Azure Stack 操作员，可以配置承载 SQL Server 数据库服务器 Vm。 SQL 宿主服务器后成功，由创建和管理 Azure Stack，用户已订阅的 SQL 服务可以轻松地创建 SQL 数据库。

本教程演示如何使用 Azure Stack 快速入门模板来创建[SQL Server AlwaysOn 可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017)，将其添加为 Azure Stack SQL 宿主服务器，并创建高度可用的 SQL 数据库。

要学习的知识：

> [!div class="checklist"]
> * 从模板创建一个 SQL Server AlwaysOn 可用性组
> * 创建 Azure Stack SQL 宿主服务器
> * 创建高度可用的 SQL 数据库

在本教程中，两个 VM 的 SQL Server AlwaysOn 可用性组将在创建和配置为使用可用的 Azure Stack marketplace 项。 

在开始本教程中的步骤之前, 确保[SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)已成功安装和 Azure Stack marketplace 中的以下项：

> [!IMPORTANT]
> 要使用的 Azure Stack 快速入门模板需要以下所有内容。

- [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer) marketplace 映像。
- SQL Server 2016 SP1 或 SP2 （企业版、 标准版或开发人员） 在 Windows Server 2016 server 映像上。 本教程使用[Windows Server 2016 上的 SQL Server 2016 SP2 Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016) marketplace 映像。
- [SQL Server IaaS 扩展](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)1.2.30 版本或更高版本。 SQL IaaS 扩展安装所需的所有 Windows 版本的 Marketplace SQL Server 项目的必需组件。 这样，SQL 虚拟机上配置特定于 SQL 的设置。 如果不在本地应用商店中安装该扩展，预配的 SQL 将失败。
- [适用于 Windows 的自定义脚本扩展](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension)版本 1.9.1 或更高版本。 自定义脚本扩展是一种工具，可用于自动启动部署后 VM 自定义任务。
- [PowerShell Desired State Configuration (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) 2.76.0.0 版本或更高版本。 DSC 是可用于部署和管理软件服务的配置数据和管理这些服务的运行的环境的 Windows PowerShell 中的管理平台。

若要了解有关将项添加到 Azure Stack marketplace 的详细信息，请参阅[Azure Stack Marketplace 概述](azure-stack-marketplace.md)。

## <a name="create-a-sql-server-alwayson-availability-group"></a>创建 SQL Server AlwaysOn 可用性组
使用在本部分中的步骤来部署使用 SQL Server AlwaysOn 可用性组[sql 2016 alwayson Azure Stack 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson)。 此模板部署 Always On 可用性组中的两个 SQL Server 企业版、 标准版或开发人员实例。 它创建以下资源：

- 网络安全组
- 虚拟网络
- 四个存储帐户 （一个用于 Active Directory (AD)、 一个用于 SQL、 一个用于文件共享见证和一个用于 VM 诊断）
- 四个公共 IP 地址 （一个 AD 中，两个用于每个 SQL VM 和一个用于公共负载均衡器绑定到 SQL AlwaysOn 侦听器）
- 一个外部负载均衡器的公共 ip 绑定到 SQL AlwaysOn 侦听器的 SQL Vm
- 一个 VM (Windows Server 2016)，为新林具有单个域的域控制器配置
- 两个 Vm (Windows Server 2016) 配置了 SQL Server 2016 SP1 或 SP2 Enterprise、 Standard 或开发人员版和群集。 这些必须 marketplace 映像。
- 一个 VM (Windows Server 2016) 配置为群集的文件共享见证服务器
- 一个可用性集，其中包含的 SQL 和文件共享见证服务器 Vm  

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. 选择“\+ 创建资源” > “自定义”，然后选择“模板部署”。

   ![自定义模板部署](media/azure-stack-tutorial-sqlrp/1.png)


3. 上**自定义部署**边栏选项卡，选择**编辑模板** > **快速入门模板**，然后使用下拉列表的可用自定义模板选择**sql 2016 alwayson**模板，单击**确定**，然后**保存**。

   [![](media/azure-stack-tutorial-sqlrp/2-sm.PNG "选择快速入门模板")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. 上**自定义部署**边栏选项卡，选择**编辑参数**和查看的默认值。 根据需要提供所有所需的参数信息，并单击修改的值**确定**。<br><br> 最小值：

    - ADMINPASSWORD、 SQLSERVERSERVICEACCOUNTPASSWORD 和 SQLAUTHPASSWORD 参数提供复杂的密码。
    - 对于所有小写字母的 DNSSUFFIX 参数中的反向查找输入的 DNS 后缀 (**azurestack.external**对于 ASDK 安装)。
    
   [![](media/azure-stack-tutorial-sqlrp/3-sm.PNG "编辑自定义部署参数")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. 上**自定义部署**边栏选项卡，选择要使用和创建新的资源组的订阅，或选择现有资源组的自定义部署。<br><br> 接下来，选择资源组的位置（对于 ASDK 安装，请选择“本地”），然后单击“创建”。 将验证自定义部署设置，然后将启动部署。

    [![](media/azure-stack-tutorial-sqlrp/4-sm.PNG "创建自定义部署")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)


6. 在管理门户中，选择**资源组**然后的资源组的名称创建自定义部署的和 (**资源组**对于此示例)。 查看部署状态，确保所有部署已成功完成。<br><br>接下来，查看资源组项目，然后选择**SQLPIPsql\<资源组名称\>** 公共 IP 地址项。 记下公共 IP 地址和负载均衡器公共 IP 的完整 FQDN。 您需要向 Azure Stack 操作员提供此，因此他们可以创建利用此 SQL AlwaysOn 可用性组 SQL 宿主服务器。

   > [!NOTE]
   > 模板部署将需要几个小时才能完成。

   ![自定义部署完成](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>启用自动种子设定
模板已成功部署和配置 SQL AlwaysON 可用性组之后，必须启用[自动种子设定](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group)每个可用性组中的 SQL Server 实例上。 

使用自动种子设定创建可用性组时，SQL Server 自动次要副本的每个数据库中创建无需任何其他人工干预组要确保高可用性的 AlwaysOn 数据库所需。

使用以下 SQL 命令来配置 AlwaysOn 可用性组的自动种子设定。 替换\<InstanceName\>与主数据库与 AlwaysOn 可用性组名称，根据需要实例 SQL Server 名称和 < availability_group_name >。 

在主 SQL 实例：

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![主 SQL 实例脚本](./media/azure-stack-tutorial-sqlrp/sql1.png)

在辅助 SQL 实例：

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![辅助 SQL 实例脚本](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>配置包含的数据库身份验证
将包含的数据库添加到可用性组之前，请确保在托管可用性组可用性副本的每个服务器实例上，包含的数据库身份验证服务器选项已设置为 1。 有关详细信息，请参阅[包含的数据库身份验证](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017)。

使用以下命令以设置可用性组中的每个 SQL Server 实例包含的数据库身份验证服务器选项：

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![设置包含数据库身份验证](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>创建 Azure Stack SQL 宿主服务器
已创建，并正确配置 SQL Server AlwayOn 可用性组后，Azure Stack 操作员必须创建 Azure Stack SQL 宿主服务器以使更多的容量可供用户创建数据库。 

确保 SQL 负载均衡器的公共 IP 之前记录创建 SQL AlwaysOn 可用性组的资源组时使用的公共 ip 地址或完整的 FQDN (**SQLPIPsql\<资源组名称\>**). 此外，您需要知道 SQL Server 身份验证凭据用于访问 AlwaysOn 可用性组中的 SQL 实例。

> [!NOTE]
> 通过 Azure Stack 操作员，必须从 Azure Stack 管理门户运行此步骤。

使用 SQL AlwaysOn 可用性组的负载均衡器侦听器的公共 IP 和 SQL 身份验证登录信息，Azure Stack 操作员可以现在[创建使用 SQL AlwaysOn 可用性组的 SQL 宿主服务器](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups)。 

此外请确保你已创建计划，并且提供了要将 SQL AlwaysOn 数据库创建提供给用户。 运算符将需要添加**Microsoft.SqlAdapter**到计划服务并创建新配额是专门用于高度可用的数据库。 有关创建计划的详细信息，请参阅[计划、 产品/服务、 配额和订阅概述](azure-stack-plan-offer-quota-overview.md)。

> [!TIP]
> **Microsoft.SqlAdapter**服务将不能将添加到计划之前[SQL Server 资源提供程序已部署](azure-stack-sql-resource-provider-deploy.md)。

## <a name="create-a-highly-available-sql-database"></a>创建高度可用的 SQL 数据库
后 SQL AlwaysOn 可用性组创建、 配置和由 Azure Stack 操作员添加为 Azure Stack SQL 宿主服务器，包括 SQL Server 数据库功能的订阅的租户用户可以创建支持的 SQL 数据库在本部分中的步骤的 AlwaysOn 功能。 

> [!NOTE]
> 以下步骤从运行在 Azure Stack 用户门户租户用户提供 SQL Server 功能 （Microsoft.SQLAdapter 服务） 的订阅。

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. 选择**\+** **创建资源** > **数据\+存储**，然后**SQL 数据库**。<br><br>提供所需的数据库属性的信息包括名称、 排序规则、 最大大小和订阅、 资源组和位置以用于部署。 

   ![创建 SQL 数据库](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. 选择**SKU** ，然后选择适当 SQL 宿主服务器的 SKU 使用。 在此示例中，已创建 Azure Stack 操作员**企业-HA** SKU 以支持高可用性的 SQL AlwaysOn 可用性组。

   ![选择 SKU](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. 选择**登录名** > **创建新的登录名**，然后提供要用于新数据库的 SQL 身份验证凭据。 完成后，单击**确定**，然后**创建**开始数据库部署过程。

   ![创建登录名](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. SQL 数据库部署成功完成后，查看数据库属性来发现要用于连接到新高度可用的数据库的连接字符串。 

   ![查看连接字符串](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 从模板创建一个 SQL Server AlwaysOn 可用性组
> * 创建 Azure Stack SQL 宿主服务器
> * 创建高度可用的 SQL 数据库

转到下一教程，了解如何执行以下操作：
> [!div class="nextstepaction"]
> [创建高可用性的 MySQL 数据库](azure-stack-tutorial-mysql.md)