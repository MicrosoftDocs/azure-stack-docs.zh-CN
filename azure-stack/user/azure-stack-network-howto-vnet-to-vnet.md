---
title: 如何通过 VNET 对等互连连接两个 Azure Stack 集线器 |Microsoft Docs
description: 了解如何通过 VNET 对等互连连接两个 Azure Stack 集线器。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/03/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: b37677b10aaddb92b0f840900ac846b1a6478bc5
ms.sourcegitcommit: 7dd685fddf2f5d7a0c0a20fb8830ca5a061ed031
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76259848"
---
# <a name="connect-two-vnets-through-peering"></a>通过对等互连连接两个 Vnet

本文介绍如何在相同环境中的两个虚拟网络之间创建连接。 设置连接时，会了解 Azure Stack 集线器中的 VPN 网关的工作原理。 使用 Fortinet FortiGate 连接同一 Azure Stack 中心环境中的两个 Vnet。 此过程将两个 Vnet 部署在每个 VNET 中，每个 FortiGate NVA，一个网络虚拟设备，在单独的资源组中。 它还详细说明了在两个 Vnet 之间设置 IPSec VPN 所需的更改。 为每个 VNET 部署重复本文中的步骤。

## <a name="prerequisites"></a>必备组件

-   访问具有可用容量的系统以部署此解决方案所需的计算、网络和资源要求。

-  已下载网络虚拟设备（NVA）解决方案并将其发布到 Azure Stack 中心市场。 NVA 控制从外围网络到其他网络或子网的网络流量。 此过程使用[Fortinet FortiGate 下一代防火墙单一 VM 解决方案](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm)。

-  至少两个可用的 FortiGate 许可证文件来激活 FortiGate NVA。 有关如何获取这些许可证的信息，请参阅 Fortinet 文档库文章[注册和下载许可证](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)。

    此过程使用[单个 FortiGate 部署](ttps://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment)。 可以在本地网络中找到有关如何将 FortiGate NVA 连接到 Azure Stack 集线器 VNET 的步骤。

    有关如何在主动-被动（HA）设置中部署 FortiGate 解决方案的详细信息，请参阅 Fortinet 文档库[中的详细信息。](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure)

## <a name="deployment-parameters"></a>部署参数

下表汇总了这些部署中用于引用的参数：

### <a name="deployment-one-forti1"></a>部署1： Forti1

| FortiGate 实例名称 | Forti1 |
|-----------------------------------|---------------------------|
| BYOL 许可证/版本 | 版 |
| FortiGate 管理用户名 | fortiadmin |
| 资源组名称 | forti1-rg1 |
| 虚拟网络名称 | forti1vnet1 |
| VNET 地址空间 | 172.16.0.0/16 * |
| 公用 VNET 子网名称 | forti1-PublicFacingSubnet |
| 公用 VNET 地址前缀 | 172.16.0.0/24 * |
| 在 VNET 子网名称中 | forti1-InsideSubnet |
| 在 VNET 子网前缀内 | 172.16.1.0/24 * |
| VM 大小 FortiGate NVA | 标准 F2s_v2 |
| 公共 IP 地址名称 | forti1-publicip1 |
| 公共 IP 地址类型 | 静态 |

### <a name="deployment-two-forti2"></a>部署2： Forti2

| FortiGate 实例名称 | Forti2 |
|-----------------------------------|---------------------------|
| BYOL 许可证/版本 | 版 |
| FortiGate 管理用户名 | fortiadmin |
| 资源组名称 | forti2-rg1 |
| 虚拟网络名称 | forti2vnet1 |
| VNET 地址空间 | 172.17.0.0/16 * |
| 公用 VNET 子网名称 | forti2-PublicFacingSubnet |
| 公用 VNET 地址前缀 | 172.17.0.0/24 * |
| 在 VNET 子网名称中 | Forti2-InsideSubnet |
| 在 VNET 子网前缀内 | 172.17.1.0/24 * |
| VM 大小 FortiGate NVA | 标准 F2s_v2 |
| 公共 IP 地址名称 | Forti2-publicip1 |
| 公共 IP 地址类型 | 静态 |

> [!Note]
> \* 选择一组不同的地址空间和子网前缀（如果与本地网络环境重叠，其中包括任何一个 Azure Stack 集线器的 VIP 池）。 还要确保地址范围不相互重叠。

## <a name="deploy-the-fortigate-ngfw"></a>部署 FortiGate NGFW

1.  打开 Azure Stack 集线器用户门户。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image5.png)

2.  选择 "**创建资源**" 并搜索 `FortiGate`。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image6.png)

3.  选择**FORTIGATE NGFW** ，然后选择 "**创建**"。

4.  使用 "[部署参数](#deployment-parameters)" 表中的参数完成**基本**操作。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image7.png)

5.  选择“确定”。

6.  使用 "[部署参数](#deployment-parameters)" 表提供 "虚拟网络"、"子网" 和 "VM 大小" 的详细信息。

    > [!Warning] 
    > 如果本地网络与 `172.16.0.0/16`的 IP 范围重叠，则必须选择并设置其他网络范围和子网。 如果要使用不同于 "[部署参数](#deployment-parameters)" 表中的名称和范围，请使用**不**会与本地网络冲突的参数。 在 VNET 中设置 VNET IP 范围和子网范围时请小心。 不希望范围与本地网络中存在的 IP 范围重叠。

7.  选择“确定”。

8.  为 Fortigate NVA 配置公共 IP：

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image8.png)

9.  选择“确定”。 再选择“确定”。

10.  选择“创建”。

部署将需要大约 10 分钟。

## <a name="configure-routes-udrs-for-each-vnet"></a>为每个 VNET 配置路由（Udr）

针对部署、forti1-rg1 和 forti2-rg1 执行以下步骤。

1. 打开 Azure Stack 集线器用户门户。

2. 选择 "资源组"。 在筛选器中键入 `forti1-rg1`，然后双击 "forti1-rg1" 资源组。

    ![资源组](./media/azure-stack-network-howto-vnet-to-onprem/image9.png)

2. 选择**forti1-forti1**资源。

3. 选择 "**设置**" 下的 "**路由**"。

    ![路由](./media/azure-stack-network-howto-vnet-to-onprem/image10.png)

4. 删除**到 Internet**路由。

    ![到 Internet](./media/azure-stack-network-howto-vnet-to-onprem/image11.png)

5. 请选择“是”。

6. 选择 "**添加**" 以添加新路由。

7. 将路由命名 `to-onprem`。

8. 输入定义 VPN 将连接到的本地网络的网络范围的 IP 网络范围。

9. 选择 "**虚拟设备**" 作为**下一跃点类型**和 `172.16.1.4`。 如果使用不同的 IP 范围，请使用 IP 范围。

    ![下一跃点类型](./media/azure-stack-network-howto-vnet-to-onprem/image12.png)

10. 选择“保存”。

你将需要 Fortinet 中的有效许可证文件以激活每个 FortiGate NVA。 在激活每个 NVA 之前，Nva 将**不**起作用。 有关如何获取许可证文件和激活 NVA 的步骤的详细信息，请参阅 Fortinet 文档库文章[注册和下载许可证](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)。

需要获取两个许可证文件–每个 NVA 一个。

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>在两个 Nva 之间创建 IPSec VPN

激活 Nva 后，请按照以下步骤在两个 Nva 之间创建 IPSec VPN。

对于 forti1 NVA 和 forti2 NVA，请执行以下步骤：

1.  通过导航到 fortiX VM 概述页获取分配的公共 IP 地址：

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image13.png)

2.  复制分配的 IP 地址，打开浏览器，然后将地址粘贴到地址栏中。 你的浏览器可能会警告你安全证书不受信任。 仍继续。

4.  输入在部署期间提供的 FortiGate 管理用户名和密码。

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image14.png)

5.  选择 "**系统** > **固件**"。

6.  选择显示最新固件的框，例如 `FortiOS v6.2.0 build0866`。

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image15.png)

7.  选择 "**备份配置和升级**" > **继续**。

8.  NVA 会将其固件更新到最新版本，然后重新启动。 此过程大约需五分钟。 重新登录到 FortiGate web 控制台。

10.  单击 " **VPN** > **IPSec 向导**"。

11. 输入 VPN 的名称，例如，在**Vpn 创建向导**中 `conn1`。

12. 选择**此站点位于 NAT 后面**。

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image16.png)

13. 选择“**下一页**”。

14. 输入要连接到的本地 VPN 设备的远程 IP 地址。

15. 选择**port1**作为**传出接口**。

16. 选择 "**预共享密钥**"，然后输入（并记录）预共享密钥。 

    > [!Note]  
    > 你将需要此密钥来设置本地 VPN 设备上的连接，即，它们必须*完全*匹配。

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image17.png)

17. 选择“**下一页**”。

18. 选择 " **port2** " 作为**本地接口**。

19. 输入本地子网范围：
    - forti1： 172.16.0.0/16
    - forti2： 172.17.0.0/16

    如果使用不同的 IP 范围，请使用 IP 范围。

20. 输入代表本地网络的适当远程子网，你将通过本地 VPN 设备连接到该网络。
    - forti1： 172.16.0.0/16
    - forti2： 172.17.0.0/16

    如果使用不同的 IP 范围，请使用 IP 范围。

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image18.png)

21. 选择“创建”

22. 选择 "**网络** > **接口**"。

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image19.png)

23. 双击 " **port2**"。

24. 在**角色**列表中选择 " **LAN** "，并选择 " **DHCP** " 作为寻址模式。

25. 选择“确定”。

对于其他 NVA，请重复上述步骤。

## <a name="bring-up-all-phase-2-selectors"></a>显示所有阶段2选择器 

完成上述*两个*nva 后，请执行以下操作：

1.  在 forti2 FortiGate web 控制台上，选择 "**监视** > **IPsec 监视器**"。 

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image20.png)

2.  突出显示 `conn1`，然后选择 "**打开** > **所有阶段 2**" 选择器。

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image21.png)

## <a name="test-and-validate-connectivity"></a>测试和验证连接

现在应能够通过 FortiGate Nva 在每个 VNET 之间路由。 若要验证连接，请在每个 VNET 的 InsideSubnet 中创建一个 Azure Stack 中心 VM。 可以通过门户、CLI 或 PowerShell 创建 Azure Stack 中心 VM。 创建 Vm 时：

-   Azure Stack 中心 Vm 位于每个 VNET 的**InsideSubnet**上。

-   创建时**不**会将任何 nsg 应用于 VM （也就是说，如果从门户创建 vm，则会删除默认情况下添加的 NSG。

-   确保 VM 防火墙规则允许用于测试连接的通信。 出于测试目的，如果有可能，建议完全在操作系统内禁用防火墙。

## <a name="next-steps"></a>后续步骤

[Azure Stack 集线器网络的差异和注意事项](azure-stack-network-differences.md)  
[使用 Fortinet FortiGate 在 Azure Stack 集线器中提供网络解决方案](../operator/azure-stack-network-solutions-enable.md)  