---
title: 提供了高度可用的 MySQL 数据库，在 Azure Stack 中提供 |Microsoft Docs
description: 了解如何使用 Azure Stack 主机计算机和高可用性的 MySQL 数据库创建 MySQL Server 资源提供程序。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: quying
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: 1b1ebd27f87030b34b132eb31c4b4ae83f13a3b0
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618228"
---
# <a name="tutorial-offer-highly-available-mysql-databases"></a>教程：提供高可用性的 MySQL 数据库

Azure Stack 操作员，可以配置承载 MySQL Server 数据库服务器 Vm。 MySQL 群集后成功，由创建和管理 Azure Stack，具有已订阅的 MySQL 服务的用户可以轻松地创建高可用性的 MySQL 数据库。

本教程演示如何使用 Azure Stack marketplace 项来创建[MySQL 复制群集](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster)。 此解决方案使用多个 VM 将数据库从主节点复制到多个可配置的副本。 一旦创建，该群集随后可添加为 Azure Stack MySQL 宿主服务器，然后用户可以创建高可用性的 MySQL 数据库。

> [!IMPORTANT]
> **MySQL 复制**Azure Stack marketplace 项可能无法用于所有 Azure 云订阅环境。 验证 marketplace 项尝试按照这 tutoral 的其余部分之前在订阅中可用。

要学习的知识：

> [!div class="checklist"]
> * 从 marketplace 项创建 MySQL 服务器群集
> * 创建 Azure Stack MySQL 宿主服务器
> * 创建高度可用的 MySQL 数据库

在本教程中，三个虚拟机 MySQL 服务器群集将创建和配置为使用可用的 Azure Stack marketplace 项。 

在开始本教程中的步骤之前, 确保[MySQL Server 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)已成功安装和在 Azure Stack marketplace 中的可用以下项：

> [!IMPORTANT]
> 以下所有所需创建 MySQL 群集。

- [使用复制的 MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster)。 这是将用于 MySQL 群集部署 Bitnami 解决方案模板。
- [Debian 8"Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian)。 Debian 8"Jessie"backports 内核与适用于 Microsoft Azure 提供由 credativ 提供。 Debian GNU/Linux 是最流行的 Linux 分发版之一。
- [适用于 linux 2.0 自定义脚本](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview)。 自定义脚本扩展是一个工具，可执行 VM 自定义任务 post VM 设置。 此扩展添加到虚拟机，它可以从 Azure 存储下载脚本，并在 VM 上运行它们。 也可以使用 Azure PowerShell cmdlet 和 Azure 跨平台命令行接口 (xPlat CLI) 自动自定义脚本扩展任务。
- Linux 扩展 1.4.7 的 VM 访问。 VM 访问扩展可以重置密码、 SSH 密钥或 SSH 配置，以便可以重新获得对 vm 的访问权限。 您还可以添加新用户使用密码或 SSH 密钥或删除使用此扩展的用户。 此扩展以目标 Linux Vm。

若要了解有关将项添加到 Azure Stack marketplace 的详细信息，请参阅[Azure Stack Marketplace 概述](azure-stack-marketplace.md)。

您还需要 SSH 客户端，如[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)能够在部署后登录到 Linux Vm。

## <a name="create-a-mysql-server-cluster"></a>创建 MySQL 服务器群集 
使用此部分部署 MySQL 服务器中的步骤使用群集[MySQL 复制](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster)marketplace 项。 此模板部署高度可用的 MySQL 群集中配置的三个 MySQL 服务器实例。 默认情况下，它将创建以下资源：

- 虚拟网络
- 网络安全组
- 一个存储帐户
- 一个可用性集
- 三个网络接口 （一个用于每个默认的 Vm）
- 公共 IP 地址 （适用于主 MySQL 群集 VM）
- 三个 Linux 虚拟机以托管 MySQL 群集

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. 选择**\+** **创建资源** > **计算**，，然后**MySQL 复制**。

   ![自定义模板部署](media/azure-stack-tutorial-mysqlrp/1.png)

3. 提供了有关基本部署信息**基础知识**页。 查看默认值并根据需要更改，单击**确定**。<br><br>至少，提供以下信息：
   - 部署名称 （默认值为 mymysql）
   - 应用程序的根密码。 提供与一个 12 字符的字母数字密码**没有特殊字符**
   - 应用程序数据库名称 （默认值为 bitnami）
   - MySQL 数据库副本要创建的 Vm 数 （默认值为 2）
   - 选择要使用的订阅
   - 选择要使用或创建一个新的资源组
   - 选择的位置 （默认值为本地对于 ASDK）

   [![](media/azure-stack-tutorial-mysqlrp/2-sm.PNG "部署基础知识")](media/azure-stack-tutorial-mysqlrp/2-lg.PNG#lightbox)

4. 上**环境配置**页上，提供以下信息，然后单击**确定**: 
   - 密码或 SSH 公钥用于安全外壳 (SSH) 身份验证。 如果使用的密码，它必须包含字母、 数字和**可以**包含特殊字符
   - VM 大小 （默认为标准 D1 v2 Vm）
   - 数据磁盘大小以 GB 单击**确定**

   [![](media/azure-stack-tutorial-mysqlrp/3-sm.PNG "环境配置")](media/azure-stack-tutorial-mysqlrp/3-lg.PNG#lightbox)

5. 对部署进行复查**摘要**。 （可选） 可以下载自定义的模板和参数，然后依次**确定**。

   [![](media/azure-stack-tutorial-mysqlrp/4-sm.PNG "摘要")](media/azure-stack-tutorial-mysqlrp/4-lg.PNG#lightbox)

6. 单击**创建**上**购买**页后，可以开始部署。

   ![购买](media/azure-stack-tutorial-mysqlrp/5.png)

    > [!NOTE]
    > 部署将需要大约一小时。 请确保部署已完成，然后再继续已完全配置 MySQL 群集。 

7. 所有部署已成功都完成后，查看资源组项目，然后选择**mysqlip**公共 IP 地址项。 记下公共 IP 地址和群集的公共 ip 的完整 FQDN。<br><br>需要提供这给 Azure Stack 操作员，以便他们可以创建利用此 MySQL 群集 MySQL 宿主服务器。


### <a name="create-a-network-security-group-rule"></a>创建网络安全组规则
默认情况下，没有公共访问权限的 MySQL 配置到 VM 的主机。 Azure Stack MySQL 资源提供程序连接和管理 MySQL 群集，需要创建入站的网络安全组 (NSG) 规则。

1. 在管理员门户中，导航到部署 MySQL 群集时创建的资源组，并选择网络安全组 (**默认子网 sg**):

   ![打开](media/azure-stack-tutorial-mysqlrp/6.png)

2. 选择**入站安全规则**，然后单击**添加**。<br><br>输入**3306**中**目标端口范围**并选择性地提供中的说明**名称**并**说明**字段。 单击添加以关闭入站的安全规则对话框。

   ![打开](media/azure-stack-tutorial-mysqlrp/7.png)

### <a name="configure-external-access-to-the-mysql-cluster"></a>配置 MySQL 群集外部访问
可以作为 Azure Stack MySQL 服务器的主机添加 MySQL 群集之前，必须启用外部访问。

1. 使用 SSH 客户端，此示例使用[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)，登录到主 MySQL 计算机可以访问的公共 IP 的计算机。 主 MySQL 虚拟机名称通常以结尾**0**并为其分配公共 IP。<br><br>使用连接到的用户名与 VM 的公共 IP 和 log **bitnami**和特殊字符没有之前创建的应用程序密码。

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)


2. 在 SSH 客户端窗口中，使用以下命令以确保 bitnami 服务处于活动状态且正在运行。 出现提示时再次提供 bitnami 密码：

   `sudo service bitnami status`

   ![检查服务](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. 创建用于通过 Azure Stack MySQL 宿主服务器以连接到 MySQL，然后退出 SSH 客户端的远程访问用户帐户。<br><br>运行以下命令以 root 身份，使用之前创建的根密码登录到 MySQL 并创建新的管理员用户，请替换*\<用户名\>* 并*\<密码\>* 根据需要为您的环境。 在此示例中，用户要创建名为**sqlsa**和使用强密码：

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```
   ![创建管理员用户](media/azure-stack-tutorial-mysqlrp/bitnami3.png)


4. 记录新的 MySQL 用户信息。<br><br>您需要向 Azure Stack 操作员以便他们可以创建 MySQL 宿主服务器使用此 MySQL 群集中提供此用户名和密码，以及公共 IP 地址或群集的公共 ip 的完整 FQDN。


## <a name="create-an-azure-stack-mysql-hosting-server"></a>创建 Azure Stack MySQL 宿主服务器
MySQL 服务器群集已创建，并正确配置后，Azure Stack 操作员必须创建 Azure Stack MySQL 宿主服务器以使更多的容量可供用户创建数据库。 

确保使用的公共 ip 地址或完整的 FQDN 的主虚拟机之前记录创建 MySQL 群集的资源组的 MySQL 群集的公共 ip (**mysqlip**)。 此外，该运算符将需要知道您创建的用于远程访问 MySQL 群集数据库的身份验证凭据的 MySQL 服务器。

> [!NOTE]
> 通过 Azure Stack 操作员，必须从 Azure Stack 管理门户运行此步骤。

使用 MySQL 群集的公共 IP 和 MySQL 身份验证登录信息，Azure Stack 操作员可以现在[创建 MySQL 宿主服务器使用新的 MySQL 群集](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server)。 

此外请确保你已创建计划和产品/服务为用户提供创建 MySQL 数据库。 运算符将需要添加**Microsoft.MySqlAdapter**到计划服务并创建新配额是专门用于高度可用的数据库。 有关创建计划的详细信息，请参阅[计划、 产品/服务、 配额和订阅概述](azure-stack-plan-offer-quota-overview.md)。

> [!TIP]
> **Microsoft.MySqlAdapter**服务将不能将添加到计划之前[MySQL Server 资源提供程序已部署](azure-stack-mysql-resource-provider-deploy.md)。



## <a name="create-a-highly-available-mysql-database"></a>创建高度可用的 MySQL 数据库
MySQL 群集创建、 配置，并由 Azure Stack 操作员添加为 Azure Stack MySQL 宿主服务器后，具有订阅包括 MySQL Server 数据库功能的租户用户可以创建高可用性的 MySQL 的数据库在本部分中的步骤。 

> [!NOTE]
> 以下步骤从运行在 Azure Stack 用户门户租户用户提供 MySQL 服务器功能 （Microsoft.MySQLAdapter 服务） 的订阅。

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. 选择**\+** **创建资源** > **数据\+存储**，然后**MySQL 数据库**.<br><br>提供所需的数据库属性的信息包括名称、 排序规则、 订阅以使用和要使用的部署位置。 

   ![创建 MySQL 数据库](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. 选择**SKU** ，然后选择适当 MySQL 宿主服务器的 SKU 使用。 在此示例中，已创建 Azure Stack 操作员**MySQL-HA** SKU 以支持高可用性的 MySQL 群集数据库。

   ![选择 SKU](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. 选择**登录名** > **创建新的登录名**，然后提供要用于新数据库的 MySQL 身份验证凭据。 完成后，单击**确定**，然后**创建**开始数据库部署过程。

   ![添加登录名](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. MySQL 数据库部署成功完成后，查看数据库属性来发现要用于连接到新高度可用的数据库的连接字符串。 

   ![查看连接字符串](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 从 marketplace 项创建 MySQL 服务器群集
> * 创建 Azure Stack MySQL 宿主服务器
> * 创建高度可用的 MySQL 数据库

转到下一教程，了解如何执行以下操作：
> [!div class="nextstepaction"]
> [提供 web 应用](azure-stack-tutorial-app-service.md)
