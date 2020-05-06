---
title: 在 Azure Stack Hub 中部署应用服务
description: 了解如何在 Azure Stack Hub 中部署应用服务。
author: bryanla
ms.topic: article
ms.date: 05/05/2020
ms.author: bryanla
ms.reviewer: anwestg
ms.lastreviewed: 04/13/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 4f9bdf84f73bf06c42f3b6910e0faad83a9bbd80
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847975"
---
# <a name="deploy-app-service-in-azure-stack-hub"></a>在 Azure Stack Hub 中部署应用服务

[!INCLUDE [Azure Stack hub update reminder](../includes/app-service-hub-update-banner.md)]

> [!IMPORTANT]
> 在运行资源提供程序安装程序之前，必须先完成中的步骤，[然后才能开始](azure-stack-app-service-before-you-get-started.md)操作

::: zone pivot="state-connected"
在本文中，你将了解如何在 Azure Stack Hub 中部署应用服务，从而使你的用户能够创建 Web、API 和 Azure Functions 应用程序。 你需要：

- 使用本文中所述的步骤，将[应用服务资源提供程序](azure-stack-app-service-overview.md)添加到 Azure Stack 中心部署。
- 安装应用服务资源提供程序后，可以将其包括在套餐和计划中。 然后，用户可以通过订阅获取服务并开始创建应用。

## <a name="run-the-app-service-resource-provider-installer"></a>运行应用服务资源提供程序安装程序

安装应用服务资源提供程序至少需要一小时。 所需时长取决于部署的角色实例数。 部署期间，安装程序运行以下任务：

- 在默认提供程序订阅中注册所需的资源提供程序
- 授予参与者对应用服务标识应用程序的访问权限
- 创建资源组和虚拟网络（如有必要）
- 为应用服务安装项目、使用情况服务和资源混合创建存储帐户和容器
- 下载应用服务项目并将其上传到应用服务存储帐户
- 部署应用服务
- 注册使用情况服务
- 为应用服务创建 DNS 条目
- 注册应用服务管理员和租户资源提供程序
- 注册库项-Web、API、Function App、应用服务计划、WordPress、DNN、Orchard 和 Django 应用程序

若要部署应用服务资源提供程序，请执行以下步骤：

1. 从可访问 Azure Stack 中心管理员 Azure 资源管理终结点的计算机上以管理员身份运行 appservice。

2. 选择“部署应用服务或升级到最新版本”。****

    ![应用服务安装程序][1]

3. 查看并接受 Microsoft 软件许可条款，然后选择 "**下一步**"。

4. 查看并接受第三方许可条款，然后选择 "**下一步**"。

5. 请确保应用服务云配置信息正确无误。 如果在 ASDK 部署过程中使用了默认设置，则可接受默认值。 但是，如果在部署 ASDK 时自定义了选项，或者在 Azure Stack 集线器集成系统上部署了选项，则必须在此窗口中编辑这些值以反映差异。

   例如，如果使用域后缀 mycloud.com，则 Azure Stack 中心租户 Azure 资源管理器终结点必须更改为 "管理"。&lt;mycloud.com&gt;。 查看这些设置，然后选择“下一步”以保存设置。****

   ![应用服务安装程序][2]

6. 在 "下一应用服务安装程序" 页上，你将连接到 Azure Stack 中心：

    1. 选择要使用的连接方法-**凭据**或**服务主体**
 
        - **Credential**
            - 如果使用 Azure Active Directory （Azure AD），请输入在部署 Azure Stack 中心时提供的 Azure AD 管理员帐户和密码。 选择“连接”  。
            - 如果使用 Active Directory 联合身份验证服务 (AD FS)，请提供管理员帐户。 例如，cloudadmin@azurestack.local 。 输入密码，然后选择 "**连接**"。

        - **服务主体**
            - 你使用的服务主体**必须**对**默认提供程序订阅**具有**所有者**权限
            - 提供**服务主体 ID**、**证书文件**和**密码**，然后选择 "**连接**"。

    1. 在**Azure Stack 中心订阅**"中，选择**默认提供程序订阅**。  **必须**在**默认提供程序订阅**中部署 Azure Stack 中心的 Azure App Service。

    1. 在**Azure Stack 集线器位置**，选择与要部署到的区域相对应的位置。 例如，若要部署到 ASDK，请选择“本地”。****

    ![应用服务安装程序][3]

7. 现在，可以部署到[使用这些步骤](azure-stack-app-service-before-you-get-started.md#virtual-network)配置的现有虚拟网络中，或者让应用服务安装程序创建新的虚拟网络和子网。 若要创建 VNet，请执行以下步骤：

   a. 选择“使用默认设置创建 VNet”，接受默认值，然后选择“下一步”。********

   b. 也可选择“使用现有的 VNet 和子网”。**** 完成以下操作：

     - 选择包含虚拟网络的**资源组**。
     - 选择要部署到其中的**虚拟网络**的名称。
     - 为每个所需角色子网选择正确的“子网”值。****
     - 选择“**下一页**”。

   ![应用服务安装程序][4]

8. 输入文件共享的信息，然后选择“下一步”****。 文件共享的地址必须使用文件服务器的完全限定域名 (FQDN) 或 IP 地址。 例如 \\\appservicefileserver.local.cloudapp.azurestack.external\websites，或 \\\10.0.0.1\websites。  如果使用已加入域的文件服务器，则必须提供包含域的完整用户名。 例如 myfileserverdomain\FileShareOwner。

   >[!NOTE]
   >在继续下一步之前，安装程序会尝试测试与文件共享的连接。 不过，如果是部署到现有的虚拟网络，此连接测试可能会失败。 系统会发出警告，并提示你继续操作。 如果文件共享信息正确，请继续部署。

   ![应用服务安装程序][7]

9. 在下一“应用服务安装程序”页上，执行以下步骤：

   a. 在 "**标识应用程序 ID** " 框中，输入你作为[必备组件](azure-stack-app-service-before-you-get-started.md)的一部分创建的标识应用程序的 GUID。

   b. 在“标识应用程序证书文件”**** 框中，输入（或浏览到）证书文件的位置。

   c. 在“标识应用程序证书密码”**** 框中，输入证书的密码。 此密码是在使用脚本创建证书时记下的密码。

   d. 在“Azure 资源管理器根证书文件”**** 框中，输入（或浏览到）证书文件的位置。

   e. 选择“**下一页**”。

   ![应用服务安装程序][9]

10. 对于三个证书文件框的每一个框，请选择“浏览”**** 并导航到相应的证书文件。 必须为每个证书提供密码。 这些证书是在[Azure Stack 集线器上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)中创建的证书。 输入所有信息后，选择“下一步”****。

    | Box | 证书文件名示例 |
    | --- | --- |
    | **应用服务默认 SSL 证书文件** | \_.appservice.local.AzureStack.external.pfx |
    | **应用服务 API SSL 证书文件** | api.appservice.local.AzureStack.external.pfx |
    | **应用服务发布者 SSL 证书文件** | ftp.appservice.local.AzureStack.external.pfx |

    如果在创建证书时使用了其他域后缀，证书文件名不要使用 *local.AzureStack.external*， 而要改用自定义域信息。

    ![应用服务安装程序][10]

11. 为用于托管应用服务资源提供程序数据库的服务器实例输入 SQL Server 详细信息，然后选择“下一步”****。 安装程序将验证 SQL 连接属性。<br><br>在继续下一步之前，应用服务安装程序会尝试测试与 SQL Server 的连接。 如果是部署到现有的虚拟网络，此连接测试可能会失败。 系统会发出警告，并提示你继续操作。 如果 SQL Server 信息正确，请继续部署。

    ![应用服务安装程序][11]

12. 查看角色实例和 SKU 选项。 默认情况下，将使用生产部署中每个角色的最小实例数和最小 SKU 填充默认值。  对于 ASDK 部署，你可以将实例向下扩展到更低的 Sku，以减少核心和内存提交，但会遇到性能下降的情况。 提供 vCPU 和内存要求摘要是为了帮助你规划部署。 进行选择后，请选择“下一步”。****

    >[!NOTE]
    >对于生产部署，请遵循[Azure Stack 中心的 Azure App Service server 角色的容量规划](azure-stack-app-service-capacity-planning.md)中的指南。

    | 角色 | 最小实例数 | 最小 SKU | 说明 |
    | --- | --- | --- | --- |
    | 控制器 | 2 | Standard_A4_v2-（4核，8192 MB） | 管理和维护应用服务云的运行状况。 |
    | 管理 | 1 | Standard_D3_v2-（4核，14336 MB） | 管理应用服务 Azure 资源管理器和 API 终结点、门户扩展（管理员门户、租户门户、Functions 门户）和数据服务。 为了支持故障转移，已将建议的实例数增加到 2 个。 |
    | 发布者 | 1 | Standard_A2_v2-（2核，4096 MB） | 通过 FTP 和 Web 部署发布内容。 |
    | FrontEnd | 1 | Standard_A4_v2-（4核，8192 MB） | 将请求路由到应用服务应用。 |
    | 共享辅助角色 | 1 | Standard_A4_v2-（4核，8192 MB） | 托管 Web 应用或 API 应用和 Azure Functions 应用。 可能需要添加更多实例。 作为操作员，可以定义产品/服务，并选择任何 SKU 层。 这些层必须至少具有一个 vCPU。 |

    ![应用服务安装程序][13]

    > [!NOTE]
    > **Windows Server 2016 Core 不是受支持的平台映像，不适用于 Azure Stack 中心的 Azure App Service。 不要将评估映像用于生产部署。**

13. 在“选择平台映像”**** 框中选择 Windows Server 2016 虚拟机 (VM) 部署映像，该映像是应用服务云的计算资源提供程序提供的映像之一。 选择“**下一页**”。

14. 在下一“应用服务安装程序”页上，执行以下步骤：

     a. 输入辅助角色 VM 管理员用户名和密码。

     b. 输入其他角色 VM 管理员用户名和密码。

     c. 选择“**下一页**”。

    ![应用服务安装程序][15]

15. 在“应用服务安装程序”摘要页上，执行以下步骤：

    a. 验证所做的选择。 若要进行更改，请使用“上一步”**** 按钮访问前面的页面。

    b. 如果配置正确，则选中此复选框。

    c. 若要开始部署，请选择“下一步”****。

    ![应用服务安装程序][16]

16. 在下一“应用服务安装程序”页上，执行以下步骤：

    a. 跟踪安装进度。 Azure Stack 集线器上的应用服务最长可能需要240分钟才能根据默认的 Windows 2016 Datacenter 映像的默认选择和使用时间进行部署。

    b. 安装程序成功完成后，选择 "**退出**"。

    ![应用服务安装程序][17]

## <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]
> 如果已经为应用服务 RP 提供 SQL Always On 实例，则必须**** [将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)并同步数据库，以免在进行数据库故障转移时丢失服务。

如果部署到现有虚拟网络并使用内部 IP 地址连接到文件服务器，则必须添加出站安全规则。 此规则允许辅助角色子网和文件服务器之间的 SMB 流量。 在管理员门户中，转到 WorkersNsg 网络安全组并添加具有以下属性的出站安全规则：

- 源：任何
- 源端口范围：*
- 目标： IP 地址
- 目标 IP 地址范围：文件服务器的 IP 范围
- 目标端口范围：445
- 协议：TCP
- 操作：允许
- 优先级：700
- 名称：Outbound_Allow_SMB445

## <a name="validate-the-app-service-on-azure-stack-hub-installation"></a>验证 Azure Stack 集线器安装上的应用服务

1. 在 Azure Stack 中心管理员门户中，请参阅**管理-应用服务**。

2. 在“概述”中，在“状态”下，检查“状态”**** 是否显示了“所有角色已就绪”****。

    ![应用服务管理](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack-hub"></a>Azure Stack 集线器上的测试驱动器应用服务

部署并注册应用服务资源提供程序后，对其进行测试以确保用户可以部署 Web 应用和 API 应用。

>[!NOTE]
>需要创建一个套餐，其中的计划包含 Microsoft.Web 命名空间。 此外还需订阅此套餐的租户订阅。 有关详细信息，请参阅[创建套餐](azure-stack-create-offer.md)和[创建计划](azure-stack-create-plan.md)。
>
>你*必须*具有租户订阅才能创建在 Azure Stack Hub 上使用应用服务的应用。 服务管理员只能在管理员门户中完成的任务与资源提供程序对应用服务的管理相关。 这包括添加容量、配置部署源以及添加辅助角色层和 SKU。
>
>若要创建 web 应用、API 应用和 Azure Functions 应用，必须使用用户门户并拥有租户订阅。
>

若要创建测试性 Web 应用，请执行以下步骤：

1. 在 Azure Stack 集线器用户门户中，选择 " **+ 创建资源** > **Web + 移动** > **Web 应用**"。

2. 在“Web 应用”下的“Web 应用”中输入一个名称。********

3. 在“资源组”下，选择“新建”********。 输入**资源组**的名称。

4. 选择 "**应用服务计划/位置** > " "**新建**"。

5. 在“应用服务计划”下，输入******应用服务计划**的名称。

6. 选择**定价层** > **免费-共享**或**共享-共享** > **选择** > **"确定" "** > **创建**"。

7. 此时新 Web 应用的磁贴会显示在仪表板上。 选择磁贴。

8. 在“Web 应用”**** 上选择“浏览”****，查看此应用的默认网站。

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>部署 WordPress、DNN 或 Django 网站（可选）

1. 在 Azure Stack 集线器用户门户中，选择**+**""，然后前往 Azure Marketplace，部署 Django 网站，并等待部署完成。 Django Web 平台使用基于文件系统的数据库。 它不需要任何其他资源提供程序，如 SQL 或 MySQL。

2. 如果还部署了 MySQL 资源提供程序，则可从市场部署 WordPress 网站。 当系统提示输入数据库参数时，请输入用户名，其格式为 *User1\@Server1*（使用所选的用户名和服务器名称）。

3. 如果还部署了 SQL Server 资源提供程序，则可从市场部署 DNN 网站。 当系统提示输入数据库参数时，请在运行 SQL Server 的计算机中选择连接到资源提供程序的数据库。
::: zone-end



<!----------------------------------------- DISCONNECTED PIVOT -------------------------------------------->
::: zone pivot="state-disconnected"
本文介绍如何将[Azure App Service 资源提供程序](azure-stack-app-service-overview.md)部署到 Azure Stack 中心环境，该环境是：
- 未连接到 Internet。
- 受 Active Directory 联合身份验证服务 (AD FS) 保护。

若要将 Azure App Service 资源提供程序添加到脱机 Azure Stack 中心部署，必须完成以下顶级任务：

1. 完成[先决条件步骤](azure-stack-app-service-before-you-get-started.md)（例如购买证书，可能需要数天才能接收到）。
2. [下载并提取安装文件和帮助器文件](azure-stack-app-service-before-you-get-started.md)到连接 Internet 的计算机。
3. 创建脱机安装包。
4. 运行 appservice.exe 安装程序文件。

## <a name="create-an-offline-installation-package"></a>创建脱机安装包

若要在脱机环境中部署 Azure App Service，请先在连接到 internet 的计算机上创建脱机安装包。

1. 在连接到 Internet 的计算机上运行 AppService.exe 安装程序。 

2. 选择 "**高级** > **创建脱机安装包**"。 此步骤需要几分钟才能完成。

    ![在 Azure App Service 安装程序中创建脱机包][31]

3. Azure App Service 安装程序将创建一个脱机安装包并显示其路径。 可以选择“打开文件夹”，在文件资源管理器中打开该文件夹。****

    ![Azure App Service 安装程序中成功生成脱机安装包](media/azure-stack-app-service-deploy-offline/image02.png)

4. 将安装程序（AppService）和脱机安装包复制到已连接到 Azure Stack 集线器的计算机。

## <a name="complete-the-offline-installation-of-azure-app-service-on-azure-stack-hub"></a>在 Azure Stack 集线器上完成 Azure App Service 的脱机安装

1. 在可访问 Azure Stack 中心管理员 Azure 资源管理终结点的计算机上以管理员身份运行 appservice。

1. 选择 "**高级** > " "**完全脱机安装**"。

    ![Azure App Service 安装程序中完成脱机安装][32]

1. 浏览到前面创建的脱机安装包的位置，选择选择“下一步”。****

    ![指定脱机安装包路径 im Azure App Service 安装程序](media/azure-stack-app-service-deploy-offline/image04.png)

1. 查看并接受 Microsoft 软件许可条款，然后选择“下一步”****。

1. 查看并接受第三方许可条款，然后选择“下一步”****。


1. 请确保 Azure App Service 的云配置信息正确。 如果在 ASDK 部署过程中使用了默认设置，则此处可以接受默认值。 但是，如果您在部署 Azure Stack 集线器或部署到集成系统时自定义了选项，则必须在此窗口中编辑这些值以反映这些更改。 例如，如果使用域后缀 mycloud.com，则 Azure Stack 中心租户 Azure 资源管理器终结点必须更改为`management.<region>.mycloud.com`。 确认信息后，选择“下一步”****。

    ![在 Azure App Service 安装程序中配置 Azure App Service 云][33]

1. 在 "下一应用服务安装程序" 页上，你将连接到 Azure Stack 中心：

    1. 选择要使用的连接方法-**凭据**或**服务主体**
        - **Credential**
            - 如果使用 Azure Active Directory （Azure AD），请输入在部署 Azure Stack 中心时提供的 Azure AD 管理员帐户和密码。 选择“连接”  。
            - 如果使用 Active Directory 联合身份验证服务 (AD FS)，请提供管理员帐户。 例如，cloudadmin@azurestack.local 。 输入密码，然后选择 "**连接**"。
        - **服务主体**
            - 你使用的服务主体**必须**对**默认提供程序订阅**具有**所有者**权限
            - 提供**服务主体 ID**、**证书文件**和**密码**，然后选择 "**连接**"。

    1. 在**Azure Stack 中心订阅**"中，选择**默认提供程序订阅**。  **必须**在**默认提供程序订阅**中部署 Azure Stack 中心的 Azure App Service。

    1. 在**Azure Stack 集线器位置**，选择与要部署到的区域相对应的位置。 例如，若要部署到 ASDK，请选择“本地”。****

1. 可以允许 Azure App Service 安装程序创建虚拟网络和关联的子网。 或者，可以部署到通过[这些步骤](azure-stack-app-service-before-you-get-started.md#virtual-network)配置的现有虚拟网络。
   - 若要使用 Azure App Service 安装程序方法，请选择 "**使用默认设置创建 VNet**"，接受默认值，然后选择 "**下一步**"。
   - 若要部署到现有网络，请选择“使用现有 VNet 和子网”，然后：****
       1. 选择包含该虚拟网络的“资源组”选项。****
       2. 选择要部署到其中的**虚拟网络**的名称。
       3. 为每个所需角色子网选择正确的“子网”值。****
       4. 选择“**下一页**”。

      ![Azure App Service 安装程序中的虚拟网络和子网信息][35]

1. 输入文件共享的信息，然后选择“下一步”****。 文件共享的地址必须使用文件服务器的完全限定域名 (FQDN) 或 IP 地址。 例如 \\\appservicefileserver.local.cloudapp.azurestack.external\websites，或 \\\10.0.0.1\websites。  如果使用已加入域的文件服务器，则必须提供包含域的完整用户名。 例如：`<myfileserverdomain>\<FileShareOwner>`。

    > [!NOTE]
    > 在继续下一步之前，安装程序会尝试测试与文件共享的连接。 但是，如果前面已选择部署到现有虚拟网络，则安装程序可能无法连接到文件共享，并显示警告来询问是否继续。 验证文件共享信息，如果正确，请继续。

   ![Azure App Service 安装程序中的文件共享信息][38]

1. 在下一页上执行以下操作：
    1. 在 "**标识应用程序 ID** " 框中，输入你作为[必备组件](azure-stack-app-service-before-you-get-started.md)的一部分创建的标识应用程序的 GUID。
    1. 在“标识应用程序证书文件”**** 框中，输入（或浏览到）证书文件的位置。
    1. 在“标识应用程序证书密码”**** 框中，输入证书的密码。 此密码是在使用脚本创建证书时记下的密码。
    1. 在“Azure 资源管理器根证书文件”**** 框中，输入（或浏览到）证书文件的位置。
    1. 选择“**下一页**”。

    ![在 Azure App Service 安装程序中输入应用程序 ID 和证书信息][40]

1. 对于三个证书文件框的每一个框，请选择“浏览”**** 并导航到相应的证书文件。 必须为每个证书提供密码。 这些证书是在[Azure Stack 集线器上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)中创建的证书。 输入所有信息后，选择“下一步”****。

    | Box | 证书文件名示例 |
    | --- | --- |
    | **应用服务默认 SSL 证书文件** | \_.appservice.local.AzureStack.external.pfx |
    | **应用服务 API SSL 证书文件** | api.appservice.local.AzureStack.external.pfx |
    | **应用服务发布者 SSL 证书文件** | ftp.appservice.local.AzureStack.external.pfx |

    如果在创建证书时使用了其他域后缀，证书文件名不要使用 *local.AzureStack.external*， 而要改用自定义域信息。

    ![在 Azure App Service 安装程序中输入 SSL 证书信息][41]

1. 输入用于承载 Azure App Service 资源提供程序数据库的服务器实例的 SQL Server 详细信息，然后选择 "**下一步**"。 安装程序将验证 SQL 连接属性。 **必须**输入内部 IP 或 FQDN 作为 SQL Server 名称。

    > [!NOTE]
    > 在继续下一步之前，安装程序会尝试测试连接到运行 SQL Server 的计算机。 但是，如果前面已选择部署到现有虚拟网络，则安装程序可能无法连接到运行 SQL Server 的计算机，并显示警告来询问是否继续。 验证 SQL Server 信息，如果正确，请继续。
    >
    > 从 Azure App Service Azure Stack 中心1.3 开始，安装程序将检查运行 SQL Server 的计算机是否在 SQL Server 级别启用了数据库包含。 如果未启用，则会出现以下异常提示：
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;
    >    GO
    >    RECONFIGURE;
    >    GO
    >    ***********************************************************
    > ```
    > 有关详细信息，请参阅[Azure Stack 中心 1.3 Azure App Service 的发行说明](azure-stack-app-service-release-notes-update-three.md)。

    ![在 Azure App Service 安装程序中输入 SQL Server 信息][42]

1. 查看角色实例和 SKU 选项。 默认情况下，将使用生产部署中每个角色的最小实例数和最小 SKU 填充默认值。  对于 ASDK 部署，你可以将实例向下扩展到更低的 Sku，以减少核心和内存提交，但会遇到性能下降的情况。  提供 vCPU 和内存要求摘要是为了帮助你规划部署。 进行选择后，请选择“下一步”。****

     > [!NOTE]
     > 对于生产部署，请按照[Azure Stack 中心的 Azure App Service server 角色的容量规划](azure-stack-app-service-capacity-planning.md)中的指南进行操作。
     >
     >

    
    | 角色 | 最小实例数 | 最小 SKU | 说明 |
    | --- | --- | --- | --- |
    | 控制器 | 2 | Standard_A4_v2-（4核，8192 MB） | 管理和维护应用服务云的运行状况。 |
    | 管理 | 1 | Standard_D3_v2-（4核，14336 MB） | 管理应用服务 Azure 资源管理器和 API 终结点、门户扩展（管理员门户、租户门户、Functions 门户）和数据服务。 为了支持故障转移，已将建议的实例数增加到 2 个。 |
    | 发布者 | 1 | Standard_A2_v2-（2核，4096 MB） | 通过 FTP 和 Web 部署发布内容。 |
    | FrontEnd | 1 | Standard_A4_v2-（4核，8192 MB） | 将请求路由到应用服务应用。 |
    | 共享辅助角色 | 1 | Standard_A4_v2-（4核，8192 MB） | 托管 Web 应用或 API 应用和 Azure Functions 应用。 可能需要添加更多实例。 作为操作员，可以定义产品/服务，并选择任何 SKU 层。 这些层必须至少具有一个 vCPU。 |

    ![在 Azure App Service 安装程序中设置角色层和 SKU 选项][44]

1. 在 "**选择平台映像**" 框中，从 Azure App Service 云的计算资源提供程序中提供的映像中选择部署 Windows Server 2016 虚拟机（VM）映像。 选择“**下一页**”。

    > [!NOTE]
    > Windows Server 2016 Core 不是受支持的平台映像，*不适*用于 Azure Stack 中心的 Azure App Service。  请勿将评估映像用于生产部署。 Azure Stack 中心的 Azure App Service 要求在用于部署的映像上激活 Microsoft .NET 3.5.1 SP1。 通过“市场”发布的 Windows Server 2016 映像未启用此功能。 因此，必须在预先启用此功能的情况下创建并使用 Windows Server 2016 映像。
    >
    > 有关创建自定义映像并将其添加到 Marketplace 的详细信息，请参阅[将自定义 VM 映像添加到 Azure Stack 集线器](azure-stack-add-vm-image.md)。 将映像添加到 Marketplace 时，请确保指定以下各项：
    >
    >- 发布服务器 = MicrosoftWindowsServer
    >- 提议 = WindowsServer
    >- SKU = 2016-Datacenter
    >- 版本 = 指定 "最新" 版本

1. 在下一页上执行以下操作：
     1. 输入辅助角色 VM 管理员用户名和密码。
     2. 输入其他角色 VM 管理员用户名和密码。
     3. 选择“**下一页**”。

    ![在 Azure App Service 安装程序中输入角色 VM 管理员][46]

1. 在摘要页上执行以下操作：
    1. 验证所做的选择。 若要进行更改，请使用“上一步”**** 按钮访问前面的页面。
    2. 如果配置正确，则选中此复选框。
    3. 若要开始部署，请选择“下一步”****。

    ![Azure App Service 安装程序中所做选择的摘要][47]

1. 在下一页上执行以下操作：
    1. 跟踪安装进度。 Azure Stack 集线器上的应用服务最长可能需要240分钟才能根据默认的 Windows 2016 Datacenter 映像的默认选择和使用时间进行部署。

    2. 安装程序完成运行后，请选择“退出”。****

    ![跟踪 Azure App Service 安装程序中的安装过程][48]

## <a name="post-deployment-steps"></a>部署后步骤

> [!IMPORTANT]
> 如果已向 Azure App Service RP 提供 SQL Always On 实例，则**必须**[将 appservice_hosting 和 appservice_metering 数据库添加到可用性组](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database)。 此外，必须同步数据库，以防止在发生数据库故障转移时丢失任何服务。

如果选择部署到现有虚拟网络和内部 IP 地址以连接到文件服务器，则必须添加出站安全规则，以便在工作子网和文件服务器之间启用 SMB 流量。 在管理员门户中，转到 WorkersNsg 网络安全组并添加具有以下属性的出站安全规则：

- 源：任何
- 源端口范围：*
- 目标： IP 地址
- 目标 IP 地址范围：文件服务器的 IP 范围
- 目标端口范围：445
- 协议：TCP
- 操作：允许
- 优先级：700
- 名称：Outbound_Allow_SMB445

## <a name="validate-the-azure-app-service-on-azure-stack-hub-installation"></a>验证 Azure Stack 中心安装的 Azure App Service

1. 在 Azure Stack 中心管理员门户中，请参阅**管理-应用服务**。

1. 在“概述”中，在“状态”下，检查“状态”**** 是否显示了“所有角色已就绪”****。

    ![Azure App Service 管理概述](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-azure-app-service-on-azure-stack-hub"></a>Azure Stack 集线器上的测试驱动器 Azure App Service

部署并注册 Azure App Service 资源提供程序后，请对其进行测试，以确保用户可以部署 web 应用和 API 应用。

> [!NOTE]
> 必须创建一个套餐，其中的计划包含 Microsoft.Web 命名空间。 然后，需要有订阅此套餐的租户订阅。 有关详细信息，请参阅[创建套餐](azure-stack-create-offer.md)和[创建计划](azure-stack-create-plan.md)。
>
> 你*必须*具有租户订阅才能在 Azure Stack 中心创建使用 Azure App Service 的应用。 服务管理员只能在管理员门户中完成的功能与 Azure App Service 的资源提供程序管理有关。 这些功能包括添加容量、配置部署源以及添加辅助角色层和 SKU。
>
> 从第三个技术预览版中，若要创建 web 应用、API 应用和 Azure Functions 应用，必须使用用户门户并拥有租户订阅。

1. 在 Azure Stack 集线器用户门户中，选择 " **+ 创建资源** > **Web + 移动** > **Web 应用**"。

1. 在“Web 应用”**** 边栏选项卡上的“Web 应用”**** 框中键入名称。

1. 在“资源组”下，选择“新建”********。 在“资源组”**** 框中键入名称。

1. 选择 "**应用服务计划/位置** > " "**新建**"。

1. 在“应用服务计划”**** 边栏选项卡上的“应用服务计划”**** 框中键入名称。

1. 选择**定价层** > **免费-共享**或**共享-共享** > **选择** > **"确定" "** > **创建**"。

1. 在不到一分钟之内，新 Web 应用的磁贴将显示在仪表板上。 选择磁贴。

1. 在“Web 应用”**** 边栏选项卡上选择“浏览”****，查看此应用的默认网站。

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>部署 WordPress、DNN 或 Django 网站（可选）

1. 在 Azure Stack 集线器用户门户中，选择**+**""，然后前往 Azure Marketplace，部署 Django 网站并等待成功完成。 Django Web 平台使用基于文件系统的数据库。 它不需要任何其他资源提供程序，如 SQL 或 MySQL。

1. 如果还部署了 MySQL 资源提供程序，则可从 Azure 市场部署 WordPress 网站。 当系统提示输入数据库参数时，请输入用户名，其格式为 *User1\@Server1*（使用所选的用户名和服务器名称）。

1. 如果还部署了 SQL Server 资源提供程序，则可从 Azure 市场部署 DNN 网站。 当系统提示输入数据库参数时，请在运行 SQL Server 的计算机中选择连接到资源提供程序的数据库。

::: zone-end

## <a name="next-steps"></a>后续步骤

准备 Azure Stack 集线器上的应用服务的其他管理操作：

- [容量规划](azure-stack-app-service-capacity-planning.md)
- [配置部署源](azure-stack-app-service-configure-deployment-sources.md)

<!-- Connected image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png

<!-- Disconnected image references-->
[31]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[32]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[33]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[34]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[35]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[36]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[37]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[38]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[39]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[40]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[41]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[42]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[43]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[44]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[45]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[46]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[47]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[48]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
