---
title: 使用 Azure 和 Azure Stack 配置混合云连接 | Microsoft Docs
description: 了解如何使用 Azure 和 Azure Stack 配置混合云连接。
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/14/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: a2311466bc6949bbc1ea372410373654352418fa
ms.sourcegitcommit: 35b13ea6dc0221a15cd0840be796f4af5370ddaf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68603017"
---
# <a name="configure-hybrid-cloud-connectivity-with-azure-and-azure-stack"></a>配置与 Azure 和 Azure Stack 的混合云连接

适用对象：*Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用混合连接模式在公有云 Azure 和 Azure Stack 中安全地访问资源。

在此解决方案中, 你将构建一个示例环境, 用于:

> [!div class="checklist"]
> - 将数据保留在本地以满足隐私或法规要求, 但要保持对全球 Azure 资源的访问权限。
> - 在公有云 Azure 中使用云规模的应用部署和资源的同时，保留旧版系统。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack 是 Azure 的扩展。 Azure Stack 将云计算的灵活性和创新带入本地环境, 从而实现了唯一的混合云, 使你能够在任何位置构建和部署混合应用。  
> 
> [混合应用程序的设计注意事项](azure-stack-edge-pattern-overview.md)查看软件质量的支柱 (放置、可伸缩性、可用性、复原能力、可管理性和安全性), 以便设计、部署和操作混合应用程序。 设计注意事项有助于优化混合应用设计, 并最大程度减少生产环境中的挑战。


## <a name="prerequisites"></a>先决条件

生成混合连接部署需要一些组件。 其中某些组件需要一定的时间来准备, 因此请相应地进行规划。

**Azure Stack**

Azure OEM/硬件合作伙伴可以部署生产 Azure Stack, 所有用户都可以部署 Azure Stack 开发工具包 (ASDK)。

**Azure Stack 组件**

Azure Stack 操作员必须部署应用服务、创建计划和产品/服务、创建租户订阅并添加 Windows Server 2016 映像。 如果已具有这些组件, 请在开始此解决方案之前确保它们满足要求。

此解决方案假设你对 Azure 和 Azure Stack 有一些基本知识。 若要在启动解决方案之前了解详细信息, 请阅读以下文章:

 - [Azure 简介](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure Stack 的重要概念](../operator/azure-stack-overview.md)

### <a name="azure"></a>Azure

 - 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
 - 在 Azure 中创建[web 应用](https://docs.microsoft.com/vsts/build-release/apps/cd/azure/aspnet-core-to-azure-webapp?view=vsts&tabs=vsts)。 请记下 web 应用 URL, 因为你将在解决方案中需要它。

### <a name="azure-stack"></a>Azure Stack

 - 使用生产型 Azure Stack，或部署 https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 提供的 Azure Stack 开发工具包。
   >[!Note]
   >部署 ASDK 可能需要长达 7 小时的时间，因此请进行相应的计划。

 - 将[应用服务](../operator/azure-stack-app-service-deploy.md) PaaS 服务部署到 Azure Stack。
 - 在 Azure Stack 环境中[创建计划和套餐](../operator/azure-stack-plan-offer-quota-overview.md)。
 - 在 Azure Stack 环境中[创建租户订阅](../operator/azure-stack-subscribe-plan-provision-vm.md)。

### <a name="before-you-begin"></a>开始之前

在开始配置混合云连接之前，请验证自己是否符合以下条件：

 - 需要一个用于 VPN 设备的面向外部的公共 IPv4 地址。 此 IP 地址不能位于 NAT 后面 (网络地址转换)。
 - 所有资源都部署在同一区域/位置。

####  <a name="solution-example-values"></a>解决方案示例值

此解决方案中的示例使用以下值。 可使用这些值创建测试环境，或根据这些值来更好地理解示例。 有关 VPN 网关设置的详细信息, 请参阅[关于 Vpn 网关设置](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-gateway-settings)。

连接规范：

 - **VPN 类型**: 基于路由
 - **连接类型**: 站点到站点 (IPsec)
 - **网关类型**：VPN
 - **Azure 连接名称**：Azure-Gateway-AzureStack-S2SGateway（门户会自动填充此值）
 - **Azure Stack 连接名称**：AzureStack-Gateway-Azure-S2SGateway（门户会自动填充此值）
 - **共享密钥**: 与 VPN 硬件兼容, 在连接两端都具有匹配值
 - **订阅**: 任何首选订阅
 - **资源组**：Test-Infra

网络和子网 IP 地址：

| Azure/Azure Stack 连接 | 名称 | Subnet | IP 地址 |
|-------------------------------------|---------------------------------------------|---------------------------------------|-----------------------------|
| Azure vNet | ApplicationvNet<br>10.100.102.9/23 | ApplicationSubnet<br>10.100.102.0/24 |  |
|  |  | GatewaySubnet<br>10.100.103.0/24 |  |
| Azure Stack vNet | ApplicationvNet<br>10.100.100.0/23 | ApplicationSubnet <br>10.100.100.0/24 |  |
|  |  | GatewaySubnet <br>10.100101.0/24 |  |
| Azure 虚拟网关 | Azure-Gateway |  |  |
| Azure Stack 虚拟网关 | AzureStack-Gateway |  |  |
| Azure 公共 IP | Azure-GatewayPublicIP |  | 在创建时确定 |
| Azure Stack 公共 IP | AzureStack-GatewayPublicIP |  | 在创建时确定 |
| Azure 本地网关 | AzureStack-S2SGateway<br>   10.100.100.0/23 |  | Azure Stack 公共 IP 值 |
| Azure Stack 本地网关 | Azure-S2SGateway<br>10.100.102.0/23 |  | Azure 公共 IP 值 |

## <a name="create-a-virtual-network-in-global-azure-and-azure-stack"></a>在公有云 Azure 和 Azure Stack 中创建虚拟网络

请执行以下步骤，以便通过门户创建虚拟网络。 如果使用本文作为解决方案, 则可以使用这些[示例值](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#values)。 如果你使用本文来配置生产环境, 请将示例设置替换为你自己的值。

> [!IMPORTANT]
> 必须确保在 Azure 或 Azure Stack vNet 地址空间中没有 IP 地址重叠现象。

若要在 Azure 中创建 VNet，请执行以下操作：

1. 使用浏览器连接到 [Azure 门户](https://portal.azure.com/)并使用 Azure 帐户登录。
2. 选择“创建资源”。 在 "**搜索 marketplace** " 字段中, 输入 "虚拟网络"。 从结果中选择 "**虚拟网络**"。
3. 从“选择部署模型”列表中选择“资源管理器”，然后选择“创建”。
4. 在“创建虚拟网络”中配置 VNet 设置。 必填字段名称带有红色星号前缀。  输入有效值时，该星号变为绿色复选标记。

若要在 Azure Stack 中创建 VNet，请执行以下操作：

* 使用 Azure Stack **租户门户**重复前述步骤 (1-4)。

## <a name="add-a-gateway-subnet"></a>添加网关子网

将虚拟网络连接到网关之前，需创建要连接的虚拟网络的网关子网。 网关服务使用在网关子网中指定的 IP 地址。

在 [Azure 门户](https://portal.azure.com/)中，导航到要在其中创建虚拟网关的资源管理器虚拟网络。

1. 选择 vNet，以便打开“虚拟网络”页。
2. 在“设置”中选择“子网”。
3. 在“子网”页中选择“+网关子网”，打开“添加子网”页。

    ![添加网关子网](media/azure-stack-solution-hybrid-connectivity/image4.png)

4. 子网的“名称”自动填充为值“GatewaySubnet”。 Azure 需要此值才能识别作为网关子网的子网。
5. 更改按配置要求提供的“地址范围”值，然后选择“确定”。

## <a name="create-a-virtual-network-gateway-in-azure-and-azure-stack"></a>在 Azure 和 Azure Stack 中创建虚拟网关

请执行以下步骤，以便在 Azure 中创建虚拟网关。

1. 在门户页左侧选择“+”，然后在搜索字段中输入“虚拟网关”。
2. 在“结果”中选择“虚拟网关”。
3. 在“虚拟网关”中选择“创建”，打开“创建虚拟网关”页。
4. 在“创建虚拟网关”中指定网络网关的值，如**教程示例值**中所示，然后指定下述其他值：

   - **SKU**：基本
   - **虚拟网络**：选择此前创建的虚拟网络。 已创建的网关子网会自动选中。
   - **第一个 IP 配置**：网关的公共 IP。
     - 选择“创建网关 IP 配置”，然后就会转到“选择公共 IP 地址”页。
     - 选择“+新建”，打开“创建公共 IP 地址”页。
     - 输入公共 IP 地址的“名称”。 将“SKU”保留为“基本”，然后选择“确定”，以便保存所做的更改。

       > [!Note]
       > 目前，VPN 网关仅支持动态公共 IP 地址分配。 但是，这并不意味着 IP 地址在分配到 VPN 网关后会更改。 公共 IP 地址只在删除或重新创建网关时更改。 VPN 网关进行大小调整、重置或其他内部维护/升级不会更改 IP 地址。

4. 验证网关设置。
5. 选择“创建”，创建 VPN 网关。 将会验证网关设置，并会在仪表板上显示“正在部署虚拟网关”磁贴。

   >[!Note]
   >创建网关最多可能需要 45 分钟。 可能需要刷新门户页才能看到完成状态。

    创建网关后，可以通过在门户中查看虚拟网络，来查看分配给网关的 IP 地址。 网关显示为连接的设备。 若要查看网关的详细信息，请选择设备。

6. 在 Azure Stack 部署上重复前述步骤 (1-5)。

## <a name="create-the-local-network-gateway-in-azure-and-azure-stack"></a>在 Azure 和 Azure Stack 中创建本地网关

本地网络网关通常是指本地位置。 请为站点提供一个可供 Azure 或 Azure Stack 引用的名称，然后指定以下配置：

- 要为其创建连接的本地 VPN 设备的 IP 地址。
- 可以通过 VPN 网关将其路由到 VPN 设备的 IP 地址前缀。 指定的地址前缀是位于本地网络的前缀。

  >[!Note]
  >如果之后本地网络发生了更改，或需要更改 VPN 设备的公共 IP 地址，可轻松更新这些值。

1. 在门户中选择“+创建资源”。
2. 在搜索框中输入“本地网关”，然后按 **Enter** 进行搜索。 将显示结果列表。
3. 选择“本地网关”，然后选择“创建”，打开“创建本地网关”页。
4. 使用**教程示例值**，在“创建本地网关”上指定本地网关的值。 包括以下其他值。

    - **IP 地址**：希望 Azure 或 Azure Stack 连接到的 VPN 设备的公共 IP 地址。 指定一个有效的不在 NAT 后面的公共 IP 地址，这样 Azure 就能访问该地址。 如果目前没有 IP 地址，可以使用示例中显示的作为占位符的值，但是需要返回来将占位符替换为 VPN 设备的公共 IP 地址。 除非提供有效的地址，否则 Azure 无法连接到设备。
    - **地址空间**: 此本地网络所代表的网络的地址范围。 可以添加多个地址空间范围。 请确保指定的范围没有与要连接到的其他网络的范围重叠。 Azure 会将指定的地址范围路由到本地 VPN 设备 IP 地址。 如果需要连接到本地站点，请使用自己的值而不是示例值。
    - **配置 BGP 设置**：仅在配置 BGP 时使用。 否则, 请不要选择此选项。
    - **订阅**：确保显示正确的订阅。
    - **资源组**：选择要使用的资源组。 可以创建新的资源组或选择已创建的资源组。
    - **位置**：选择将在其中创建此对象的位置。 可选择 VNet 所在的位置，但这不是必须的。
5. 将所需值指定完以后，选择“创建”即可创建本地网关。
6. 在 Azure Stack 部署上重复这些步骤 (1-5)。

## <a name="configure-your-connection"></a>配置连接

到本地网络的站点到站点连接需要 VPN 设备。 配置的 VPN 设备称为“连接”。 若要配置连接，需提供：

- 共享密钥。 此密钥与在创建站点到站点 VPN 连接时指定的共享密钥相同。 在示例中，我们使用基本的共享密钥。 建议生成更复杂的可用密钥。
- 虚拟网络网关的公共 IP 地址。 可以通过 Azure 门户、PowerShell 或 CLI 查看公共 IP 地址。 若要使用 Azure 门户查找 VPN 网关的公共 IP 地址, 请导航到 "虚拟网络网关", 然后选择你的网关的名称。

使用以下步骤在虚拟网络网关和本地 VPN 设备之间创建站点到站点 VPN 连接。

1. 在 Azure 门户中，选择“+创建资源”。
2. 搜索**连接**。
3. 在“结果”中选择“连接”。
4. 在“连接”上选择“创建”。
5. 在“创建连接”上配置以下设置：

    - **连接类型**：选择 "站点到站点 (IPSec)"。
    - **资源组**：选择你的测试资源组。
    - **虚拟网络网关**：选择所创建的虚拟网络网关。
    - **本地网络网关**：选择创建的本地网络网关。
    - **连接名称**：此名称是使用两个网关上的值自动填充的。
    - **共享密钥**：此值必须与用于本地 VPN 设备的值匹配。 本教程示例使用 "abc123", 但应使用更复杂的内容。 重要的是，此值必须与配置 VPN 设备时指定的值相同。
    - “**订阅**”、“**资源组**”和“**位置**”值是固定的。

6. 选择“确定”以创建连接。

可在虚拟网关的“连接”页中查看连接。 “状态”会从“未知”转换为“正在连接”，再转换为“成功”。

## <a name="next-steps"></a>后续步骤

- 若要了解有关 Azure 云模式的详细信息，请参阅[云设计模式](https://docs.microsoft.com/azure/architecture/patterns)。
