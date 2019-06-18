---
title: 使用 Azure 和 Azure Stack 部署混合云 | Microsoft Docs
description: 了解如何使用 Azure 和 Azure Stack 部署混合云。
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: db2ceabfdd49ed113a467923a3772423ab1ca65a
ms.sourcegitcommit: 593d40bccf1b2957a763017a8a2d7043f8d8315c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152560"
---
# <a name="tutorial-deploy-a-hybrid-cloud-solution-with-azure-and-azure-stack"></a>教程：使用 Azure 和 Azure Stack 部署混合云解决方案

适用于：  Azure Stack 集成系统和 Azure Stack 开发工具包

本教程介绍如何部署使用 Azure 公有云和 Azure Stack 私有云的混合云解决方案。

使用混合云解决方案，可以结合私有云在合规性方面的优势与公有云的可伸缩性。 此外，您的开发人员可以充分利用 Microsoft 开发人员生态系统，并将他们的技能应用到在本地和云环境。

## <a name="overview-and-assumptions"></a>概述和假设

可以遵循本教程设置工作流，让开发人员将相同的 Web 应用程序部署到公有云和私有云。 此应用程序可以访问私有云中托管的、无法通过 Internet 路由的网络。 这些 Web 应用程序受到监视；出现流量高峰时，某个程序会修改 DNS 记录，以将流量重定向到公有云。 如果流量下降到高峰出现之前的水平，则流量将路由回到私有云。

本教程涵盖以下任务：

> [!div class="checklist"]
> - 部署混合连接的 SQL Server 数据库服务器。
> - 将全球 Azure 中的 Web 应用连接到混合网络。
> - 为跨云缩放配置 DNS。
> - 为跨云缩放配置 SSL 证书。
> - 配置并部署 Web 应用程序。
> - 创建流量管理器配置文件，并根据跨云缩放对其进行配置。
> - 针对增大的流量设置 Application Insights 监视和警报。
> - 配置全球 Azure 与 Azure Stack 之间的自动流量切换。

### <a name="assumptions"></a>假设

本教程假设你对全球 Azure 和 Azure Stack 有基本的了解。 若要在开始本教程之前了解详细信息，请查看以下文章：

 - [Azure 简介](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack 的重要概念](../operator/azure-stack-overview.md)

本教程还假设你有一个 Azure 订阅。 如果你没有订阅，则可以[创建一个免费帐户](https://azure.microsoft.com/free/)在开始之前。

## <a name="prerequisites"></a>必备组件

在开始本教程之前，请确保可以满足以下要求：

- Azure Stack 开发工具包 (ASDK)，或 Azure Stack 集成系统的订阅。 若要部署 Azure Stack 开发工具包，请遵照[使用安装程序部署 ASDK](../asdk/asdk-install.md) 中的说明操作。
- Azure Stack 安装中应包含以下组件：
  - Azure 应用服务。 请与 Azure Stack 操作员协作，在环境中部署并配置 Azure 应用服务。 在本教程中，应用服务必须至少有一 (1) 个可用的专用辅助角色。
  - Windows Server 2016 映像
  - 包含 Microsoft SQL Server 映像的 Windows Server 2016
  - 相应的计划和产品/服务
  - Web 应用程序的域名。 如果没有域名，可以从 GoDaddy、Bluehost 和 InMotion 等域提供商购买。
- 受信任的证书颁发机构（例如 LetsEncrypt）为域颁发的 SSL 证书。
- 与 SQL Server 数据库通信且支持 Application Insights 的 Web 应用程序。 可以从 GitHub 下载 [dotnetcore-sqldb-tutorial](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial) 示例应用。
- Azure 虚拟网络与 Azure Stack 虚拟网络之间的混合网络。 有关详细说明，请参阅[使用 Azure 和 Azure Stack 配置混合云连接](azure-stack-solution-hybrid-connectivity.md)。

- Azure Stack 中包含专用生成代理的混合持续集成/持续部署 (CI/CD) 管道。 有关详细说明，请参阅[使用 Azure 和 Azure Stack 应用程序配置混合云标识](azure-stack-solution-hybrid-identity.md)

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>部署混合连接的 SQL Server 数据库服务器

1. 登录到 Azure Stack 用户门户。

2. 在“仪表板”中选择“市场”。  

    ![Azure Stack 市场](media/azure-stack-solution-hybrid-cloud/image1.png)

3. 在“市场”中选择“计算”，然后选择“更多”。    在“更多”下面，选择“免费 SQL Server 许可证:   Windows Server 上的 SQL Server 2017 Developer”映像。

    ![选择虚拟机映像](media/azure-stack-solution-hybrid-cloud/image2.png)

4. 在“免费 SQL Server 许可证:   Windows Server 上的 SQL Server 2017 Developer”中，选择“创建”。

5. 在“基本信息”>“配置基本设置”中，提供虚拟机 (VM) 的**名称**、SQL Server SA 的**用户名**，以及 SA 的**密码**。   在“订阅”下拉列表中，选择要部署到的订阅。  对于“资源组”，请使用“选择现有项”，并将 VM 放到 Azure Stack Web 应用所在的同一资源组中。  

    ![配置 VM 的基本设置](media/azure-stack-solution-hybrid-cloud/image3.png)

6. 在“大小”下面，选择 VM 的大小。  对于本教程，建议使用 A2_Standard 或 DS2_V2_Standard。

7. 在“设置”>“配置可选功能”下面配置以下设置： 

   - **存储帐户**： 根据需要创建新帐户。
   - **虚拟网络**

     > [!Important]  
     > 请务必将 SQL Server VM 部署到 VPN 网关所在的同一虚拟网络中。

   - **公共 IP 地址**。 可以使用默认设置。
   - **网络安全组** (NSG)。 创建新 NSG。
   - **扩展和监视**。 保留默认设置。
   - **诊断存储帐户**。 根据需要创建新帐户。
   - 选择“确定”以保存配置。 

     ![配置可选功能](media/azure-stack-solution-hybrid-cloud/image4.png)

1. 在“SQL Server 设置”下面配置以下设置： 
   - 对于“SQL 连接”，请选择“公共(Internet)”。  
   - 对于“端口”，请保留默认值 **1433**。 
   - 对于“SQL 身份验证”，请选择“启用”。  

     > [!Note]  
     > 启用 SQL 身份验证时，应会自动填充在“基本信息”中配置的“SQLAdmin”信息。 

   - 对于剩余的设置，请保留默认值。 选择“确定”  。

     ![配置 SQL Server 设置](media/azure-stack-solution-hybrid-cloud/image5.png)

9. 在“摘要”中检查虚拟机配置，然后选择“确定”开始部署。  

    ![配置摘要](media/azure-stack-solution-hybrid-cloud/image6.png)

10. 创建新 VM 需要花费一段时间。 可以在“虚拟机”中查看 VM 的状态。 

    ![虚拟机](media/azure-stack-solution-hybrid-cloud/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack"></a>在 Azure 和 Azure Stack 中创建 Web 应用

Azure 应用服务简化了运行和管理 Web 应用程序的过程。 由于 Azure Stack 与 Azure 相一致，因此，应用服务可在这两个环境中运行。 你将使用应用服务来托管应用程序。

### <a name="create-web-apps"></a>创建 Web 应用

1. 遵照[在 Azure 中管理应用服务计划](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan)中的说明，在 Azure 中创建 Web 应用。 请务必将 Web 应用放到混合网络所在的同一订阅和资源组中。

2. 在 Azure Stack 中重复上述步骤 (1)。

### <a name="add-route-for-azure-stack"></a>添加 Azure Stack 的路由

Azure Stack 上的应用服务必须可从公共 Internet 进行路由，使用户能够访问你的应用程序。 如果 Azure Stack 可从 Internet 访问，请记下 Azure Stack Web 应用的面向公众的 IP 地址或 URL。

如果使用 ASDK，则可以[配置静态 NAT 映射](../operator/azure-stack-create-vpn-connection-one-node.md#configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal)，以便在虚拟环境外部公开应用服务。

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>将 Azure 中的 Web 应用连接到混合网络

若要让 Azure 中的 Web 前端与 Azure Stack 中的 SQL Server 数据库相互连接，必须将 Web 应用连接到 Azure 与 Azure Stack 之间的混合网络。 若要启用连接，必须：

- 配置点到站点连接
- 配置 Web 应用
- 修改 Azure Stack 中的本地网络网关。

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>为点到站点连接配置 Azure 虚拟网络

在混合网络中，Azure 端的虚拟网络网关必须允许点到站点连接，以便与 Azure 应用服务集成。

1. 在 Azure 中，导航到虚拟网络网关页。 在“设置”下面，选择“点到站点配置”。  

    ![“点到站点”选项](media/azure-stack-solution-hybrid-cloud/image8.png)

2. 选择“立即配置”以配置点到站点连接。 

    ![开始进行点到站点配置](media/azure-stack-solution-hybrid-cloud/image9.png)

3. 在“点到站点”配置页上的“地址池”中，输入要使用的专用 IP 地址范围。  

   > [!Note]  
   > 请确保指定的范围不与任何已使用全局 Azure 或 Azure Stack 的混合网络组件中的子网的地址范围重叠。

   在“隧道类型”下面，取消选中“IKEv2 VPN”。   选择“保存”完成点到站点配置。 

   ![“点到站点”设置](media/azure-stack-solution-hybrid-cloud/image10.png)

### <a name="integrate-the-azure-app-service-application-with-the-hybrid-network"></a>将 Azure 应用服务应用程序与混合网络集成

1. 若要连接到 Azure VNet 的应用程序，按照中的说明[网关所需的 VNet 集成](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#gateway-required-vnet-integration)。

2. 导航到托管 Web 应用程序的应用服务计划的“设置”。  在“设置”中，选择“网络”。  

    ![配置网络](media/azure-stack-solution-hybrid-cloud/image11.png)

3. 在“VNET 集成”中，选择“单击此处进行管理”。  

    ![管理 VNET 集成](media/azure-stack-solution-hybrid-cloud/image12.png)

4. 选择要配置的 VNET。 在“路由到 VNET 的 IP 地址”下面，输入 Azure VNet、Azure Stack VNet 和点到站点地址空间的 IP 地址范围。  选择“保存”以验证并保存这些设置。 

    ![路由的 IP 地址范围](media/azure-stack-solution-hybrid-cloud/image13.png)

若要详细了解应用服务如何与 Azure VNet 集成，请参阅[将应用与 Azure 虚拟网络集成](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)。

### <a name="configure-the-azure-stack-virtual-network"></a>配置 Azure Stack 虚拟网络

需将 Azure Stack 虚拟网络中的本地网络网关配置为路由来自应用服务点到站点地址范围的流量。

1. 在 Azure Stack 中，导航到“本地网络网关”。  在“设置”下，选择“配置”   。

    ![网关配置选项](media/azure-stack-solution-hybrid-cloud/image14.png)

2. 在“地址空间”中，输入 Azure 虚拟网络网关的点到站点地址范围。选择“保存”以验证并保存此配置。  

    ![点到站点地址空间](media/azure-stack-solution-hybrid-cloud/image15.png)

## <a name="configure-dns-for-cross-cloud-scaling"></a>为跨云缩放配置 DNS

通过跨云应用程序的正确配置 DNS，用户可以访问你的 web 应用的全局 Azure 和 Azure Stack 实例。 本教程中的 DNS 配置还可让 Azure 流量管理器在负载增加或减少时路由流量。

本教程使用 Azure DNS 来管理 DNS。 （应用服务域不适用。）

### <a name="create-subdomains"></a>创建子域

由于流量管理器依赖于 DNS CNAME，因此需要使用子域来正确将流量路由到终结点 有关 DNS 记录和域映射的详细信息，请参阅[使用流量管理器映射域](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name)

对于 Azure 终结点，需要创建一个可让用户用来访问你的 Web 应用的子域。 在本教程中可以使用 **app.northwind.com**，但应根据自己的域自定义此值。

此外，需要为 Azure Stack 终结点创建包含 A 记录的子域。 可以使用 **azurestack.northwind.com**。

### <a name="configure-a-custom-domain-in-azure"></a>在 Azure 中配置自定义域

1. 通过[将 CNAME 映射到 Azure 应用服务](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)，将 **app.northwind.com** 主机名添加到 Azure Web 应用。

### <a name="configure-custom-domains-in-azure-stack"></a>在 Azure Stack 中配置自定义域

1. 通过[将 A 记录映射到 Azure 应用服务](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record)，将 **azurestack.northwind.com** 主机名添加到 Azure Stack Web 应用。 对应用服务应用程序请使用可通过 Internet 路由的 IP 地址。

2. 通过[将 CNAME 映射到 Azure 应用服务](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)，将 **app.northwind.com** 主机名添加到 Azure Stack Web 应用。 使用在前一步骤 (1) 中配置的主机名作为 CNAME 的目标。

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>为跨云缩放配置 SSL 证书

需确保 Web 应用程序收集的敏感数据在传输到 SQL 数据库以及在该数据库中处于静态时受到保护。

将 Azure 和 Azure Stack Web 应用程序配置为对所有传入流量使用 SSL 证书。

### <a name="add-ssl-to-azure-and-azure-stack"></a>将 SSL 添加到 Azure 和 Azure Stack

将 SSL 添加到 Azure：

1. 确保获取的 SSL 证书对于所创建的子域有效。 （也可以使用通配符证书。）

2. 在 Azure 中，遵照[将现有的自定义 SSL 证书绑定到 Azure Web 应用](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl)一文的“准备 Web 应用”和“绑定 SSL 证书”部分的说明操作。   为“SSL 类型”选择“基于 SNI 的 SSL”。  

3. 将所有流量重定向到 HTTPS 端口。 遵照[将现有的自定义 SSL 证书绑定到 Azure Web 应用](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl)一文的“强制实施 HTTPS”部分的说明操作。 

将 SSL 添加到 Azure Stack：

- 重复适用于 Azure 的步骤 1-3。

## <a name="configure-and-deploy-the-web-application"></a>配置和部署 web 应用程序

将配置应用程序代码来报告遥测数据到正确的 Application Insights 实例，并使用正确的连接字符串配置 web 应用程序。 若要了解有关 Application Insights 的详细信息，请参阅[什么是 Application Insights？](https://docs.microsoft.com/azure/application-insights/app-insights-overview)

### <a name="add-application-insights"></a>添加 Application Insights

1. 在 Microsoft Visual Studio 中打开 web 应用程序。

2. [添加 Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-client-side-telemetry-for-web-applications)到项目中以传输用于创建警报时 web 流量增加或减少 Application Insights 的遥测数据。

### <a name="configure-dynamic-connection-strings"></a>配置动态连接字符串

Web 应用程序的每个实例将使用另一种方法来连接到 SQL 数据库。 在 Azure 中的应用程序使用 SQL Server 虚拟机 (VM) 的专用 IP 地址并在 Azure Stack 中的应用程序使用 SQL Server VM 的公共 IP 地址。

> [!Note]  
> 在 Azure Stack 集成系统中，公共 IP 地址不应 Internet 路由。 在 Azure Stack 开发工具包 (ASDK)，公共 IP 地址不是外部 ASDK 可路由的。

应用服务环境变量可用于将不同的连接字符串传递给应用程序的每个实例。

1. 在 Visual Studio 中打开该应用程序。

2. 打开 Startup.cs 并查找以下代码块：

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. 将以下代码，使用 appsettings.json 文件中定义的连接字符串替换为上一个代码块：

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-application-settings"></a>配置应用服务应用程序设置

1. 创建适用于 Azure 和 Azure Stack 的连接字符串。 在这些字符串中，除使用的 IP 地址外，其余部分应该相同。

2. 在 Azure 和 Azure Stack 中，使用 `SQLCONNSTR\_` 作为名称中的前缀，添加相应的连接字符串作为 Web 应用程序中的[应用程序设置](https://docs.microsoft.com/azure/app-service/web-sites-configure)。

3. **保存** Web 应用设置并重启应用程序。

## <a name="enable-automatic-scaling-in-global-azure"></a>启用全球 Azure 中的自动缩放

在应用服务环境中创建 Web 应用时，它最初有一个实例。 可通过自动横向扩展来添加实例，以便为应用提供更多的计算资源。 同样，可以自动缩减并减少应用所需的实例数。

> [!Note]  
> 需要创建应用服务计划来配置横向扩展和缩减。 如果没有计划，请在开始执行后续步骤之前创建一个计划。

### <a name="enable-automatic-scale-out"></a>启用自动横向扩展

1. 在 Azure 中，找到要横向扩展的站点的应用服务计划，然后选择“横向扩展(应用服务计划)”。 

    ![横向扩展](media/azure-stack-solution-hybrid-cloud/image16.png)

2. 选择“启用自动缩放”。 

    ![启用自动缩放](media/azure-stack-solution-hybrid-cloud/image17.png)

3. 在“自动缩放设置名称”中输入名称。  对于“默认”自动缩放规则，请选择“基于指标缩放”。   将“实例限制”设置为“最小值:   1”、“最大值:  10”和“默认值:  1”。

    ![配置自动缩放](media/azure-stack-solution-hybrid-cloud/image18.png)

4. 选择“+添加规则”  。

5. 在“指标源”中，选择“当前资源”。   对规则使用以下条件和操作。

**条件**

1. 在“时间聚合”下面，选择“平均”。  

2. 在“指标名称”下面，选择“CPU 百分比”。  

3. 在“运算符”下面，选择“大于”。  

   - 将“阈值”设置为 **50**。 
   - 将“持续时间”设置为 **10**。 

**Action**

1. 在“操作”下面，选择“计数增量”。  

2. 将“实例计数”设置为 **2**。 

3. 将“冷却时间”设置为 **5**。 

4. 选择 **添加** 。

5. 选择“+添加规则”  。

6. 在“指标源”中，选择“当前资源”。  

   > [!Note]  
   > 当前资源将包含应用服务计划的名称/GUID，“资源类型”和“资源”下拉列表将会灰显。  

### <a name="enable-automatic-scale-in"></a>启用自动横向缩减

当流量减少时，Azure Web 应用程序可以自动减少活动实例的数目，以降低成本。 此操作的力度不如横向扩展，旨在尽量降低对应用程序用户造成的影响。

1. 导航到“默认”横向扩展条件，选择“+ 添加规则”。   对规则使用以下条件和操作。

**条件**

1. 在“时间聚合”下面，选择“平均”。  

2. 在“指标名称”下面，选择“CPU 百分比”。  

3. 在“运算符”下面，选择“小于”。  

   - 将“阈值”设置为 **30**。 
   - 将“持续时间”设置为 **10**。 

**Action**

1. 在“操作”下面，选择“计数减量”。  

   - 将“实例计数”设置为 **1**。 
   - 将“冷却时间”设置为 **5**。 

2. 选择 **添加** 。

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>创建流量管理器配置文件并配置跨云缩放

将在 Azure 中创建流量管理器配置文件，然后配置终结点以启用跨云缩放。

### <a name="create-traffic-manager-profile"></a>创建流量管理器配置文件

1. 选择“创建资源” 
2. 选择“网络” 
3. 选择“流量管理器配置文件”并进行以下配置： 

   - 在“名称”中，输入配置文件的名称。  此名称**必须**trafficmanager.net 区域中是唯一的用于创建新的 DNS 名称 (例如，northwindstore.trafficmanager.net)。
   - 对于“路由方法”，请选择“加权”。  
   - 对于“订阅”，请选择要在其中创建此配置文件的订阅。 
   - 在“资源组”中，为此配置文件创建新的资源组。 
   - 在**资源组位置**中，选择资源组的位置。 此设置指的是资源组的位置，对将全局部署的流量管理器配置文件没有影响。

4. 选择“创建”  。

    ![创建流量管理器配置文件](media/azure-stack-solution-hybrid-cloud/image19.png)

   流量管理器配置文件的全局部署完成后，会显示在它所属的资源组的资源列表中。

### <a name="add-traffic-manager-endpoints"></a>添加流量管理器终结点

1. 搜索创建的流量管理器配置文件。 （如果已导航到配置文件的资源组，请选择该配置文件。）

2. 在“流量管理器配置文件”中的“设置”下面，选择“终结点”    。

3. 选择 **添加** 。

4. 在“添加终结点”中，对 Azure Stack 使用以下设置： 

   - 对于“类型”，请选择“外部终结点”。  
   - 输入此终结点的**名称**。
   - 对于“完全限定的域名(FQDN)或 IP”，请输入 Azure Stack Web 应用的外部 URL。 
   - 对于“权重”，请保留默认值 **1**。  如果此终结点处于正常状态，此权重会使所有流量转到此终结点。
   - 将“添加为已禁用”保持未选中状态。 

5. 选择“确定”保存 Azure Stack 终结点。 

接下来将配置 Azure 终结点。

1. 在“流量管理器配置文件”中选择“终结点”。  
2. 选择“+添加”  。
3. 在“添加终结点”中，对 Azure 使用以下设置： 

   - 对于“类型”，请选择“Azure 终结点”。  
   - 输入此终结点的**名称**。
   - 对于“目标资源类型”，请选择“应用服务”。  
   - 对于“目标资源”，请选择“选择应用服务”以查看同一订阅中的 Web 应用列表。  
   - 在“资源”  中，选取要添加为第一个终结点的应用服务。
   - 对于“权重”，请选择 **2**。  如果主要终结点不正常，或者触发的某个规则/警报会重定向流量，则此权重会使所有流量转到此终结点。
   - 将“添加为已禁用”保持未选中状态。 

4. 选择“确定”保存 Azure 终结点。 

配置这两个终结点之后，选择“终结点”时，它们会列在“流量管理器配置文件”中。   以下屏幕截图中的示例显示了两个终结点及其状态和配置信息。

![终结点](media/azure-stack-solution-hybrid-cloud/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>设置 Application Insights 监视和警报

Azure Application Insights 可以监视你的应用程序以及发送警报根据你配置的条件。 下面是一些示例： 应用程序不可用、 遇到故障，或显示的性能问题。

将使用 Application Insights 指标创建警报。 这些警报触发器时，实例将从 Azure Stack 会自动切换到 Azure，以横向扩展，然后再返回到 Azure Web 应用程序堆栈中的刻度。

### <a name="create-an-alert-from-metrics"></a>从指标创建警报

对于本教程中，导航到资源组，然后选择要打开的 Application Insights 实例**Application Insights**。

![Application Insights](media/azure-stack-solution-hybrid-cloud/image21.png)

将使用此视图在警报中创建横向扩展警报和小数位数。

### <a name="create-the-scale-out-alert"></a>创建横向扩展警报

1. 下**配置**，选择**警报 （经典）** 。
2. 选择“添加指标警报(经典)”  。
3. 在中**添加规则**，配置以下各项：

   - 有关**名称**，输入**迸发到 Azure 云**。
   - 一个**说明**是可选的。
   - 下**源**，**警报**，选择**指标**。
   - 下**条件**，选择你的订阅，流量管理器配置文件和流量管理器配置文件的名称的资源组的资源。

4. 有关**指标**，选择**请求速率**。
5. 有关**条件**，选择**大于**。
6. 有关**阈值**，输入**2**。
7. 有关**期**，选择**过去 5 分钟内**。
8. 下**通过通知**:
   - 选中的复选框**电子邮件所有者、 参与者和读者**。
   - 输入的电子邮件地址**其他管理员 email(s)** 。

9. 在菜单栏上，选择**保存**。

### <a name="create-the-scale-in-alert"></a>在警报中创建规模

1. 下**配置**，选择**警报 （经典）** 。
2. 选择“添加指标警报(经典)”  。
3. 在中**添加规则**，配置以下各项：

   - 有关**名称**，输入**恢复到 Azure Stack 缩放**。
   - 一个**说明**是可选的。
   - 下**源**，**警报**，选择**指标**。
   - 下**条件**，选择你的订阅，流量管理器配置文件和流量管理器配置文件的名称的资源组的资源。

4. 有关**指标**，选择**请求速率**。
5. 有关**条件**，选择**小于**。
6. 有关**阈值**，输入**2**。
7. 有关**期**，选择**过去 5 分钟内**。
8. 下**通过通知**:
   - 选中的复选框**电子邮件所有者、 参与者和读者**。
   - 输入的电子邮件地址**其他管理员 email(s)** 。

9. 在菜单栏上，选择**保存**。

以下屏幕截图显示了向外扩展和缩小的警报。

   ![警报（经典）](media/azure-stack-solution-hybrid-cloud/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack"></a>在 Azure 与 Azure Stack 之间重定向流量

可以配置为在 Azure 与 Azure Stack 之间手动或自动切换 Web 应用流量。

### <a name="configure-manual-switching-between-azure-and-azure-stack"></a>配置 Azure 与 Azure Stack 之间的手动切换

当网站达到配置的阈值时，你会收到警报。 使用以下步骤将流量手动重定向到 Azure。

1. 在 Azure 门户中，选择你的流量管理器配置文件。

    ![流量管理器终结点](media/azure-stack-solution-hybrid-cloud/image20.png)

2. 选择“终结点”。 
3. 选择“Azure 终结点”。 
4. 在“状态”下面，依次选择“已启用”、“保存”。   

    ![启用 Azure 终结点](media/azure-stack-solution-hybrid-cloud/image23.png)

5. 在流量管理器配置文件的“终结点”中，选择“外部终结点”。  
6. 在“状态”下面，依次选择“已禁用”、“保存”。   

    ![禁用 Azure Stack 终结点](media/azure-stack-solution-hybrid-cloud/image24.png)

配置终结点之后，应用程序流量将转到 Azure 横向扩展 Web 应用，而不是 Azure Stack Web 应用。

 ![终结点已更改](media/azure-stack-solution-hybrid-cloud/image25.png)

若要将流量回送到 Azure Stack，请使用上述步骤执行以下操作：

- 启用 Azure Stack 终结点
- 禁用 Azure 终结点

### <a name="configure-automatic-switching-between-azure-and-azure-stack"></a>配置 Azure 和 Azure Stack 之间自动切换

此外可以使用 Application Insights 监视在运行应用程序[无服务器](https://azure.microsoft.com/overview/serverless-computing/)Azure Functions 提供的环境。

在此方案中，可以配置为使用 webhook 调用的函数应用的 Application Insights。 此应用会自动启用或禁用对警报的响应中的终结点。

使用以下步骤作为指南来配置自动流量切换。

1. 创建 Azure Function app。
2. 创建 HTTP 触发的函数。
3. 为资源管理器、 Web 应用和流量管理器导入 Azure Sdk。
4. 开发代码：

   - 向你的 Azure 订阅进行身份验证。
   - 使用切换流量定向到 Azure 或 Azure Stack 的流量管理器终结点的参数。

5. 保存你的代码并添加使用到适当的参数的函数应用的 URL **Webhook** Application Insights 警报规则设置部分。
6. Application Insights 警报触发时，将自动重定向流量。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
