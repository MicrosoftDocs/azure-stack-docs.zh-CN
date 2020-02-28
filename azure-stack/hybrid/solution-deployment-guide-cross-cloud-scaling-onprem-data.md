---
title: 部署使用本地数据的应用，并使用 Azure 和 Azure Stack 集线器缩放跨云
description: 了解如何部署使用本地数据的应用程序，以及如何使用 Azure 和 Azure Stack 集线器缩放跨云。
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: b376be7855300dab0177bbbe735d6a5bf34d6bb9
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701065"
---
# <a name="deploy-an-app-that-uses-on-premises-data-and-scales-cross-cloud-using-azure-and-azure-stack-hub"></a>部署使用本地数据的应用，并使用 Azure 和 Azure Stack 集线器缩放跨云

本解决方案指南演示如何部署跨 Azure 和 Azure Stack 集线器的混合应用程序，并使用单个本地数据源。

通过使用混合云解决方案，你可以将私有云的符合性优点与公有云的可伸缩性相结合。 此外，开发人员可以利用 Microsoft 开发人员生态系统，并将其技能应用到云和本地环境。

## <a name="overview-and-assumptions"></a>概述和假设

按照本教程设置工作流，使开发人员能够将相同的 web 应用部署到公有云和私有云。 此应用可以访问在私有云上托管的非 internet 可路由网络。 监视这些 web 应用，在流量高峰时，程序会修改 DNS 记录，以将流量重定向到公有云。 当流量下降到高峰期之前，流量将路由回私有云。

本教程涵盖以下任务：

> [!div class="checklist"]
> - 部署混合连接 SQL Server 数据库服务器。
> - 将全球 Azure 中的 web 应用连接到混合网络。
> - 配置 DNS 以实现跨云缩放。
> - 配置用于跨云缩放的 SSL 证书。
> - 配置和部署 web 应用。
> - 创建流量管理器配置文件并对其进行配置以实现跨云缩放。
> - 为增加的流量设置 Application Insights 监视和警报。
> - 配置全局 Azure 与 Azure Stack 中心之间的自动流量切换。

> [!Tip]  
> ![hybrid-pillars](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack 中心是 Azure 的扩展。 Azure Stack 中心为本地环境带来了云计算的灵活性和革新，使你能够在任何位置构建和部署混合应用，从而实现了唯一的混合云。  
> 
> [混合应用程序的设计注意事项](overview-app-design-considerations.md)查看软件质量的支柱（放置、可伸缩性、可用性、复原能力、可管理性和安全性），以便设计、部署和操作混合应用程序。 设计注意事项有助于优化混合应用设计，并最大程度减少生产环境中的挑战。

### <a name="assumptions"></a>假设

本教程假定你对全球 Azure 和 Azure Stack 中心有基本的了解。 如果在开始本教程之前要了解详细信息，请查看以下文章：

 - [Azure 简介](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack 集线器关键概念](../operator/azure-stack-overview.md)

本教程还假定你具有 Azure 订阅。 如果没有订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>必备条件

在开始此解决方案之前，请确保满足以下要求：

- Azure Stack 中心集成系统上的 Azure Stack 开发工具包（ASDK）或订阅。 若要部署 Azure Stack 开发工具包，请按照[使用安装程序部署 ASDK](../asdk/asdk-install.md)中的说明进行操作。
- Azure Stack 中心安装应安装以下各项：
  - Azure App Service。 使用 Azure Stack 中心操作员在你的环境中部署和配置 Azure App Service。 本教程要求应用服务至少具有一个（1）可用专用辅助角色。
  - Windows Server 2016 映像。
  - 带有 Microsoft SQL Server 映像的 Windows Server 2016。
  - 适当的计划和产品/服务。
  - Web 应用的域名。 如果没有域名，可以从域提供商处购买，如 GoDaddy、Bluehost 和 InMotion。
- 来自受信任的证书颁发机构（如 LetsEncrypt）的域的 SSL 证书。
- 与 SQL Server 数据库通信并支持 Application Insights 的 web 应用。 可以从 GitHub 下载[dotnetcore-sqldb-教程](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial)示例应用。
- Azure 虚拟网络与 Azure Stack 中心虚拟网络之间的混合网络。 有关详细说明，请参阅[配置与 Azure 和 Azure Stack 集线器的混合云连接](solution-deployment-guide-connectivity.md)。

- 一种混合持续集成/持续部署（CI/CD）管道，在 Azure Stack 中心提供专用生成代理。 有关详细说明，请参阅[配置具有 Azure 的混合云标识和 Azure Stack 中心应用](solution-deployment-guide-identity.md)。

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>部署混合连接 SQL Server 数据库服务器

1. 登录到 Azure Stack 集线器用户门户。

2. 在**仪表板**上，选择 " **Marketplace**"。

    ![Azure Stack 中心市场](media/solution-deployment-guide-hybrid/image1.png)

3. 在**Marketplace**中选择 "**计算**"，然后选择 "**更多**"。 在 "**详细信息**" 下，选择**免费 SQL Server 许可证： Windows Server 上的 SQL Server 2017 开发人员**映像。

    ![选择虚拟机映像](media/solution-deployment-guide-hybrid/image2.png)

4. **免费 SQL Server 许可证： Windows Server 上的 SQL Server 2017 开发人员**，请选择 "**创建**"。

5. 在 "基本信息" **> 配置基本设置**，为虚拟机（VM）提供一个**名称**，为 SQL Server sa 提供一个**用户名**，并为 sa 提供一个**密码**。  从 "**订阅**" 下拉列表中，选择要部署到的订阅。 对于 "**资源组**"，请使用 "**选择现有**"，并将 VM 放在与 Azure Stack 中心 web 应用相同的资源组中。

    ![配置 VM 的基本设置](media/solution-deployment-guide-hybrid/image3.png)

6. 在 "**大小**" 下，为 VM 选择大小。 对于本教程，我们建议 A2_Standard 或 DS2_V2_Standard。

7. 在 "**设置" > 配置可选功能**"下，配置以下设置：

   - **存储帐户**：如果需要，可以创建一个新帐户。
   - **虚拟网络**：

     > [!Important]  
     > 请确保将你的 SQL Server VM 部署在与 VPN 网关相同的虚拟网络上。

   - **公共 IP 地址**：使用默认设置。
   - **网络安全组**：（NSG）。 创建新的 NSG。
   - **扩展和监视**：保留默认设置。
   - **诊断存储帐户**：如果需要，可以创建一个新帐户。
   - 选择 **"确定"** 保存配置。

     ![配置可选功能](media/solution-deployment-guide-hybrid/image4.png)

8. 在**SQL Server 设置**"下，配置以下设置：
   - 对于 " **SQL 连接**"，请选择 "**公共（Internet）** "。
   - 对于 "**端口**"，保留默认值**1433**。
   - 对于**SQL 身份验证**，请选择 "**启用**"。

     > [!Note]  
     > 启用 SQL 身份验证后，它应自动填充你在**基础**上配置的 "SQLAdmin" 信息。

   - 对于其余设置，请保留默认值。 选择“确定”。

     ![配置 SQL Server 设置](media/solution-deployment-guide-hybrid/image5.png)

9. 在 "**摘要**" 中，查看虚拟机配置，然后选择 **"确定"** 以启动部署。

    ![配置摘要](media/solution-deployment-guide-hybrid/image6.png)

10. 创建新的 VM 需要一些时间。 可以在 "**虚拟机**" 中查看 VM 的状态。

    ![虚拟机](media/solution-deployment-guide-hybrid/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack-hub"></a>在 Azure 中创建 web 应用并 Azure Stack 中心

Azure App Service 简化了运行和管理 web 应用。 由于 Azure Stack 中心与 Azure 一致，因此应用服务可以在这两种环境中运行。 你将使用应用服务来托管你的应用程序。

### <a name="create-web-apps"></a>创建 Web 应用

1. 按照在[azure 中管理应用服务计划](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan)中的说明在 azure 中创建 web 应用。 请确保将 web 应用放在与混合网络相同的订阅和资源组中。

2. 在 Azure Stack 集线器中重复前面的步骤（1）。

### <a name="add-route-for-azure-stack-hub"></a>为 Azure Stack 中心添加路由

Azure Stack 集线器上的应用服务必须可通过公共 internet 路由，以便用户可以访问你的应用。 如果 Azure Stack 集线器可通过 internet 访问，请记下面向公众的 IP 地址或 Azure Stack 中心 web 应用的 URL。

如果使用的是 ASDK，则可以[配置静态 NAT 映射](../operator/azure-stack-create-vpn-connection-one-node.md#configure-the-nat-vm-on-each-asdk-for-gateway-traversal)，以在虚拟环境外部公开应用服务。

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>将 Azure 中的 web 应用连接到混合网络

若要在 Azure 中的 web 前端和 Azure Stack 中心的 SQL Server 数据库之间提供连接，必须将 web 应用连接到 Azure 与 Azure Stack 中心之间的混合网络。 若要启用连接，你必须：

- 配置点到站点连接。
- 配置 web 应用。
- 在 Azure Stack Hub 中修改本地网络网关。

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>配置用于点到站点连接的 Azure 虚拟网络

混合网络的 Azure 端的虚拟网络网关必须允许点到站点连接与 Azure App Service 集成。

1. 在 Azure 中，导航到 "虚拟网络网关" 页。 在 "**设置**" 下，选择 "**点到站点配置**"。

    ![点到站点选项](media/solution-deployment-guide-hybrid/image8.png)

2. 选择 "**立即配置**" 以配置点到站点。

    ![开始点到站点配置](media/solution-deployment-guide-hybrid/image9.png)

3. 在 "**点到站点**配置" 页上，输入要在 "**地址池**" 中使用的专用 IP 地址范围。

   > [!Note]  
   > 请确保指定的范围与混合网络的全局 Azure 或 Azure Stack 中心组件中的子网已使用的任何地址范围不重叠。

   在 "**隧道类型**" 下，取消选择**IKEv2 VPN**。 选择 "**保存**" 以完成点到站点配置。

   ![点到站点设置](media/solution-deployment-guide-hybrid/image10.png)

### <a name="integrate-the-azure-app-service-app-with-the-hybrid-network"></a>将 Azure App Service 应用与混合网络集成

1. 若要将应用连接到 Azure VNet，请按照[网关所需的 VNet 集成](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#gateway-required-vnet-integration)中的说明进行操作。

2. 导航到托管 web 应用的应用服务计划的 "**设置**"。 在“设置”中，选择“网络”。

    ![配置网络](media/solution-deployment-guide-hybrid/image11.png)

3. 在**VNET 集成**中，选择 **"单击此处进行管理"** 。

    ![管理 VNET 集成](media/solution-deployment-guide-hybrid/image12.png)

4. 选择要配置的 VNET。 在 "**路由到 VNET 的 IP 地址**" 下，输入 Azure VNET、Azure Stack 中心 VNET 和点到站点地址空间的 ip 地址范围。 选择 "**保存**" 以验证并保存这些设置。

    ![要路由的 IP 地址范围](media/solution-deployment-guide-hybrid/image13.png)

若要详细了解如何将应用服务与 Azure Vnet 集成，请参阅将[应用与 Azure 虚拟网络集成](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)。

### <a name="configure-the-azure-stack-hub-virtual-network"></a>配置 Azure Stack 中心虚拟网络

需要将 Azure Stack 中心虚拟网络中的本地网络网关配置为从应用服务点到站点地址范围路由流量。

1. 在 Azure Stack Hub 中，导航到 "**本地网络网关**"。 在“设置”下，选择“配置”。

    ![网关配置选项](media/solution-deployment-guide-hybrid/image14.png)

2. 在 "**地址空间**" 中，输入 Azure 中虚拟网络网关的点到站点地址范围。

    ![点到站点地址空间](media/solution-deployment-guide-hybrid/image15.png)

3. 选择 "**保存**" 以验证并保存配置。

## <a name="configure-dns-for-cross-cloud-scaling"></a>配置 DNS 以实现跨云缩放

通过对跨云应用正确配置 DNS，用户可以访问 web 应用的全局 Azure 和 Azure Stack 中心实例。 本教程的 DNS 配置还允许 Azure 流量管理器在负载增加或减少时路由流量。

本教程使用 Azure DNS 来管理 DNS，因为应用服务域不起作用。

### <a name="create-subdomains"></a>创建子域

由于流量管理器依赖于 DNS Cname，因此需要一个子域才能正确地将流量路由到终结点。 有关 DNS 记录和域映射的详细信息，请参阅[用流量管理器映射域](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name)。

对于 Azure 终结点，你将创建一个可供用户用来访问你的 web 应用的子域。 对于本教程，可以使用**app.northwind.com**，但你应根据自己的域自定义此值。

还需要创建一个子域，其中包含 Azure Stack 中心终结点的 A 记录。 可以使用**azurestack.northwind.com**。

### <a name="configure-a-custom-domain-in-azure"></a>在 Azure 中配置自定义域

1. 通过将[CNAME 映射到 Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)，将**app.northwind.com**主机名添加到 Azure web 应用。

### <a name="configure-custom-domains-in-azure-stack-hub"></a>在 Azure Stack 中心配置自定义域

1. 通过将[A 记录映射到 Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record)，将**azurestack.northwind.com**主机名添加到 Azure Stack 中心 web 应用。 使用应用服务应用的可通过 Internet 路由的 IP 地址。

2. 通过将[CNAME 映射到 Azure App Service](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record)，将**app.northwind.com**主机名添加到 Azure Stack 中心 web 应用。 使用在上一步中配置的主机名（1）作为 CNAME 的目标。

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>配置用于跨云缩放的 SSL 证书

确保 web 应用收集的敏感数据在传输到和存储在 SQL 数据库上时安全，这一点很重要。

你将配置 Azure 和 Azure Stack 中心 web 应用，以便对所有传入流量使用 SSL 证书。

### <a name="add-ssl-to-azure-and-azure-stack-hub"></a>向 Azure 和 Azure Stack 中心添加 SSL

向 Azure 添加 SSL：

1. 请确保你获取的 SSL 证书对你创建的子域有效。 （可以使用通配符证书。）

2. 在 Azure 中，按照**准备 web 应用**中的说明和将[现有自定义 SSL 证书绑定到 Azure WEB 应用](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl)的**ssl 证书**部分中的说明进行操作。 选择 "**基于 SNI 的 ssl** " 作为**ssl 类型**。

3. 将所有流量重定向到 HTTPS 端口。 按照将[现有自定义 SSL 证书绑定到 Azure Web 应用](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl)的**强制 HTTPS**部分中的说明进行操作。

向 Azure Stack 中心添加 SSL：

- 重复使用适用于 Azure 的步骤1-3。

## <a name="configure-and-deploy-the-web-app"></a>配置和部署 web 应用

你将配置应用程序代码，以便向正确的 Application Insights 实例报告遥测，并为 web 应用配置正确的连接字符串。 若要了解有关 Application Insights 的详细信息，请参阅[什么是 Application Insights？](https://docs.microsoft.com/azure/application-insights/app-insights-overview)

### <a name="add-application-insights"></a>添加 Application Insights

1. 在 Microsoft Visual Studio 中打开 web 应用。

2. [将 Application Insights 添加](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core#enable-client-side-telemetry-for-web-applications)到项目中，以传输在 web 流量增加或减少时 Application Insights 用于创建警报的遥测。

### <a name="configure-dynamic-connection-strings"></a>配置动态连接字符串

Web 应用的每个实例都将使用不同的方法连接到 SQL 数据库。 Azure 中的应用使用 SQL Server 虚拟机（VM）的专用 IP 地址，Azure Stack 中心的应用使用 SQL Server VM 的公共 IP 地址。

> [!Note]  
> 在 Azure Stack 集线器集成系统上，公共 IP 地址不应可通过 internet 路由。 在 Azure Stack 开发工具包（ASDK）上，公共 IP 地址不能在 ASDK 外部路由。

可以使用应用服务环境变量将不同的连接字符串传递给应用的每个实例。

1. 在 Visual Studio 中打开应用。

2. 打开 Startup.cs 并找到以下代码块：

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. 将前面的代码块替换为以下代码，该代码使用在*appsettings*文件中定义的连接字符串：

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-app-settings"></a>配置应用服务应用设置

1. 为 Azure 和 Azure Stack 集线器创建连接字符串。 字符串应该是相同的，但使用的 IP 地址除外。

2. 在 Azure 和 Azure Stack 中心中，在 web 应用中添加适当的连接字符串[作为应用设置](https://docs.microsoft.com/azure/app-service/web-sites-configure)，并使用 `SQLCONNSTR\_` 作为名称中的前缀。

3. **保存**web 应用设置并重新启动应用。

## <a name="enable-automatic-scaling-in-global-azure"></a>在全球 Azure 中启用自动缩放

在应用服务环境中创建 web 应用时，它将从一个实例开始。 你可以自动横向扩展以添加实例，为你的应用提供更多计算资源。 同样，您可以自动缩放并减少应用程序所需的实例数。

> [!Note]  
> 你需要使用应用服务计划来配置 scale out 和 scale in。 如果没有计划，请先创建一个计划，然后再开始后续步骤。

### <a name="enable-automatic-scale-out"></a>启用自动横向扩展

1. 在 Azure 中，查找要横向扩展的站点的应用服务计划，然后选择 "**扩展（应用服务计划）** "。

    ![横向扩展](media/solution-deployment-guide-hybrid/image16.png)

2. 选择 "**启用自动缩放**"。

    ![启用自动缩放](media/solution-deployment-guide-hybrid/image17.png)

3. 输入**自动缩放设置名称**的名称。 对于**默认**的 "自动缩放" 规则，选择 **"基于指标缩放**"。 将**实例限制**设置为**最小值： 1**，**最大值： 10**，**默认值为 1**。

    ![配置自动缩放](media/solution-deployment-guide-hybrid/image18.png)

4. 选择 " **+ 添加规则**"。

5. 在 "**指标源**" 中，选择 "**当前资源**"。 对规则使用以下条件和操作。

**条件**

1. 在 "**时间聚合" 下，** 选择 "**平均值**"。

2. 在 "**指标名称**" 下，选择 " **CPU 百分比**"。

3. 在 "**运算符**" 下，选择 "**大于**"。

   - 将**阈值**设置为**50**。
   - 将**持续时间**设置为**10**。

**Action**

1. 在 "**操作**" 下，选择 "**增加计数**"。

2. 将**实例计数**设置为**2**。

3. 将**冷却**设置为**5**。

4. 选择 **添加** 。

5. 选择 " **+ 添加规则**"。

6. 在 "**指标源**" 中，选择 "**当前资源"。**

   > [!Note]  
   > 当前资源将包含应用服务计划的名称/GUID，并且 "**资源类型**" 和 "**资源**" 下拉列表将不可用。

### <a name="enable-automatic-scale-in"></a>启用自动缩放

当流量下降时，Azure web 应用可以自动减少活动实例数以降低成本。 此操作不如缩小，并最大程度地降低对应用用户的影响。

1. 导航到**默认**的 scale out 条件，选择 " **+ 添加规则**"。 对规则使用以下条件和操作。

**条件**

1. 在 "**时间聚合" 下，** 选择 "**平均值**"。

2. 在 "**指标名称**" 下，选择 " **CPU 百分比**"。

3. 在 "**运算符**" 下，选择 "**小于**"。

   - 将**阈值**设置为**30**。
   - 将**持续时间**设置为**10**。

**Action**

1. 在 "**操作**" 下，选择 "**减少计数**"。

   - 将**实例计数**设置为**1**。
   - 将**冷却**设置为**5**。

2. 选择 **添加** 。

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>创建流量管理器配置文件并配置跨云缩放

在 Azure 中创建流量管理器配置文件，并配置终结点以启用跨云缩放。

### <a name="create-traffic-manager-profile"></a>创建流量管理器配置文件

1. 选择“创建资源”。
2. 选择“网络”。
3. 选择 "**流量管理器配置文件**" 并配置以下设置：

   - 在 "**名称**" 中，输入配置文件的名称。 此名称**必须**在 trafficmanager.net 区域中唯一，并用于创建新的 DNS 名称（例如，northwindstore.trafficmanager.net）。
   - 对于**路由方法**，请选择 "**加权**"。
   - 对于 "**订阅**"，请选择要在其中创建此配置文件的订阅。
   - 在 "**资源组**" 中，为此配置文件创建新的资源组。
   - 在**资源组位置**中，选择资源组的位置。 此设置指的是资源组的位置，对全局部署的流量管理器配置文件没有影响。

4. 选择“创建”。

    ![创建流量管理器配置文件](media/solution-deployment-guide-hybrid/image19.png)

   流量管理器配置文件的全局部署完成后，它会显示在所创建的资源组的资源列表中。

### <a name="add-traffic-manager-endpoints"></a>添加流量管理器终结点

1. 搜索你创建的流量管理器配置文件。 如果你导航到该配置文件的资源组，请选择该配置文件。

2. 在**流量管理器配置文件**中的 "**设置**" 下，选择 "**终结点**"。

3. 选择 **添加** 。

4. 在 "**添加终结点**" 中，将以下设置用于 Azure Stack 中心：

   - 对于 "**类型**"，请选择 "**外部终结点**"。
   - 输入终结点的**名称**。
   - 对于**完全限定的域名（FQDN）或 IP**，请输入 Azure Stack 中心 web 应用的外部 URL。
   - 对于 "**权重**"，保留默认值**1**。 如果此终结点处于正常状态，则此权重会导致所有流量转到此终结点。
   - 将 "**添加为禁止**选中"。

5. 选择 **"确定"** 以保存 Azure Stack 中心终结点。

接下来，你将配置 Azure 终结点。

1. 在**流量管理器配置文件**中，选择 "**终结点**"。
2. 选择“+添加”。
3. 在 "**添加终结点**" 上，对 Azure 使用以下设置：

   - 对于 "**类型**"，请选择 " **Azure 终结点**"。
   - 输入终结点的**名称**。
   - 对于 "**目标资源类型**"，请选择 "**应用服务**"。
   - 对于 "**目标资源**"，选择 "**选择应用服务**" 以查看同一订阅中的 Web 应用列表。
   - 在“资源”中，选取要添加为第一个终结点的应用服务。
   - 对于 "**权重**"，选择**2**。 如果主终结点不正常，或者如果你有在触发时重定向流量的规则/警报，此设置会导致所有流量转到此终结点。
   - 将 "**添加为禁止**选中"。

4. 选择 **"确定"** 保存 Azure 终结点。

在配置了两个终结点后，当你选择**终结**点时，它们将在**流量管理器配置文件**中列出。 以下屏幕截图中的示例显示了两个终结点，其中每个终结点具有状态和配置信息。

![终结点](media/solution-deployment-guide-hybrid/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>设置 Application Insights 监视和警报

Azure 应用程序 Insights 使你可以监视应用，并根据配置的条件发送警报。 一些示例包括：应用不可用、遇到故障或显示了性能问题。

你将使用 Application Insights 度量值创建警报。 当这些警报触发时，web 应用的实例会自动从 Azure Stack 中心切换到 Azure 以进行横向扩展，然后返回到 Azure Stack 集线器进行扩展。

### <a name="create-an-alert-from-metrics"></a>从指标创建警报

导航到本教程的资源组，然后选择 Application Insights 实例以打开**Application Insights**。

![Application Insights](media/solution-deployment-guide-hybrid/image21.png)

你将使用此视图创建向外扩展警报和在警报中进行缩放。

### <a name="create-the-scale-out-alert"></a>创建扩展警报

1. 在 "**配置**" 下，选择 "**警报（经典）** "。
2. 选择“添加指标警报(经典)”。
3. 在 "**添加规则**" 中，配置以下设置：

   - 对于 "**名称**"，请输入**突发入 Azure 云**。
   - **描述**是可选的。
   - 在 "**源** > **上的警报**" 下，选择 "**指标**"。
   - 在 "**条件**" 下，选择你的订阅、流量管理器配置文件的资源组和资源的流量管理器配置文件的名称。

4. 对于 "**指标**"，请选择 "**请求速率**"。
5. 对于 "**条件**"，选择 "**大于**"。
6. 对于 "**阈值**"，输入**2**。
7. 对于 "**期间**"，选择 **"过去5分钟"** 。
8. 在 "**通知**方式" 下面：
   - 选中**电子邮件所有者、参与者和读者**的复选框。
   - 输入**其他管理员电子邮件**的电子邮件地址。

9. 在菜单栏上，选择 "**保存**"。

### <a name="create-the-scale-in-alert"></a>创建放大警报

1. 在 "**配置**" 下，选择 "**警报（经典）** "。
2. 选择“添加指标警报(经典)”。
3. 在 "**添加规则**" 中，配置以下设置：

   - 对于 "**名称**"，请输入**Scale 回 Azure Stack Hub**。
   - **描述**是可选的。
   - 在 "**源** > **上的警报**" 下，选择 "**指标**"。
   - 在 "**条件**" 下，选择你的订阅、流量管理器配置文件的资源组和资源的流量管理器配置文件的名称。

4. 对于 "**指标**"，请选择 "**请求速率**"。
5. 对于 "**条件**"，选择 "**小于**"。
6. 对于 "**阈值**"，输入**2**。
7. 对于 "**期间**"，选择 **"过去5分钟"** 。
8. 在 "**通知**方式" 下面：
   - 选中**电子邮件所有者、参与者和读者**的复选框。
   - 输入**其他管理员电子邮件**的电子邮件地址。

9. 在菜单栏上，选择 "**保存**"。

以下屏幕截图显示了向外扩展和向外缩放的警报。

   ![警报（经典）](media/solution-deployment-guide-hybrid/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack-hub"></a>重定向 Azure 与 Azure Stack 中心之间的流量

可以在 Azure 与 Azure Stack 中心之间配置手动或自动切换 web 应用流量切换。

### <a name="configure-manual-switching-between-azure-and-azure-stack-hub"></a>配置 Azure 与 Azure Stack 中心之间的手动切换

当你的网站达到你配置的阈值时，你将收到警报。 使用以下步骤手动将流量重定向到 Azure。

1. 在 Azure 门户中，选择流量管理器配置文件。

    ![流量管理器终结点](media/solution-deployment-guide-hybrid/image20.png)

2. 选择“终结点”。
3. 选择**Azure 终结点**。
4. 在 "**状态**" 下，选择 "**已启用**"，然后选择 "**保存**"。

    ![启用 Azure 终结点](media/solution-deployment-guide-hybrid/image23.png)

5. 在流量管理器配置文件的**终结点**上选择 "**外部终结点**"。
6. 在 "**状态**" 下，选择 "**禁用**"，然后选择 "**保存**"。

    ![禁用 Azure Stack 中心终结点](media/solution-deployment-guide-hybrid/image24.png)

配置终结点后，应用流量将转到 Azure 横向扩展 web 应用，而不是 Azure Stack 中心 web 应用。

 ![终结点已更改](media/solution-deployment-guide-hybrid/image25.png)

若要将流反向退回 Azure Stack 中心，请使用前面的步骤执行以下操作：

- 启用 Azure Stack 中心终结点。
- 禁用 Azure 终结点。

### <a name="configure-automatic-switching-between-azure-and-azure-stack-hub"></a>配置 Azure 与 Azure Stack 中心之间的自动切换

如果你的应用程序在 Azure Functions 提供的[无服务器](https://azure.microsoft.com/overview/serverless-computing/)环境中运行，你还可以使用 Application Insights 监视。

在此方案中，可以将 Application Insights 配置为使用调用函数应用的 webhook。 此应用将自动启用或禁用终结点以响应警报。

使用以下步骤来配置自动流量切换。

1. 创建 Azure Function app。
2. 创建 HTTP 触发的函数。
3. 导入适用于资源管理器、Web 应用和流量管理器的 Azure Sdk。
4. 开发代码：

   - 向 Azure 订阅进行身份验证。
   - 使用参数来切换流量管理器终结点，以将流量定向到 Azure 或 Azure Stack 中心。

5. 保存代码，并将相应的参数添加到 Application Insights 预警规则设置的**Webhook**部分的函数应用 URL。
6. 当 Application Insights 警报触发时，会自动重定向流量。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
