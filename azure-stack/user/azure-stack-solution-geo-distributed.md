---
title: 使用 Azure 和 Azure Stack 创建异地分布式应用解决方案 | Microsoft Docs
description: 了解如何使用 Azure 和 Azure Stack 创建异地分布式应用解决方案。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 81dfc9c7571bc191582425962d1e7b37c2ed05cd
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64304923"
---
# <a name="tutorial-create-a-geo-distributed-app-solution-with-azure-and-azure-stack"></a>教程：使用 Azure 和 Azure Stack 创建异地分布式应用解决方案

适用于：Azure Stack 集成系统和 Azure Stack 开发工具包

了解如何根据各种指标，使用异地分布式应用模式将流量定向到特定的终结点。 使用基于地理位置的路由和终结点配置创建流量管理器配置文件可确保根据区域要求、企业与国际法规和数据需求将信息路由到终结点。

在本教程中，我们将构建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 创建异地分布式应用程序。
> - 使用流量管理器设置应用目标。

## <a name="use-the-geo-distributed-apps-pattern"></a>使用异地分布式应用模式

应用可以使用异地分布式模式来实现跨区域。 可以默认使用公有云，但某些用户可能需要将数据保留在其所在区域中。 可根据用户的要求将他们定向到最合适的云。

### <a name="issues-and-considerations"></a>问题和注意事项

#### <a name="scalability-considerations"></a>可伸缩性注意事项

在本教程中构建的解决方案无法处理可伸缩性。 但是，如果与其他 Azure 和本地技术以及解决方案结合使用，则可以满足可伸缩性方面的要求。 有关通过流量管理器创建可自动缩放的混合解决方案的信息，请参阅[在 Azure 中创建跨云缩放解决方案](azure-stack-solution-cloud-burst.md)。

#### <a name="availability-considerations"></a>可用性注意事项

与可伸缩性注意事项中提到的一样，此解决方案无法直接处理可用性。 但是，同样与可伸缩性注意事项中提到的一样，可在此解决方案中实施 Azure 和本地技术以及解决方案，以确保所有相关组件具有高可用性。

### <a name="when-to-use-this-pattern"></a>何时使用此模式

- 组织有海外分公司，需要自定义的区域安全性和分发策略。

- 组织的每个办事处提取员工、业务和设施数据，需要根据当地法规和时区报告活动。

- 只要对单个区域内和跨区域的多个应用部署进行应用的水平扩展，即可达到高缩放性要求以处理极高的负载要求。

### <a name="planning-the-topology"></a>规划拓扑

在规划分布式应用的范围之前，最好先学习以下知识：

-   **应用的自定义域：** 客户访问应用时使用的自定义域名是什么？ 示例应用的自定义域名是 *www.scalableasedemo.com*。

-   **流量管理器域：** 创建 [Azure 流量管理器配置文件](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles)时需要选择域名。 此名称与 *trafficmanager.net* 后缀相结合，以注册流量管理器所管理的域条目。 就示例应用而言，选择的名称是 *scalable-ase-demo*。 因此，管理流量管理器的完整域名是*可扩展 ase demo.trafficmanager.net*。

-   **缩放应用占用空间的策略：** 应用程序占用空间是否分布到单个区域中的多个应用服务环境？ 是多个区域吗？ 是否混用两种方法？ 决策依据应来自于客户流量的来源位置，以及其余应用的支持后端基础结构的伸缩性。 例如，对于 100% 无状态的应用程序，可以使用每一 Azure 区域多个应用服务环境的组合，乘以跨多个 Azure 区域部署的应用服务环境数，来大幅缩放应用。 由于有 15 个以上的全球 Azure 区域可供选择，客户可真正构建全球性超高缩放性的应用程序范围。 在本文所使用的示例应用中，有三个应用服务环境创建在单个 Azure 区域（美国中南部）。

-   **应用服务环境的命名约定：** 每个应用服务环境需要唯一的名称。 有两个或更多应用服务环境时，命名约定将有助于标识每个应用服务环境。 示例应用中使用了简单的命名约定。 三个应用服务环境的名称分别是 *fe1ase*、*fe2ase* 和 *fe3ase*。

-   **应用的命名约定：** 由于将部署多个应用实例，每个部署的应用实例都要有名称。 多个应用服务环境可以使用相同的应用名称。 由于每个应用服务环境都有唯一的域后缀，开发人员可以选择在每个环境中重复使用完全相同的应用名称。 例如，开发人员可以将应用命名，如下所示： *myapp.foo1.p.azurewebsites.net*， *myapp.foo2.p.azurewebsites.net*， *myapp.foo3.p.azurewebsites.net*，等等。对于此方案中的应用，每个应用实例具有唯一的名称。 所用的应用实例名称是 *webfrontend1*、*webfrontend2* 和 *webfrontend3*。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack 是 Azure 的扩展。 Azure Stack 将云计算的灵活性和创新性带入你的本地环境，并支持唯一的混合云，以允许你在任何地方构建和部署混合应用。  
> 
> 白皮书 [Design Considerations for Hybrid Applications](https://aka.ms/hybrid-cloud-applications-pillars)（混合应用程序的设计注意事项）回顾了设计、部署和运行混合应用程序所需的软件质量要素（位置、可伸缩性、可用性、复原能力、可管理性和安全性）。 这些设计注意事项有助于优化混合应用程序设计，从而最大限度地减少生产环境中的难题。

## <a name="part-1-create-a-geo-distributed-app"></a>第 1 部分：创建异地分布式应用

在此部分创建 Web 应用。

> [!div class="checklist"]
> - 创建 Web 应用并发布
> - 向 Azure Repos 中添加代码
> - 将应用生成指向多个云目标。
> - 管理和配置 CD 过程

### <a name="prerequisites"></a>必备组件

需要 Azure 订阅并安装 Azure Stack。

### <a name="geo-distributed-app-steps"></a>异地分布式应用步骤

### <a name="obtain-a-custom-domain-and-configure-dns"></a>获取自定义域并配置 DNS

更新域的 DNS 区域文件。 然后，Azure AD 可以验证自定义域名的所有权。 将 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) 用于 Azure 中的 Azure/Office 365/外部 DNS 记录，或在[其他 DNS 注册机构](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)中添加 DNS 条目。

1. 向公共注册机构注册自定义域。

2. 登录到域的域名注册机构。 可能需要由获批准的管理员进行 DNS 更新。

3. 通过添加 Azure AD 提供的 DNS 条目来更新域的 DNS 区域文件。 DNS 条目不会更改行为，例如邮件路由或 Web 托管。

### <a name="create-web-apps-and-publish"></a>创建 Web 应用并发布

设置混合 CI/CD，以将 Web 应用部署到 Azure 和 Azure Stack，并自动将更改推送到这两个云中。

> [!Note]  
> 需要在 Azure Stack 上创建适当的合成映像用于运行 Windows Server 和 SQL，并需要部署应用服务。 查看应用服务文档中面向 Azure Stack 操作员的[开始使用 Azure Stack 上的应用服务之前](../operator/azure-stack-app-service-before-you-get-started.md)部分。

#### <a name="add-code-to-azure-repos"></a>向 Azure Repos 中添加代码

1. 使用在 Azure Repos 上**拥有项目创建权限的帐户**登录到 Visual Studio。

    混合持续集成/持续交付 (CI/CD) 可同时应用到应用程序代码和基础结构代码。 使用 [Azure 资源管理器模板](https://azure.microsoft.com/resources/templates/)进行专用与托管的云开发。

    ![Alt text](media/azure-stack-solution-geo-distributed/image1.JPG)

2. 创建并打开默认 Web 应用以**克隆存储库**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>在这两个云中创建 Web 应用部署

1.  编辑 **WebApplication.csproj** 文件：选择“Runtimeidentifier”并添加 **win10-x64**。 （请参阅[独立部署](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)文档。）

    ![Alt text](media/azure-stack-solution-geo-distributed/image3.png)

1.  使用团队资源管理器**将代码签入到 Azure Repos**。

2.  确认**应用程序代码**已签入到 Azure Repos。

### <a name="create-the-build-definition"></a>创建生成定义

1. **登录到 Azure Pipelines** 以确认能够创建生成定义。

2. 添加 **-r win10-x64** 代码。 使用 .NET Core 触发独立部署时需要此代码。

    ![Alt text](media/azure-stack-solution-geo-distributed/image4.png)

3. **运行生成**。 [独立部署生成](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd)过程将发布可在 Azure 和 Azure Stack 上运行的项目。

**使用 Azure 托管代理**

在 Azure Pipelines 中使用托管代理是生成和部署 Web 应用的便捷做法。 Microsoft Azure 会自动执行维护和升级，可实现持续不间断的开发、测试和部署。

### <a name="manage-and-configure-the-cd-process"></a>管理和配置 CD 过程

Azure DevOps 和 Azure DevOps Server 提供高度可配置、可管理的管道，用于将内容发布到多个环境（例如开发、过渡、QA 和生产环境）；在特定的阶段要求审批。

#### <a name="create-release-definition"></a>创建发布定义


![Alt text](media/azure-stack-solution-geo-distributed/image5.png)

1. 在 Visual Studio Online (VSO) 的“生成和发布”页的“发布”选项卡下，选择**加号**按钮以添加新的发布。

   ![Alt text](media/azure-stack-solution-geo-distributed/image6.png)

2. 应用“Azure 应用服务部署”模板。

    ![Alt text](media/azure-stack-solution-geo-distributed/image7.png)

3. 在“添加项目”下拉菜单中，为 Azure 云生成应用**添加项目**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image8.png)

4. 在“管道”选项卡下选择环境的“阶段和任务”链接，并设置 Azure 云环境值。

    ![Alt text](media/azure-stack-solution-geo-distributed/image9.png)

5. 设置**环境名称**，并选择 Azure 云终结点的 Azure **订阅**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image10.png)

6. 在“环境名称”下，设置所需的 **Azure 应用服务名称**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image11.png)

7. 在 Azure 云托管环境的“代理队列”下输入 **Hosted VS2017**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image12.png)

8. 在“部署 Azure 应用服务”菜单中，为环境选择有效的**包或文件夹**。 选择**文件夹位置**旁边的“确定”。

    ![Alt text](media/azure-stack-solution-geo-distributed/image13.png)

    ![Alt text](media/azure-stack-solution-geo-distributed/image14.png)

9. 保存所有更改并返回**发布管道**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image15.png)

10. 选择 Azure Stack 应用的生成以添加**新项目**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image16.png)

11. 额外添加一个应用 **Azure 应用服务部署**的环境。

    ![Alt text](media/azure-stack-solution-geo-distributed/image17.png)

12. 将新环境命名为 **Azure Stack**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image18.png)

13. 在“任务”选项卡下找到 Azure Stack 环境。

    ![Alt text](media/azure-stack-solution-geo-distributed/image19.png)

14. 选择 Azure Stack 终结点的**订阅**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image20.png)

15. 将 Azure Stack Web 应用名称设置为**应用服务名称**。

    ![Alt text](media/azure-stack-solution-geo-distributed/image21.png)

16. 选择“Azure Stack 代理”。

    ![Alt text](media/azure-stack-solution-geo-distributed/image22.png)

17. 在“部署 Azure 应用服务”部分下，为环境选择有效的**包或文件夹**。 选择**文件夹位置**旁边的“确定”。

    ![Alt text](media/azure-stack-solution-geo-distributed/image23.png)

    ![Alt text](media/azure-stack-solution-geo-distributed/image24.png)

18. 在“变量”选项卡下添加名为 `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS` 的变量，将其值设置为 `true`，将范围设置为 `Azure Stack`。

    ![Alt text](media/azure-stack-solution-geo-distributed/image25.png)

19. 选择两个项目中的“持续”部署触发器图标，并启用“持续”部署触发器。

    ![Alt text](media/azure-stack-solution-geo-distributed/image26.png)

20. 选择 Azure Stack 环境中的“部署前”条件图标，并将触发器设置为“发布后”。

21. 保存所有更改。

> [!Note]  
>  任务的某些设置可能已在从模板创建发布定义时自动定义为[环境变量](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables)。 无法在任务设置中修改这些设置；必须选择父环境项才能编辑这些设置。

## <a name="part-2-update-web-app-options"></a>第 2 部分：更新 Web 应用选项

[Azure 应用服务](https://docs.microsoft.com/azure/app-service/overview)提供高度可缩放、自修补的 Web 托管服务。 

![Alt text](media/azure-stack-solution-geo-distributed/image27.png)

> [!div class="checklist"]
> - 将现有的自定义 DNS 名称映射到 Azure Web 应用
> - 使用 **CNAME 记录或 **A 记录**将自定义 DNS 名称映射到应用服务。

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>将现有的自定义 DNS 名称映射到 Azure Web 应用

> [!Note]  
>  对除根域（例如 northwind.com）以外的所有自定义 DNS 名称使用 CNAME。

若要将实时站点及其 DNS 域名迁移到应用服务，请参阅[将活动 DNS 名称迁移到 Azure 应用服务](https://docs.microsoft.com/azure/app-service/manage-custom-dns-migrate-domain)。

### <a name="prerequisites"></a>必备组件

完成本教程：

-   [创建一个应用服务应用](https://docs.microsoft.com/azure/app-service/)，或者使用为其他教程创建的应用。

-   购买一个域名并确保你对你的域提供商的 DNS 注册表拥有访问权限。

更新域的 DNS 区域文件。 然后，Azure AD 将会验证自定义域名的所有权。 将 [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) 用于 Azure 中的 Azure/Office 365/外部 DNS 记录，或在[其他 DNS 注册机构](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)中添加 DNS 条目。

-   向公共注册机构注册自定义域。

-   登录到域的域名注册机构。 （可能需要由获批准的管理员进行 DNS 更新。）

-   通过添加 Azure AD 提供的 DNS 条目来更新域的 DNS 区域文件。

例如，若要添加 northwindcloud.com 和 www.northwindcloud.com 的 DNS 条目，请配置 northwindcloud.com 根域的 DNS 设置。

> [!Note]  
>  可使用购买的域名[Azure 门户](https://docs.microsoft.com/azure/app-service/manage-custom-dns-buy-domain)。  
> 若要映射自定义 DNS 名称到 Web 应用，Web 应用的[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/)必须位于付费层（“共享”、“基本”、“标准”或“高级”）。



### <a name="create-and-map-cname-and-a-records"></a>创建并映射 CNAME 和 A 记录

#### <a name="access-dns-records-with-domain-provider"></a>通过域提供商访问 DNS 记录

> [!Note]  
>  使用 Azure DNS 为 Azure Web 应用配置自定义 DNS 名称。 有关详细信息，请参阅[使用 Azure DNS 为 Azure 服务提供自定义域设置](https://docs.microsoft.com/azure/dns/dns-custom-domain)。

1.  登录到域提供商的网站。

2.  查找管理 DNS 记录的页面。 每个域提供商都有其自己的 DNS 记录界面。 查找站点中标记为“域名”、“DNS”或“名称服务器管理”的区域。

可以在“我的域”中查看 DNS 记录页。 找到命名为“区域文件”、“DNS 记录”或“高级配置”的链接。

以下屏幕截图是 DNS 记录页的一个示例：

![示例 DNS 记录页](media/azure-stack-solution-geo-distributed/image28.png)

1. 在“域名注册机构”中，选择“添加或创建”以创建记录。 某些提供商提供了不同的链接来添加不同的记录类型。 查阅提供商的文档。

2. 添加一条 CNAME 记录来将子域映射到应用的默认主机名。

   Www.northwindcloud.com 域示例中，将添加 CNAME 记录映射到名称 < 应用\_名称 >。 azurewebsites.net。

添加 CNAME 后，DNS 记录页类似于以下示例：

![在门户中导航到 Azure 应用](media/azure-stack-solution-geo-distributed/image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>在 Azure 中启用 CNAME 记录映射

1. 在新选项卡中登录到 Azure 门户。

2. 导航到“应用服务”。

3. 选择 Web 应用。

4. 在 Azure 门户中的应用页左侧导航窗格中，选择“自定义域”。

5. 选择“添加主机名”旁边的 **+** 图标。

1. 键入完全限定的域名，例如 `www.northwindcloud.com`。

2. 选择“验证”。

3. 如有出现提示，请将其他类型的记录（`A` 或 `TXT`）添加到域名注册机构 DNS 记录。 Azure 将提供这些记录的值和类型：

   a.  要映射到应用 IP 地址的 A 记录。

   b.  一个**TXT**记录映射到应用的默认主机名 < a p p _ >。 azurewebsites.net。 应用服务仅在配置时使用此记录来验证自定义域所有权。 验证后，删除 TXT 记录。

4. 在域注册机构选项卡中完成此任务并重新验证，直到“添加主机名”按钮激活为止。

5. 确保“**主机名记录类型”设置为“CNAME (www.example.com 或任何子域)”。

6. 选择“添加主机名”。

7. 键入完全限定的域名，例如 `northwindcloud.com`。

8. 选择“验证”。

9. “添加”按钮将会激活。

10. 确保“**主机名记录类型”设置为“A 记录(example.com)”。

11. **添加主机名**。

    新主机名可能需要经过一段时间后才会反映在应用的“自定义域”页中。 请尝试刷新浏览器来更新数据。
  
    ![Alt text](media/azure-stack-solution-geo-distributed/image31.png) 
  
    如果发生错误，页面底部会显示验证错误通知。 ![验证错误](media/azure-stack-solution-geo-distributed/image32.png)

> [!Note]  
>  可以重复上述步骤以映射通配符域（\*.northwindcloud.com）。 如此可以直接将其他子域添加到此应用服务，而无需为每个子域单独创建 CNAME 记录。 请遵照注册机构的说明配置此设置。

#### <a name="test-in-a-browser"></a>在浏览器中测试

浏览到前面配置的 DNS 名称（例如，`northwindcloud.com` 和 www.northwindcloud.com）。

## <a name="part-3-bind-a-custom-ssl-cert"></a>第 3 部分：绑定自定义 SSL 证书

本部分介绍：

> [!div class="checklist"]
> - 将自定义 SSL 证书绑定到应用服务
> - 为应用实施 HTTPS
> - 使用脚本自动执行 SSL 证书绑定

> [!Note]  
> 如果需要获取客户在 Azure 门户中的 SSL 证书并将其绑定到 web 应用。 请遵循[应用服务证书教程](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site)。

### <a name="prerequisites"></a>必备组件

若要完成本教程，需执行以下操作：

-   [创建应用服务应用](https://docs.microsoft.com/azure/app-service/)
-   [将自定义 DNS 名称映射到 Web 应用](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   从受信任的证书颁发机构获取 SSL 证书，并使用密钥将请求签名

### <a name="requirements-for-your-ssl-certificate"></a>SSL 证书的要求

若要在应用服务中使用证书，该证书必须满足以下所有要求：

-   已由受信任的证书颁发机构签名

-   已导出为受密码保护的 PFX 文件

-   包含长度至少为 2048 位的私钥

-   包含证书链中的所有中间证书

> [!Note]  
>  **椭圆曲线加密 (ECC) 证书**可用于应用服务，但本指南不予讨论。 创建 ECC 证书时如需帮助，请咨询证书颁发机构。 

#### <a name="prepare-the-web-app"></a>准备 Web 应用

若要将自定义 SSL 证书绑定到 Web 应用，[应用服务计划](https://azure.microsoft.com/pricing/details/app-service/)必须位于“基本”、“标准”或“高级”层。

#### <a name="sign-in-to-azure"></a>登录 Azure

1.  打开 [Azure 门户](https://portal.azure.com/)并导航到 Web 应用。

2.  在左侧菜单中选择“应用服务”，然后选择 Web 应用名称。

![选择 Web 应用](media/azure-stack-solution-geo-distributed/image33.png)

#### <a name="check-the-pricing-tier"></a>检查定价层

1.  在 Web 应用页面的左侧导航窗格中，向下滚动到“设置”部分，然后选择“纵向扩展(应用服务计划)”。

    ![扩展菜单](media/azure-stack-solution-geo-distributed/image34.png)

1.  确保 Web 应用不在“免费”或“共享”层中。 深蓝色的框突出显示了 Web 应用的当前层。

    ![检查定价层](media/azure-stack-solution-geo-distributed/image35.png)

“免费”或“共享”层不支持自定义 SSL。 若要纵向扩展，请遵循下一部分中的步骤，或者关闭“选择定价层”页面并跳到[上传和绑定 SSL 证书](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)。

#### <a name="scale-up-your-app-service-plan"></a>纵向扩展应用服务计划

1.  选择“基本”、“标准”或“高级”层。

2.  选择“选择”。

![选择定价层](media/azure-stack-solution-geo-distributed/image36.png)

显示通知即表示缩放操作已完成。

![扩展通知](media/azure-stack-solution-geo-distributed/image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>绑定 SSL 证书并合并中间证书

在链中合并多个证书。

1. 在文本编辑器中**打开收到的每个证书**。

2. 创建名为 mergedcertificate.crt 的合并证书文件。 在文本编辑器中，将每个证书的内容复制到此文件。 证书的顺序应遵循证书链中的顺序，以你的证书开头，以根证书结尾， 如以下示例所示：

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>将证书导出为 PFX

使用证书生成的私钥导出已合并的 SSL 证书。

私钥文件是通过 OpenSSL 创建的。 若要将证书导出为 PFX，请运行以下命令，替换占位符*\<私有密钥文件 >* 并*\<合并证书文件 >* 专用密钥路径和合并的证书文件。

```powershell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

出现提示时，请定义稍后将 SSL 证书上传到应用服务时要使用的密码。

如果使用 IIS 或 **Certreq.exe** 生成证书请求，请将证书安装到本地计算机，然后[将证书导出为 PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)。

#### <a name="upload-the-ssl-certificate"></a>上传 SSL 证书

1. 在 Web 应用的左侧导航窗格中选择“SSL 设置”。

2. 选择“上传证书”。

3. 在“PFX 证书文件”中选择 PFX 文件。

4. 1. 在“证书密码”中，键入导出 PFX 文件时创建的密码。

5. 选择 **“上传”**。

![上传证书](media/azure-stack-solution-geo-distributed/image38.png)

应用服务上传完证书后，该证书会显示在“SSL 设置”页面中。

![Alt text](media/azure-stack-solution-geo-distributed/image39.png)

#### <a name="bind-your-ssl-certificate"></a>绑定 SSL 证书

1.  在“SSL 绑定”部分选择“添加绑定”。

    > [!Note]  
    >  如果证书已上传，但未显示在“主机名”下拉列表中的域名内，请尝试刷新浏览器页面。

1.  在“添加 SSL 绑定”页面中，使用下拉列表选择要保护的域名，然后选择要使用的证书。

2.  在“SSL 类型”中，选择是要使用[**服务器名称指示 (SNI)**](https://en.wikipedia.org/wiki/Server_Name_Indication) 还是使用基于 IP 的 SSL。

-   **基于 SNI 的 SSL** - 可添加多个基于 SNI 的 SSL 绑定。 选择此选项可以使用多个 SSL 证书来保护同一 IP 地址上的多个域。 大多数新式浏览器（包括 Internet Explorer、Chrome、Firefox 和 Opera）都支持 SNI（在[服务器名称指示](https://wikipedia.org/wiki/Server_Name_Indication)中了解更全面的浏览器支持信息）。

-   **基于 IP 的 SSL** - 只能添加一个基于 IP 的 SSL 绑定。 选择此选项只能使用一个 SSL 证书来保护专用公共 IP 地址。 若要保护多个域，请使用同一个 SSL 证书来保护所有这些域。 这是 SSL 绑定的传统选项。

    1.  选择“添加绑定”。

    ![Alt text](media/azure-stack-solution-geo-distributed/image40.png)

应用服务上传完证书后，该证书将显示在“SSL 绑定”部分。

![Alt text](media/azure-stack-solution-geo-distributed/image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>重新映射 IP SSL 的 A 记录

如果未在 Web 应用中使用基于 IP 的 SSL，请转到[测试自定义域的 HTTPS](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl)。

默认情况下，Web 应用使用共享的公共 IP 地址。 将证书绑定到基于 IP 的 SSL 后，应用服务会立即为 Web 应用创建一个新的专用 IP 地址。

将 A 记录映射到 Web 应用后，必须使用该专用 IP 地址更新域注册表。

“自定义域”页面将会更新，显示新的专用 IP 地址。 复制[此 IP 地址](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)，然后将 [A 记录](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)重新映射到此新 IP 地址。

#### <a name="test-https"></a>测试 HTTPS

在不同的浏览器中浏览到 https://<your.custom.domain>，以确保可以访问 Web 应用。

![Alt text](media/azure-stack-solution-geo-distributed/image42.png)

> [!Note]  
> 如果发生证书验证错误，原因可能是自签名证书所致，或者在导出到 PFX 文件时遗漏了中间证书。

#### <a name="enforce-https"></a>实施 HTTPS

默认情况下，任何人可以使用 HTTP 访问 Web 应用。 可将所有 HTTP 请求重定向到 HTTPS 端口。

在 Web 应用页面中，选择“SL 设置”。 然后，在“仅 HTTPS”中，选择“启用”。

![实施 HTTPS](media/azure-stack-solution-geo-distributed/image43.png)

该操作完成后，将导航到指向应用的任一 HTTP URL。 例如：

-   https://<app_name>.azurewebsites.net
-   https://northwindcloud.com
-   <https://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>强制实施 TLS 1.1/1.2

应用默认允许 [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0，但行业标准（例如 [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard)）不再将此协议视为安全。 若要强制实施更高的 TLS 版本，请按照下列步骤操作：

1.  在 Web 应用页面的左侧导航窗格中，选择“SSL 设置”。

2.  在“TLS 版本”中，选择最低的 TLS 版本。

![强制实施 TLS 1.1 或 1.2](media/azure-stack-solution-geo-distributed/image44.png)

### <a name="create-a-traffic-manager-profile"></a>创建流量管理器配置文件

1.  选择“创建资源” > “网络” > “流量管理器配置文件” > “创建”。

2.  在“创建流量管理器配置文件”中，按如下所示完成操作：

    1.  在“名称”中提供配置文件的名称。 此名称必须在 trafficmanager.net 区域中唯一，并将生成用于访问流量管理器配置文件的 DNS 名称 trafficmanager.net。

    2.  在“路由方法”中，选择“地理路由方法”。

    3.  在“订阅”中，选择要在其中创建此配置文件的订阅。

    4.  在**资源组**中，创建新的资源组，以在其下放置此配置文件。

    5.  在**资源组位置**中，选择资源组的位置。 此设置指的是资源组的位置，对将全局部署的流量管理器配置文件没有影响。

    6.  选择“创建”。

    7.  流量管理器配置文件的全局部署完成后，它会在相应的资源组中作为资源之一列出。

    ![Alt text](media/azure-stack-solution-geo-distributed/image45.png)

### <a name="add-traffic-manager-endpoints"></a>添加流量管理器终结点

1. 在门户的搜索栏中，搜索在前面部分创建的**流量管理器配置文件**名称，并在显示的结果中选择该流量管理器配置文件。

2. 在“流量管理器配置文件”的“设置”部分，选择“终结点”。

3. 选择 **添加** 。

4. 添加 Azure Stack 终结点。

5. 对于“类型”，请选择“外部终结点”。

6. 提供此终结点的**名称**，最好是 Azure Stack 的名称。

7. 对于完全限定的域名 (**FQDN**)，请使用 Azure Stack Web 应用的外部 URL。

8. 在异地映射下选择资源所在的位置，例如，一个区域/大陆**欧洲。**

9. 在国家/地区下拉列表显示，下选择将应用到此终结点，例如，国家/地区**德国**。

10. 使“添加为已禁用”保持未选中状态。

11. 选择“确定”。

12. 添加 Azure 终结点：

    1.  对于“类型”，请选择“Azure 终结点”。

    2.  提供此终结点的**名称**。

    3.  对于“目标资源类型”，请选择“应用服务”。

    4.  对于“目标资源”，请选择“选择应用服务”以显示同一订阅下的 Web 应用列表。 在“资源”中，选取要用作第一个终结点的应用服务。

13. 在异地映射下选择资源所在的位置，例如，一个区域/大陆  **/中美 America/加勒比海地区。**

14. 在国家/地区下拉列表显示，将其留空以选择所有上述区域分组。

15. 使“添加为已禁用”保持未选中状态。

16. 选择“确定”

    > [!Note]  
    >  创建至少一个地理范围为“全部(全球)”的终结点，作为资源的默认终结点。

1. 添加完这两个终结点后，这两个终结点会显示在“流量管理器配置文件”中，并且其监视状态为“联机”。

    ![Alt text](media/azure-stack-solution-geo-distributed/image46.png)

**全球企业依赖于 Azure 异地分布功能**

通过 Azure 流量管理器和地理位置特定的终结点定向数据流量，全球企业可以遵守区域法规，保持数据的合规性和安全性，这对于当地企业的成功和远地运营而言都至关重要。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
