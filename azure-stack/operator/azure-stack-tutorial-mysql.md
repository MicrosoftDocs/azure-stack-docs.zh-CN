---
title: 在 Azure Stack 中心提供高度可用的 MySQL 数据库
description: 了解如何使用 Azure Stack 集线器创建 MySQL Server 资源提供程序主机和高度可用的 MySQL 数据库。
services: azure-stack
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 10/07/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: 422ffe6f0b99909af8d3baf3dbc64a8b8c810a7b
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023277"
---
# <a name="offer-highly-available-mysql-databases"></a>提供高可用性的 MySQL 数据库

作为 Azure Stack 中心操作员，你可以将服务器 Vm 配置为托管 MySQL Server 数据库。 成功创建 MySQL 群集并通过 Azure Stack 中心管理这些群集后，订阅 MySQL 服务的用户可以轻松地创建高度可用的 MySQL 数据库。

本文介绍如何使用 Azure Stack 集线器 Marketplace 项创建[具有复制群集的 MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster)。 此解决方案使用多个 Vm 将数据库从主节点复制到可配置的副本数。 创建群集后，可以将其作为 Azure Stack 中心 MySQL 宿主服务器添加，然后用户可以创建高度可用的 MySQL 数据库。

> [!IMPORTANT]
> 对于所有 Azure 云订阅环境，**使用复制**Azure Stack 中心市场项的 MySQL 可能不可用。 验证 marketplace 项在你的订阅中是否可用，然后再尝试执行此 tutoral 的其余部分。

学习内容：

> [!div class="checklist"]
> * 从 marketplace 项创建 MySQL Server 群集
> * 创建 Azure Stack 中心 MySQL 宿主服务器
> * 创建高度可用的 MySQL 数据库

将使用可用的 Azure Stack 中心市场项创建和配置三个 VM MySQL 服务器群集。 

在开始之前，请确保已成功安装[MySQL Server 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)，并且 Azure Stack 中心 Marketplace 中提供了以下各项：

> [!IMPORTANT]
> 需要以下所有项才能创建 MySQL 群集。

- [具有复制的 MySQL](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster)。 这是将用于 MySQL 群集部署的 Bitnami 解决方案模板。
- [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian)。 Debian 8 "Jessie" with precise-backports 内核，适用于 credativ 提供的 Microsoft Azure。 Debian GNU/Linux 是最流行的 Linux 分发版之一。
- [适用于 linux 2.0 的自定义脚本](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview)。 自定义脚本扩展是一种工具，用于在 VM 预配后执行 VM 自定义任务。 将此扩展添加到虚拟机后，它可以从 Azure 存储下载脚本并在 VM 上运行这些脚本。 自定义脚本扩展任务也可以使用 Azure PowerShell cmdlet 和 Azure 跨平台命令行接口（xPlat CLI）自动执行。
- 适用于 Linux 扩展1.4.7 的 VM 访问权限。 使用 VM 访问扩展可以重置密码、SSH 密钥或 SSH 配置，使你能够重新获得 VM 的访问权限。 你还可以使用密码或 SSH 密钥添加新用户，或使用此扩展来删除用户。 此扩展以 Linux Vm 为目标。

若要了解有关将项目添加到 Azure Stack 中心 Marketplace 的详细信息，请参阅[Azure Stack 中心市场概述](azure-stack-marketplace.md)。

还需要使用 SSH 客户端（如[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) ），以便在部署 Linux vm 后登录到这些 vm。

## <a name="create-a-mysql-server-cluster"></a>创建 MySQL Server 群集 
使用本部分中的步骤，使用[包含复制](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster)marketplace 项的 MySQL 部署 mysql Server 群集。 此模板部署在高可用性 MySQL 群集中配置的三个 MySQL Server 实例。 默认情况下，它会创建以下资源：

- 虚拟网络
- 网络安全组
- 一个存储帐户
- 一个可用性集
- 三个网络接口（每个默认 Vm 一个）
- 公共 IP 地址（适用于主 MySQL 群集 VM）
- 用于托管 MySQL 群集的三个 Linux Vm

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. 选择 **\+** **创建资源** > **计算**，然后**使用复制创建 MySQL**。

   ![自定义模板部署](media/azure-stack-tutorial-mysqlrp/1.png)

3. 在 "**基本**信息" 页上提供基本的部署信息。 查看默认值并根据需要进行更改，然后单击 **"确定"** 。<br><br>至少提供以下内容：
   - 部署名称（默认值为 mymysql）
   - 应用程序根密码。 提供12个字符的字母数字密码，**无特殊字符**
   - 应用程序数据库名称（默认值为 bitnami）
   - 要创建的 MySQL 数据库副本 Vm 的数量（默认值为2）
   - 选择要使用的订阅
   - 选择要使用的资源组，或者创建一个新资源组
   - 选择位置（默认为 ASDK `local`）

   [![](media/azure-stack-tutorial-mysqlrp/2-sm.PNG "Deployment basics")](media/azure-stack-tutorial-mysqlrp/2-lg.PNG#lightbox)

4. 在 "**环境配置**" 页上，提供以下信息，然后单击 **"确定"** ： 
   - 用于安全外壳（SSH）身份验证的密码或 SSH 公钥。 如果使用密码，则它必须包含字母、数字，并且**可以**包含特殊字符
   - VM 大小（默认为标准 D1 v2 Vm）
   - 数据磁盘大小（GB）单击**确定**

   [![](media/azure-stack-tutorial-mysqlrp/3-sm.PNG "Environment configuration")](media/azure-stack-tutorial-mysqlrp/3-lg.PNG#lightbox)

5. 查看部署**摘要**。 还可以选择下载自定义模板和参数，然后单击 **"确定"** 。

   [![](media/azure-stack-tutorial-mysqlrp/4-sm.PNG "Summary")](media/azure-stack-tutorial-mysqlrp/4-lg.PNG#lightbox)

6. 在 "**购买**" 页上单击 "**创建**" 以开始部署。

   ![购买](media/azure-stack-tutorial-mysqlrp/5.png)

    > [!NOTE]
    > 部署需要大约一小时。 在继续之前，请确保已完成部署并且已完全配置 MySQL 群集。 

7. 所有部署都成功完成后，查看资源组项，并选择 " **mysqlip** " 公共 IP 地址项。 记录群集的公共 ip 地址和公共 IP 的完全 FQDN。<br><br>需要为 Azure Stack 中心操作员提供此服务，以便他们可以利用此 MySQL 群集创建 MySQL 宿主服务器。


### <a name="create-a-network-security-group-rule"></a>创建网络安全组规则
默认情况下，不会为 MySQL 配置主机 VM 的公共访问权限。 要使 Azure Stack Hub MySQL 资源提供程序连接和管理 MySQL 群集，需要创建入站网络安全组（NSG）规则。

1. 在管理员门户中，导航到部署 MySQL 群集时创建的资源组，并选择 "网络安全组" （**默认-子网-sg**）：

   ![开门](media/azure-stack-tutorial-mysqlrp/6.png)

2. 选择 "**入站安全规则**"，然后单击 "**添加**"。<br><br>在 "**目标端口范围**" 中输入**3306** ，并根据需要在 "**名称**" 和 "**说明**" 字段中提供说明。 单击 "添加" 以关闭 "入站安全规则" 对话框。

   ![开门](media/azure-stack-tutorial-mysqlrp/7.png)

### <a name="configure-external-access-to-the-mysql-cluster"></a>配置对 MySQL 群集的外部访问
必须先启用外部访问，然后才能将 MySQL 群集添加为 Azure Stack Hub MySQL Server 主机。

1. 使用 SSH 客户端时，此示例使用[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)从可以访问公共 IP 的计算机登录到主 MySQL 计算机。 主要 MySQL VM 名称通常以**0**结尾，并分配有一个公共 IP。<br><br>使用公共 IP 并使用**bitnami**的用户名和之前创建的应用程序密码登录到 VM，但不使用特殊字符。

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)


2. 在 SSH 客户端窗口中，使用以下命令确保 bitnami 服务处于活动状态且正在运行。 出现提示时，再次提供 bitnami 密码：

   `sudo service bitnami status`

   ![检查服务](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. 创建 Azure Stack 中心 MySQL 宿主服务器用来连接 MySQL 的远程访问用户帐户，然后退出 SSH 客户端。<br><br>运行以下命令，使用前面创建的超级用户密码以 root 身份登录到 MySQL，并创建新的管理员用户，将 *\<用户名\>* 和 *\<密码\>* 根据环境需要。 在此示例中，要创建的用户的名称为**sqlsa** ，并使用强密码：

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```
   ![创建管理员用户](media/azure-stack-tutorial-mysqlrp/bitnami3.png)


4. 记录新的 MySQL 用户信息。<br><br>需要将此用户名和密码以及群集公共 IP 的公共 ip 地址或完整 FQDN 提供给 Azure Stack 中心操作员，以便他们可以使用此 MySQL 群集创建 MySQL 宿主服务器。


## <a name="create-an-azure-stack-hub-mysql-hosting-server"></a>创建 Azure Stack 中心 MySQL 宿主服务器
创建并正确配置 MySQL Server 群集后，Azure Stack 中心操作员必须创建 Azure Stack 中心 MySQL 宿主服务器，以使用户能够使用额外的容量来创建数据库。 

请确保在创建 MySQL 群集的资源组（**mysqlip**）时，为之前记录的 mysql 群集主 VM 的公共 ip 使用公共 ip 或完全 FQDN。 此外，操作员需要知道创建的 MySQL Server 身份验证凭据以远程访问 MySQL 群集数据库。

> [!NOTE]
> 必须通过 Azure Stack 中心操作员从 Azure Stack 中心管理门户运行此步骤。

使用 MySQL 群集的公共 IP 和 MySQL 身份验证登录信息，Azure Stack 中心操作员现在可以[使用新的 mysql 群集创建 MySQL 宿主服务器](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server)。 

此外，请确保已创建计划和产品/服务，以便用户可以创建 MySQL 数据库。 操作员需要将**mysqladapter.dbadapter**服务添加到计划并为高度可用的数据库创建新的配额。 有关创建计划的详细信息，请参阅 "[服务、计划、产品/服务、订阅概述](service-plan-offer-subscription-overview.md)"。

> [!TIP]
> 在[部署 MySQL Server 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)之前，将不能向计划中添加**mysqladapter.dbadapter**服务。



## <a name="create-a-highly-available-mysql-database"></a>创建高度可用的 MySQL 数据库
创建、配置 MySQL 群集并将其作为 Azure Stack 中心 MySQL 宿主服务器添加到 Azure Stack 中心操作员后，具有订阅的租户用户（包括 MySQL Server 数据库功能）可以通过按照本部分中的步骤进行操作。 

> [!NOTE]
> 使用订阅提供 MySQL 服务器功能（Mysqladapter.dbadapter 服务）作为租户用户，从 Azure Stack 中心用户门户运行这些步骤。

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. 选择 **\+** **创建资源** > **数据 \+ 存储**，然后选择 " **MySQL 数据库**"。<br><br>提供所需的数据库属性信息，包括名称、排序规则、要使用的订阅以及要用于部署的位置。 

   ![创建 MySQL 数据库](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. 选择 " **SKU** "，然后选择要使用的相应 MySQL 宿主服务器 SKU。 在此示例中，Azure Stack 中心操作员创建了**mysql-HA** SKU，以支持 MySQL 群集数据库的高可用性。

   ![选择 SKU](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. 选择 "**登录**" > **创建新的登录名**，然后提供要用于新数据库的 MySQL 身份验证凭据。 完成后，单击 **"确定"** ，然后单击 "**创建**" 以开始数据库部署过程。

   ![添加登录名](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. MySQL 数据库部署成功完成后，请查看数据库属性，以发现用于连接到新的高可用性数据库的连接字符串。 

   ![查看连接字符串](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>后续步骤

[更新 MySQL 资源提供程序](azure-stack-mysql-resource-provider-update.md)
