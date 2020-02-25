---
title: 使用 Azure 和 Azure Stack 中心配置混合云连接
description: 了解如何使用 Azure 和 Azure Stack 中心配置混合云连接。
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 5ed6869b4c03c9e360cd12a98e206941a3842bfc
ms.sourcegitcommit: a7db4594de43c31fe0c51e60e84fdaf4d41ef1bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2020
ms.locfileid: "77568480"
---
# <a name="configure-hybrid-cloud-connectivity-using-azure-and-azure-stack-hub"></a>使用 Azure 和 Azure Stack 中心配置混合云连接

你可以使用混合连接模式，通过全球 Azure 中的安全性和 Azure Stack 中心访问资源。

在此解决方案中，你将构建一个示例环境，用于：

> [!div class="checklist"]
> - 将数据保留在本地以满足隐私或法规要求，但要保持对全球 Azure 资源的访问权限。
> - 在全球 Azure 中使用云缩放的应用部署和资源时，请维护旧系统。

> [!Tip]  
> ![hybrid-pillars](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack 中心是 Azure 的扩展。 Azure Stack 中心为本地环境带来了云计算的灵活性和革新，使你能够在任何位置构建和部署混合应用，从而实现了唯一的混合云。  
> 
> [混合应用程序的设计注意事项](overview-app-design-considerations.md)查看软件质量的支柱（放置、可伸缩性、可用性、复原能力、可管理性和安全性），以便设计、部署和操作混合应用程序。 设计注意事项有助于优化混合应用设计，并最大程度减少生产环境中的挑战。


## <a name="prerequisites"></a>先决条件

构建混合连接部署需要几个组件。 其中某些组件需要一定的时间来准备，因此请相应地进行规划。

**Azure Stack 中心**

Azure OEM/硬件合作伙伴可以部署生产 Azure Stack 中心，所有用户都可以部署 Azure Stack 开发工具包（ASDK）。

**Azure Stack 集线器组件**

Azure Stack 中心操作员必须部署应用服务、创建计划和产品/服务、创建租户订阅并添加 Windows Server 2016 映像。 如果已具有这些组件，请在开始此解决方案之前确保它们满足要求。

此解决方案示例假设你对 Azure 和 Azure Stack 中心有一些基本知识。 若要在启动解决方案之前了解详细信息，请阅读以下文章：

 - [Azure 简介](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack 集线器关键概念](../operator/azure-stack-overview.md)

### <a name="azure"></a>Azure

 - 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
 - 在 Azure 中创建[web 应用](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts)。 请记下 web 应用 URL，因为你将在解决方案中需要它。

### <a name="azure-stack-hub"></a>Azure Stack 中心

 - 使用生产 Azure Stack 中心或从 https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1部署 Azure Stack 开发工具包。
   >[!Note]
   >部署 ASDK 可能最多需要7小时，因此请相应地进行规划。

 - 将[应用服务](../operator/azure-stack-app-service-deploy.md)PaaS 服务部署到 Azure Stack 中心。
 - 在 Azure Stack 中心环境中[创建计划和产品/服务](../operator/service-plan-offer-subscription-overview.md)。
 - 在 Azure Stack 中心环境中[创建租户订阅](../operator/azure-stack-subscribe-plan-provision-vm.md)。

### <a name="before-you-begin"></a>开始之前

在开始配置混合云连接之前，请确保满足以下条件：

 - 你需要 VPN 设备的面向外部的公共 IPv4 地址。 此 IP 地址不能位于 NAT 后面（网络地址转换）。
 - 所有资源都部署在同一区域/位置。

#### <a name="solution-example-values"></a>解决方案示例值

此解决方案中的示例使用以下值。 您可以使用这些值创建测试环境，或参考这些值以更好地了解示例。 有关 VPN 网关设置的详细信息，请参阅[关于 Vpn 网关设置](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings)。

连接规范：

 - **VPN 类型**：基于路由
 - **连接类型**：站点到站点（IPsec）
 - **网关类型**：VPN
 - **Azure 连接名称**： azure Test-azurestack-S2SGateway （门户将自动填充此值）
 - **Azure Stack 中心连接名称**： Test-azurestack-S2SGateway （门户会自动填充此值）
 - **共享密钥**：与 VPN 硬件兼容，在连接两端都具有匹配值
 - **订阅**：任何首选订阅
 - **资源组**：测试-基础

网络和子网 IP 地址：

| Azure/Azure Stack 中心连接 | 名称 | 子网 | IP 地址 |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure Stack 集线器 vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Azure 虚拟网络网关 | Azure-网关 |  |  |
| Azure Stack 中心虚拟网络网关 | Test-azurestack-网关 |  |  |
| Azure 公共 IP | Azure-GatewayPublicIP |  | 创建时确定 |
| Azure Stack 中心公共 IP | Test-azurestack-GatewayPublicIP |  | 创建时确定 |
| Azure 本地网关 | Test-azurestack-S2SGateway<br>   10.100.100.0/23 |  | Azure Stack 中心公共 IP 值 |
| Azure Stack 中心本地网络网关 | Azure-S2SGateway<br>10.100.102.0/23 |  | Azure 公共 IP 值 |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack-hub"></a>在全球 Azure 中创建虚拟网络并 Azure Stack 中心

按照以下步骤使用门户创建虚拟网络。 如果使用本文作为解决方案，则可以使用这些[示例值](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values)。 如果你使用本文来配置生产环境，请将示例设置替换为你自己的值。

> [!IMPORTANT]
> 必须确保 Azure 或 Azure Stack 中心 vNet 地址空间中的 IP 地址不重叠。

在 Azure 中创建 vNet：

1. 使用浏览器连接到[Azure 门户](https://portal.azure.com/)，并使用 Azure 帐户登录。
2. 选择“创建资源”。 在 "**搜索 marketplace** " 字段中，输入 "虚拟网络"。 从结果中选择 "**虚拟网络**"。
3. 从 "**选择部署模型**" 列表中，选择 "**资源管理器**"，然后选择 "**创建**"。
4. 在 "**创建虚拟网络**" 上，配置 VNet 设置。 必需的字段名称以红色星号作为前缀。  输入有效值后，星号将变为绿色复选标记。

在 Azure Stack 集线器中创建 vNet：

* 使用 Azure Stack 中心**租户门户**重复前面的步骤（1-4）。

## <a name="add-a-gateway-subnet"></a>添加网关子网

在将虚拟网络连接到网关之前，需要为要连接到的虚拟网络创建网关子网。 网关服务使用你在网关子网中指定的 IP 地址。

在[Azure 门户](https://portal.azure.com/)中，导航到要在其中创建虚拟网络网关的资源管理器虚拟网络。

1. 选择 vNet 以打开 "**虚拟网络**" 页。
2. 在 "**设置**" 中，选择 "**子网**"。
3. 在 "**子**网" 页上，选择 " **+ 网关子网**" 打开 "**添加子网**" 页。

    ![添加网关子网](media/solution-deployment-guide-connectivity/image4.png)

4. 子网的**名称**自动填充为值 "GatewaySubnet"。 Azure 需要此值才能识别作为网关子网的子网。
5. 更改提供的用于匹配配置要求的**地址范围**值，然后选择 **"确定"** 。

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>在 Azure 中创建虚拟网络网关并 Azure Stack

使用以下步骤在 Azure 中创建虚拟网络网关。

1. 在门户页的左侧，选择 " **+** "，并在搜索字段中输入 "虚拟网络网关"。
2. 在**结果**中，选择 "**虚拟网络网关**"。
3. 在 "**虚拟网络网关**" 中，选择 "**创建**" 以打开 "**创建虚拟网络网关**" 页。
4. 在 "**创建虚拟网络网关**" 上，指定网络网关的值，如**教程示例值**和以下其他值中所示：

   - **SKU**：基本
   - **虚拟网络**：选择前面创建的虚拟网络。 将自动选择创建的网关子网。
   - **第一个 IP 配置**：网关的公共 IP。
     - 选择 "**创建网关 IP 配置**"，此操作将转到 "**选择公共 IP 地址**" 页。
     - 选择 " **+ 新建**" 打开 "**创建公共 IP 地址**" 页。
     - 输入公共 IP 地址的**名称**。 将 SKU 保留为 "**基本**"，然后选择 **"确定"** 保存所做的更改。

       > [!Note]
       > 目前，VPN 网关仅支持动态公共 IP 地址分配。 但是，这并不意味着 IP 地址在分配到 VPN 网关后会更改。 公共 IP 地址只在删除或重新创建网关时更改。 调整大小、重置或其他 VPN 网关内部维护/升级时，不会更改 IP 地址。

4. 验证网关设置。
5. 选择 "**创建**"，创建 VPN 网关。 将验证网关设置，并在仪表板上显示 "正在部署虚拟网络网关" 磁贴。

   >[!Note]
   >创建网关最多可能需要 45 分钟。 可能需要刷新门户页才能看到完成状态。

    创建网关后，可以通过在门户中查看虚拟网络来查看分配给它的 IP 地址。 网关显示为连接的设备。 若要查看有关网关的详细信息，请选择设备。

6. 在 Azure Stack 中心部署上重复前面的步骤（1-5）。

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack-hub"></a>在 Azure 中创建本地网络网关，并 Azure Stack 中心

本地网络网关通常是指本地位置。 为该站点指定一个 Azure 或 Azure Stack 中心可以引用的名称，然后指定：

- 要为其创建连接的本地 VPN 设备的 IP 地址。
- 将通过 VPN 网关路由到 VPN 设备的 IP 地址前缀。 指定的地址前缀是位于本地网络的前缀。

  >[!Note]
  >如果本地网络发生更改，或者需要更改 VPN 设备的公共 IP 地址，则可以在以后轻松地更新这些值。

1. 在门户中，选择 " **+ 创建资源**"。
2. 在搜索框中，输入 "**本地网络网关**"，然后选择 "**输入**" 进行搜索。 将显示结果列表。
3. 选择 "**本地网络网关**"，然后选择 "**创建**" 以打开 "**创建本地网络网关**" 页。
4. 在 "**创建本地网络网关**" 中，使用**教程示例值**指定本地网络网关的值。 包括以下其他值。

    - **IP 地址**：希望 Azure 或 Azure Stack 中心连接到的 VPN 设备的公共 IP 地址。 指定一个不在 NAT 后面的有效公共 IP 地址，以便 Azure 可以访问该地址。 如果你现在没有 IP 地址，可以使用示例中的值作为占位符，但必须返回并将占位符替换为 VPN 设备的公共 IP 地址。 在提供有效地址之前，Azure 无法连接到设备。
    - **地址空间**：此本地网络所代表的网络的地址范围。 可以添加多个地址空间范围。 请确保指定的范围与要连接到的其他网络的范围不重叠。 Azure 会将指定的地址范围路由到本地 VPN 设备 IP 地址。 如果要连接到本地站点而不是示例值，请使用自己的值。
    - **配置 bgp 设置**：仅在配置 bgp 时使用。 否则，请不要选择此选项。
    - **订阅**：验证是否显示了正确的订阅。
    - **资源组**：选择要使用的资源组。 你可以创建新的资源组，或选择已创建的资源组。
    - **位置**：选择将在其中创建此对象的位置。 你可能想要选择 VNet 所在的同一位置，但不需要这样做。
5. 完成指定必需值后，选择 "**创建**" 以创建本地网关。
6. 在 Azure Stack 中心部署上重复上述步骤（1-5）。

## <a name="configure-your-connection"></a>配置连接

到本地网络的站点到站点连接需要 VPN 设备。 你配置的 VPN 设备称为连接。 若要配置连接，需要：

- 共享密钥。 此密钥与在创建站点到站点 VPN 连接时指定的共享密钥相同。 在示例中，我们使用基本的共享密钥。 建议生成更复杂的可用密钥。
- 虚拟网络网关的公共 IP 地址。 可以通过 Azure 门户、PowerShell 或 CLI 查看公共 IP 地址。 若要使用 Azure 门户查找 VPN 网关的公共 IP 地址，请导航到 "虚拟网络网关"，然后选择你的网关的名称。

使用以下步骤在虚拟网络网关和本地 VPN 设备之间创建站点到站点 VPN 连接。

1. 在 Azure 门户中，选择 " **+ 创建资源**"。
2. 搜索**连接**。
3. 在**结果**中，选择 "**连接**"。
4. 在 "**连接**" 中，选择 "**创建**"。
5. 在 "**创建连接**" 上，配置以下设置：

    - **连接类型**：选择 "站点到站点（IPSec）"。
    - **资源组**：选择测试资源组。
    - **虚拟网络网关**：选择创建的虚拟网络网关。
    - **本地网络网关**：选择创建的本地网络网关。
    - **连接名称**：使用两个网关上的值会自动填充此名称。
    - **共享密钥**：此值必须与用于本地 VPN 设备的值匹配。 本教程示例使用 "abc123"，但应使用更复杂的内容。 重要的是，此值必须与配置 VPN 设备时指定的值相同。
    - “订阅”、“资源组”和“位置”值是固定的。

6. 选择 **"确定"** 以创建连接。

可以在虚拟网络网关的 "**连接**" 页中查看连接。 状态将从*未知*到*连接*，然后才会*成功*。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
