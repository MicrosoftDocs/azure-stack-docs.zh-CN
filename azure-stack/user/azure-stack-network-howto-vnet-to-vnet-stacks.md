---
title: 如何使用 Fortinet FortiGate NVA 在 Azure Stack 集线器中建立 vnet 到 VNET 的连接
description: 了解如何使用 Fortinet FortiGate NVA 在 Azure Stack 集线器中建立 vnet 到 VNET 的连接
author: mattbriggs
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: c39285cec673a7274304cb2dbe1179e5478ddc7d
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884599"
---
# <a name="establish-a-vnet-to-vnet-connection-in-azure-stack-hub-with-fortinet-fortigate-nva"></a>在 Fortinet FortiGate NVA 的 Azure Stack 集线器中建立 vnet 到 VNET 的连接

本文介绍如何使用 Fortinet FortiGate NVA （一种网络虚拟设备）将同一 Azure Stack 集线器中的 VNET 连接到另一 Azure Stack 中心的 VNET。

本文介绍了当前 Azure Stack 集线器限制，该限制允许租户在两个环境中仅设置一个 VPN 连接。 用户将了解如何在 Linux 虚拟机上设置自定义网关，该网关允许跨不同 Azure Stack 集线器进行多个 VPN 连接。 本文中的过程使用每个 VNET 中的 FortiGate NVA 部署两个 Vnet：每个 Azure Stack 中心环境一个部署。 它还详细说明了在两个 Vnet 之间设置 IPSec VPN 所需的更改。 应为每个 Azure Stack 中心中的每个 VNET 重复本文中的步骤。 

## <a name="prerequisites"></a>必备组件

-  访问具有可用容量的 Azure Stack 集线器集成系统，以部署此解决方案所需的计算、网络和资源要求。 

    > [!Note]  
    > 由于 ASDK 中的网络限制，这些说明**不适**用于 AZURE STACK 开发工具包（ASDK）。 有关详细信息，请参阅[ASDK 要求和注意事项](https://docs.microsoft.com/azure-stack/asdk/asdk-deploy-considerations)。

-  已下载网络虚拟设备（NVA）解决方案并将其发布到 Azure Stack 中心市场。 NVA 控制从外围网络到其他网络或子网的网络流量。 此过程使用[Fortinet FortiGate 下一代防火墙单一 VM 解决方案](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm)。

-  至少两个可用的 FortiGate 许可证文件来激活 FortiGate NVA。 有关如何获取这些许可证的信息，请参阅 Fortinet 文档库文章[注册和下载许可证](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)。

    此过程使用[单个 FortiGate 部署](ttps://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment)。 可以在本地网络中找到有关如何将 FortiGate NVA 连接到 Azure Stack 集线器 VNET 的步骤。

    有关如何在主动-被动（HA）设置中部署 FortiGate 解决方案的详细信息，请参阅 Fortinet 文档库文章[HA For FortiGate On Azure](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure)。

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
> \* 选择一组不同的地址空间和子网前缀（如果与本地网络环境重叠，其中包括任何一个 Azure Stack 集线器的 VIP 池）。 还要确保地址范围不相互重叠。 * *

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>部署 FortiGate NGFW Marketplace 项

在 Azure Stack 中心环境中重复上述步骤。 

1. 打开 Azure Stack 集线器用户门户。 请确保使用至少具有订阅的参与者权限的凭据。

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image5.png)

1. 选择 "**创建资源**" 并搜索 `FortiGate`。

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image6.png)

2. 选择**FORTIGATE NGFW** ，然后选择 "**创建**"。

3. 使用 "[部署参数](#deployment-parameters)" 表中的参数完成**基本**操作。

    您的窗体应包含以下信息：

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image7.png)

4. 选择“确定”。

5. 提供[部署参数](#deployment-parameters)的虚拟网络、子网和 VM 大小详细信息。

    如果要使用不同的名称和范围，请注意不要使用与其他 Azure Stack 中心环境中的其他 VNET 和 FortiGate 资源冲突的参数。 在 VNET 中设置 VNET IP 范围和子网范围时，尤其如此。 检查它们是否与你创建的其他 VNET 的 IP 范围不重叠。

6. 选择“确定”。

7. 配置将用于 FortiGate NVA 的公共 IP：

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image8.png)

8. 选择 **"确定"** ，然后选择 **"确定"** 。

9. 选择“创建”。

部署需要大约10分钟。 你现在可以重复这些步骤，在其他 Azure Stack 中心环境中创建其他 FortiGate NVA 和 VNET 部署。

## <a name="configure-routes-udrs-for-each-vnet"></a>为每个 VNET 配置路由（Udr）

针对部署、forti1-rg1 和 forti2-rg1 执行以下步骤。

1. 在 Azure Stack 中心门户中导航到 forti1-rg1 资源组。

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image9.png)

2. 选择 "forti1-forti1-InsideSubnet" 资源。

3. 选择 "**设置**" 下的 "**路由**"。

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image10.png)

4. 删除**到 Internet**路由。

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image11.png)

5. 请选择“是”。

6. 选择 **添加** 。

7. 将**路由**命名 `to-forti1` 或 `to-forti2`。 如果使用不同的 IP 范围，请使用 IP 范围。

8. 输入：
    - forti1： `172.17.0.0/16`  
    - forti2： `172.16.0.0/16`  

    如果使用不同的 IP 范围，请使用 IP 范围。

9. 选择 "**虚拟设备**" 作为**下一跃点类型**。
    - forti1： `172.16.1.4`  
    - forti2： `172.17.0.4`  

    如果使用不同的 IP 范围，请使用 IP 范围。

    ![](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image12.png)

10. 选择“保存”。

为每个资源组的每个**InsideSubnet**路由重复上述步骤。

## <a name="activate-the-fortigate-nvas-and-configure-an-ipsec-vpn-connection-on-each-nva"></a>激活 FortiGate Nva 并在每个 NVA 上配置 IPSec VPN 连接

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

7.  在出现提示时，选择 "**备份配置" 和 "升级**并继续"。

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

完成上述**两个**nva 后，请执行以下操作：

1.  在 forti2 FortiGate web 控制台上，选择 "**监视** > **IPsec 监视器**"。 

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image20.png)

2.  突出显示 `conn1`，然后选择 "**打开** > **所有阶段 2**" 选择器。

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image21.png)


## <a name="test-and-validate-connectivity"></a>测试和验证连接

现在应能够通过 FortiGate Nva 在每个 VNET 之间路由。 若要验证连接，请在每个 VNET 的 InsideSubnet 中创建一个 Azure Stack 中心 VM。 可以通过门户、CLI 或 PowerShell 创建 Azure Stack 中心 VM。 创建 Vm 时：

-   Azure Stack 中心 Vm 位于每个 VNET 的**InsideSubnet**上。

-   创建时**不**会将任何 nsg 应用于 VM （也就是说，如果在门户中创建 vm，则会删除默认情况下添加的 NSG。

-   确保 VM 防火墙规则允许要用于测试连接的通信。 出于测试目的，如果有可能，建议完全在操作系统内禁用防火墙。

## <a name="next-steps"></a>后续步骤

[Azure Stack 集线器网络的差异和注意事项](azure-stack-network-differences.md)  
[使用 Fortinet FortiGate 在 Azure Stack 集线器中提供网络解决方案](../operator/azure-stack-network-solutions-enable.md)  