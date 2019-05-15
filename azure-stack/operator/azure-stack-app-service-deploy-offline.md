---
title: 在 Azure Stack 离线环境中部署应用服务 | Microsoft Docs
description: 有关如何在受 AD FS 保护且已断开连接的 Azure Stack 环境中部署应用服务的详细指南。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 1dfe1cba366d9b30c53a43724741c9a9e0f65819
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618527"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>将应用服务资源提供程序添加到受 AD FS 保护且已断开连接的 Azure Stack 环境

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!IMPORTANT]
> 请将 1901 更新应用于 Azure Stack 集成系统，或部署最新的 Azure Stack 开发工具包，然后部署 Azure 应用服务 1.5。

按照本文中的说明操作即可将[应用服务资源提供程序](azure-stack-app-service-overview.md)安装到符合以下条件的 Azure Stack 环境：

- 未连接到 Internet
- 受 Active Directory 联合身份验证服务 (AD FS) 保护。

> [!IMPORTANT]  
> 在运行资源提供程序安装程序之前，请确保已按照[准备工作](azure-stack-app-service-before-you-get-started.md)中的指南进行操作，并已阅读版本 1.5 随附的[发行说明](azure-stack-app-service-release-notes-update-five.md)，了解新功能、修补程序以及任何可能影响部署的已知问题。

若要将应用服务资源提供程序添加到脱机的 Azure Stack 部署，必须完成以下顶级任务：

1. 完成[先决条件步骤](azure-stack-app-service-before-you-get-started.md)（例如购买证书，可能需要数天才能接收到）。
2. [下载并提取安装文件和帮助器文件](azure-stack-app-service-before-you-get-started.md)到连接 Internet 的计算机。
3. 创建脱机安装包。
4. 运行 appservice.exe 安装程序文件。

## <a name="create-an-offline-installation-package"></a>创建脱机安装包

若要在断开连接的环境中部署应用服务，必须先在连接到 Internet 的计算机上创建脱机安装包。

1. 在连接到 Internet 的计算机上运行 AppService.exe 安装程序。

2. 单击“高级” > “创建脱机安装包”。

    ![应用服务安装程序][1]

3. 应用服务安装程序创建脱机安装包并显示其路径。 可以单击“打开文件夹”，在文件资源管理器中打开该文件夹。

    ![应用服务安装程序](media/azure-stack-app-service-deploy-offline/image02.png)

4. 将安装程序 (AppService.exe) 和脱机安装包复制到 Azure Stack 主机。

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>在 Azure Stack 上完成应用服务的脱机安装

1. 在可以访问“Azure Stack 管理”Azure 资源管理终结点的计算机上，以管理员身份运行 appservice.exe。

2. 单击“高级” > “完成脱机安装”。

    ![应用服务安装程序][2]

3. 浏览到以前创建的脱机安装包的位置，然后单击“下一步”。

    ![应用服务安装程序](media/azure-stack-app-service-deploy-offline/image04.png)

4. 查看并接受 Microsoft 软件许可条款，然后单击“下一步”。

5. 查看并接受第三方许可条款，然后单击“下一步”。

6. 请确保应用服务云配置信息正确无误。 如果在 Azure Stack 开发工具包部署过程中使用了默认设置，可以接受此处的默认值。 但是，如果在部署 Azure Stack 时自定义了选项，或者要部署到集成系统，则必须在此窗口中编辑相应的值，以反映自己的需要。 例如，如果使用域后缀 mycloud.com，则你的 Azure Stack 租户 Azure 资源管理器终结点必须更改为`management.<region>.mycloud.com`。 确认信息后，单击“下一步”。

    ![应用服务安装程序][3]

7. 在下一页上执行以下操作：
   1. 单击“Azure Stack 订阅”框旁边的“连接”按钮。
      - 提供管理员帐户。 例如，cloudadmin@azurestack.local。 输入密码，并单击“登录”。
   2. 在“Azure Stack 订阅”框中，选择“默认提供商订阅”。
    
      > [!NOTE]
      > 应用服务只能部署到“默认提供程序订阅”。
      >
    
   3. 在“Azure Stack 位置”框中，选择要部署到的区域所对应的位置。 例如，如果要部署到 Azure Stack 开发工具包，请选择“本地”。
   4. 单击“下一步”。

      ![应用服务安装程序][4]

8. 现在，可以选择部署到通过[此处](azure-stack-app-service-before-you-get-started.md#virtual-network)所述的步骤配置的现有虚拟网络，或者让应用服务安装程序创建虚拟网络和关联的子网。
   1. 选择“使用默认设置创建 VNet”，接受默认值，然后单击“下一步”，或者
   2. 选择“使用现有的 VNet 和子网”。
       1. 选择包含虚拟网络的**资源组**；
       2. 选择要部署到的正确**虚拟网络**名称；
       3. 为每个所需角色子网选择正确的“子网”值；
       4. 点击“下一步”

      ![应用服务安装程序][5]

9. 输入文件共享的信息，然后单击“下一步”。 文件共享的地址必须使用文件服务器的完全限定域名或 IP 地址。 例如 \\\appservicefileserver.local.cloudapp.azurestack.external\websites，或 \\\10.0.0.1\websites。  如果使用已加入域的文件服务器，则必须提供包含域的完整用户名，例如 myfileserverdomain\FileShareOwner。

    > [!NOTE]
    > 在继续下一步之前，安装程序会尝试测试与文件共享的连接。  但是，如果前面已选择部署到现有虚拟网络，则安装程序可能无法连接到文件共享，并显示警告来询问是否继续。  请检查文件共享信息，如果正确，则继续。
    >
    >

   ![应用服务安装程序][8]

10. 在下一页上执行以下操作：
    1. 在中**标识应用程序 ID**框中，输入用于标识 （由 Azure AD) 进行应用程序的 GUID。
    2. 在“标识应用程序证书文件”框中，输入（或浏览到）证书文件的位置。
    3. 在“标识应用程序证书密码”框中，输入证书的密码。 此密码是在使用脚本创建证书时记下的密码。
    4. 在“Azure 资源管理器根证书文件”框中，输入（或浏览到）证书文件的位置。
    5. 单击“下一步”。

    ![应用服务安装程序][10]

11. 对于三个证书文件框的每一个框，单击“浏览”并导航到相应的证书文件。 必须为每个证书提供密码。 这些证书是在[创建所需证书步骤](azure-stack-app-service-before-you-get-started.md#get-certificates)中创建的。 输入所有信息后，单击“下一步”。

    | 框 | 证书文件名示例 |
    | --- | --- |
    | **应用服务默认 SSL 证书文件** | \_.appservice.local.AzureStack.external.pfx |
    | **应用服务 API SSL 证书文件** | api.appservice.local.AzureStack.external.pfx |
    | **应用服务发布者 SSL 证书文件** | ftp.appservice.local.AzureStack.external.pfx |

    如果你在创建证书时使用不同的域后缀，证书文件名不要使用*本地。AzureStack.external*。 而要改用自定义域信息。

    ![应用服务安装程序][11]

12. 为用于托管应用服务资源提供程序数据库的服务器实例输入 SQL Server 详细信息，然后单击“下一步”。 安装程序将验证 SQL 连接属性。 **必须**输入内部 IP 或完全限定域名作为 SQL Server 名称。

    > [!NOTE]
    > 在继续下一步之前，安装程序会尝试测试与 SQL Server 的连接。  但是，如果前面已选择部署到现有虚拟网络，则安装程序可能无法连接到 SQL Server，并显示警告来询问是否继续。  请检查 SQL Server 信息，如果正确，则继续。
    >
    > 从 Azure Stack 1.3 上的 Azure 应用服务开始，安装程序将检查 SQL Server 是否在 SQL Server 级别启用了数据库包含。  如果未启用，则会出现以下异常提示：
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;  
    >    GO  
    >    RECONFIGURE;  
    >    GO
    >    ***********************************************************
    > ```
    > 有关更多详细信息，请参阅 [Azure Stack 1.3 上的 Azure 应用服务的发行说明](azure-stack-app-service-release-notes-update-three.md)。
   
    ![应用服务安装程序][12]

13. 查看角色实例和 SKU 选项。 使用 ASDK 部署中每个角色的最小实例数和最小 SKU 填充默认值。 提供 vCPU 和内存要求摘要是为了帮助你规划部署。 进行选择后，单击“下一步”。

     > [!NOTE]
     > 对于生产部署，请遵循 [Azure Stack 中 Azure 应用服务服务器角色的容量规划](azure-stack-app-service-capacity-planning.md)中的指导。
     >
     >

    | 角色 | 最小实例数 | 最小 SKU | 说明 |
    | --- | --- | --- | --- |
    | 控制器 | 第 | Standard_A2 -（2 个 vCPU，3584 MB） | 管理和维护应用服务云的运行状况。 |
    | 管理 | 第 | Standard_A2 -（2 vCPU，3584 MB） | 管理应用服务 Azure 资源管理器和 API 终结点、门户扩展（管理员门户、租户门户、Functions 门户）和数据服务。 为了支持故障转移，已将建议的实例数增加到 2 个。 |
    | 发布服务器 | 第 | Standard_A1 -（1 vCPU，1792 MB） | 通过 FTP 和 Web 部署发布内容。 |
    | 前端 | 第 | Standard_A1 -（1 vCPU，1792 MB） | 将请求路由到应用服务应用程序。 |
    | 共享辅助角色 | 第 | Standard_A1 -（1 vCPU，1792 MB） | 托管 Web 应用程序或 API 应用程序和 Azure Functions 应用。 可能需要添加更多实例。 作为操作员，可以定义产品/服务，并选择任何 SKU 层。 这些层必须至少具有一个 vCPU。 |

    ![应用服务安装程序][14]

    > [!NOTE]
    > **不支持将 Windows Server 2016 Core 平台映像与 Azure Stack 上的 Azure 应用服务配合使用。请勿将评估映像用于生产部署。Azure Stack上的 Azure 应用服务要求在用于部署的映像上激活 Microsoft.NET 3.5.1 SP1。 市场联合 Windows Server 2016 映像未启用此功能，因此必须在预先启用此功能的情况下创建并使用 Windows Server 2016 映像。**

14. 在“选择平台映像”框中选择部署型 Windows Server 2016 虚拟机映像，该映像由应用服务云的计算资源提供程序提供。 单击“下一步”。

15. 在下一页上执行以下操作：
     1. 输入辅助角色虚拟机管理员用户名和密码。
     2. 输入其他角色虚拟机管理员用户名和密码。
     3. 单击“下一步”。

    ![应用服务安装程序][16]

16. 在摘要页上执行以下操作：
    1. 验证所做的选择。 若要进行更改，请使用“上一步”按钮访问前面的页面。
    2. 如果配置正确，则选中此复选框。
    3. 若要开始部署，请单击“下一步”。

    ![应用服务安装程序][17]

17. 在下一页上执行以下操作：
    1. 跟踪安装进度。 Azure Stack 上的应用服务大约需要 60 分钟才能完成基于默认选择的部署。
    2. 安装程序成功完成后，单击“退出”。

    ![应用服务安装程序][18]

## <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]  
> 如果已经为应用服务 RP 提供 SQL Always On 实例，则必须[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)并同步数据库，以免在进行数据库故障转移时丢失服务。

## <a name="validate-the-app-service-on-azure-stack-installation"></a>验证 Azure Stack 上的应用服务安装

1. 在 Azure Stack 管理员门户中，转到“管理 - 应用服务”。

2. 在“概述”中，在“状态”下，检查“状态”是否显示了“所有角色已就绪”。

    ![应用服务管理](media/azure-stack-app-service-deploy/image12.png)

> [!NOTE]
> 如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。  为此，请转到管理门户中的 WorkersNsg 并添加具有以下属性的出站安全规则：
> * 源:任意
> * 源端口范围：*
> * 目标：IP 地址
> * 目标 IP 地址范围：文件服务器的 IP 范围
> * 目标端口范围：445
> * 协议：TCP
> * 操作：允许
> * 优先级：700
> * 姓名：Outbound_Allow_SMB445
>

## <a name="test-drive-app-service-on-azure-stack"></a>体验 Azure Stack 上的应用服务

部署并注册应用服务资源提供程序后，对其进行测试以确保用户可以部署 Web 应用和 API 应用。

> [!NOTE]
> 需要创建一个套餐，其中的计划包含 Microsoft.Web 命名空间。 然后，需要有订阅此套餐的租户订阅。 有关详细信息，请参阅[创建套餐](azure-stack-create-offer.md)和[创建计划](azure-stack-create-plan.md)。
>
> 必须有租户订阅，才能创建使用 Azure Stack 上的应用服务的应用程序。 服务管理员只能在管理员门户中完成的功能与资源提供程序对应用服务的管理相关。 这些功能包括添加容量、配置部署源以及添加辅助角色层和 SKU。
>
> 至于第三个技术预览版，若要创建 Web 应用、API 应用和 Azure Functions 应用，必须使用租户门户并有租户订阅。

1. 在 Azure Stack 租户门户中，单击“+ 创建资源” > “Web + 移动” > “Web 应用”。

2. 在“Web 应用”边栏选项卡上的“Web 应用”框中键入名称。

3. 在“资源组”下单击“新建”。 在“资源组”框中键入名称。

4. 单击“应用服务计划/位置” > “新建”。

5. 在“应用服务计划”边栏选项卡上的“应用服务计划”框中键入名称。

6. 单击“定价层” > “免费共享”或“共享共享” > “选择” > “确定” > “创建”。

7. 在不到一分钟之内，新的 Web 应用的磁贴将显示在仪表板上。 单击该磁贴。

8. 在“Web 应用”边栏选项卡上单击“浏览”，查看此应用的默认网站。

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>部署 WordPress、DNN 或 Django 网站（可选）

1. 在 Azure Stack 租户门户中，单击“+”，转到 Azure 市场，部署 Django 网站并等待成功完成。 Django Web 平台使用基于文件系统的数据库。 它不需要任何其他资源提供程序，如 SQL 或 MySQL。

2. 如果还部署了 MySQL 资源提供程序，则可从市场部署 WordPress 网站。 当系统提示输入数据库参数时，输入作为用户名*User1\@Server1*、 用户名称与所选的服务器名称。

3. 如果还部署了 SQL Server 资源提供程序，则可从市场部署 DNN 网站。 当系统提示输入数据库参数时，请在运行 SQL Server 的计算机中选择连接到资源提供程序的数据库。

## <a name="next-steps"></a>后续步骤

还可以试用其他[平台即服务 (PaaS) 服务](azure-stack-offer-services-overview.md)。

- [SQL Server 资源提供程序](azure-stack-sql-resource-provider-deploy.md)
- [MySQL 资源提供程序](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: https://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: https://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: https://go.microsoft.com/fwlink/?LinkId=733525

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
