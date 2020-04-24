---
title: 如何通过 VNET 对等互连连接两个 Azure Stack Hub
description: 了解如何通过 VNET 对等互连连接两个 Azure Stack Hub。
author: mattbriggs
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: b1d60037dd86a6de1aac73ff5a607605ceda1614
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77703870"
---
# <a name="connect-two-vnets-through-peering"></a>通过对等互连连接两个 VNET

本文介绍如何在同一环境中的两个虚拟网络之间创建连接。 在设置连接过程中，读者可以了解 Azure Stack Hub 中 VPN 网关的工作方式。 使用 Fortinet FortiGate 在同一 Azure Stack Hub 环境中连接两个 VNET。 此过程在资源组中部署两个具有 FortiGate NVA 的 VNET，每个 VNET 位于单独的资源组中，并且都有一个网络虚拟设备。 此外，其中详细说明了在这两个 VNET 之间设置 IPSec VPN 所要做出的更改。 请对每个 VNET 部署重复本文中的步骤。

## <a name="prerequisites"></a>先决条件

-   有权访问可提供足够容量用于部署此解决方案所需的计算、网络和资源的系统。

-  已下载网络虚拟设备 (NVA) 解决方案并将其发布到 Azure Stack Hub 市场。 NVA 控制从外围网络到其他网络或子网的网络流量。 此过程使用 [Fortinet FortiGate 下一代防火墙单一 VM 解决方案](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm)。

-  至少有两个可用于激活 FortiGate NVA 的 FortiGate 许可证文件。 有关如何获取这些许可证的信息，请参阅 Fortinet 文档库文章[注册和下载许可证](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)。

    此过程使用[单一 FortiGate-VM 部署](ttps://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment)。 其中提供了在本地网络中将 FortiGate NVA 连接到 Azure Stack Hub VNET 的步骤。

    有关如何在主动-被动 (HA) 设置中部署 FortiGate 解决方案的详细信息，请参阅 Fortinet 文档库文章 [Azure 上的 FortiGate-VM 的 HA](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure) 中的详细信息。

## <a name="deployment-parameters"></a>部署参数

下表汇总了在这些部署中使用的参数供用户参考：

### <a name="deployment-one-forti1"></a>部署 1：Forti1

| FortiGate 实例名称 | Forti1 |
|-----------------------------------|---------------------------|
| BYOL 许可证/版本 | 6.0.3 |
| FortiGate 管理用户名 | fortiadmin |
| 资源组名称 | forti1-rg1 |
| 虚拟网络名称 | forti1vnet1 |
| VNET 地址空间 | 172.16.0.0/16* |
| 公共 VNET 子网名称 | forti1-PublicFacingSubnet |
| 公共 VNET 地址前缀 | 172.16.0.0/24* |
| 内部 VNET 子网名称 | forti1-InsideSubnet |
| 内部 VNET 子网前缀 | 172.16.1.0/24* |
| FortiGate NVA 的 VM 大小 | 标准 F2s_v2 |
| 公共 IP 地址名称 | forti1-publicip1 |
| 公共 IP 地址类型 | 静态 |

### <a name="deployment-two-forti2"></a>部署 2：Forti2

| FortiGate 实例名称 | Forti2 |
|-----------------------------------|---------------------------|
| BYOL 许可证/版本 | 6.0.3 |
| FortiGate 管理用户名 | fortiadmin |
| 资源组名称 | forti2-rg1 |
| 虚拟网络名称 | forti2vnet1 |
| VNET 地址空间 | 172.17.0.0/16* |
| 公共 VNET 子网名称 | forti2-PublicFacingSubnet |
| 公共 VNET 地址前缀 | 172.17.0.0/24* |
| 内部 VNET 子网名称 | Forti2-InsideSubnet |
| 内部 VNET 子网前缀 | 172.17.1.0/24* |
| FortiGate NVA 的 VM 大小 | 标准 F2s_v2 |
| 公共 IP 地址名称 | Forti2-publicip1 |
| 公共 IP 地址类型 | 静态 |

> [!Note]
> \* 如果上述设置与本地网络环境存在任何重叠情况（包括任一 Azure Stack Hub 的 VIP 池），请选择一组不同的地址空间和子网前缀。 另请确保地址范围不相互重叠。

## <a name="deploy-the-fortigate-ngfw"></a>部署 FortiGate NGFW

1.  打开 Azure Stack Hub 用户门户。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image5.png)

2.  选择“创建资源”，然后搜索 `FortiGate`。 

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image6.png)

3.  依次选择“FortiGate NGFW”、“创建”。  

4.  使用[部署参数](#deployment-parameters)表格中的参数填写“基本信息”。 

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image7.png)

5.  选择“确定”  。

6.  使用[部署参数](#deployment-parameters)表格提供“虚拟网络”、“子网”和“VM 大小”详细信息。

    > [!Warning] 
    > 如果本地网络与 IP 范围 `172.16.0.0/16` 重叠，则必须选择并设置不同的网络范围和子网。 若要使用与[部署参数](#deployment-parameters)表格中不同的名称和范围，请使用**不**与本地网络冲突的参数。 在 VNET 中设置 VNET IP 范围和子网范围时，请多加留意。 范围不应与本地网络中存在的 IP 范围重叠。

7.  选择“确定”  。

8.  为 FortiGate NVA 配置公共 IP：

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image8.png)

9.  选择“确定”  。 再选择“确定”。 

10.  选择“创建”  。

完成部署大约需要 10 分钟。

## <a name="configure-routes-udrs-for-each-vnet"></a>配置每个 VNET 的路由 (UDR)

对 forti1-rg1 和 forti2-rg1 这两个部署执行以下步骤。

1. 打开 Azure Stack Hub 用户门户。

2. 选择资源组。 在筛选器中键入 `forti1-rg1`，然后双击“forti1-rg1”资源组。

    ![资源组](./media/azure-stack-network-howto-vnet-to-onprem/image9.png)

2. 选择“forti1-forti1-InsideSubnet-routes-xxxx”资源。 

3. 在“设置”下选择“路由”。  

    ![路由](./media/azure-stack-network-howto-vnet-to-onprem/image10.png)

4. 删除“to-Internet”路由。 

    ![to-Internet](./media/azure-stack-network-howto-vnet-to-onprem/image11.png)

5. 请选择“是”。 

6. 选择“添加”以添加新路由。 

7. 将路由命名为 `to-onprem`。

8. 输入 IP 网络范围，用于定义 VPN 所要连接到的本地网络的网络范围。

9. 对于“下一跃点类型”，请选择“虚拟设备”；选择 `172.16.1.4`。   如果你的 IP 范围与此不同，请使用自己的 IP 范围。

    ![下一跃点类型](./media/azure-stack-network-howto-vnet-to-onprem/image12.png)

10. 选择“保存”  。

需要使用 Fortinet 提供的有效许可证文件来激活每个 FortiGate NVA。 在激活每个 NVA 之前，NVA **无法**正常运行。 有关如何获取许可证文件和 NVA 激活步骤的详细信息，请参阅 Fortinet 文档库文章[注册和下载许可证](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)。

需要两个许可证文件 – 每个 NVA 各需一个。

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>在两个 NVA 之间创建 IPSec VPN

激活 NVA 之后，遵循以下步骤在两个 NVA 之间创建 IPSec VPN。

对 forti1 NVA 和 forti2 NVA，请执行以下步骤：

1.  导航到 fortiX VM 的“概览”页，获取分配的公共 IP 地址：

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image13.png)

2.  复制分配的 IP 地址，打开浏览器，然后将该地址粘贴到地址栏中。 浏览器可能会警告安全证书不受信任。 请继续操作。

4.  输入在部署期间提供的 FortiGate 管理用户名和密码。

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image14.png)

5.  选择“系统” > “固件”。  

6.  选中显示最新固件的框，例如 `FortiOS v6.2.0 build0866`。

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image15.png)

7.  选择“备份配置并升级” > “继续”。  

8.  NVA 会将其固件更新到最新内部版本，然后重新启动。 此过程大约需要五分钟时间。 重新登录到 FortiGate Web 控制台。

10.  单击“VPN” > “IPSec 向导”。  

11. 在“VPN 创建向导”中输入 VPN 的名称，例如 `conn1`。 

12. 选择“此站点位于 NAT 后”。 

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image16.png)

13. 选择“**下一步**”。

14. 输入要连接到的本地 VPN 设备的远程 IP 地址。

15. 选择“port1”作为“传出接口”。  

16. 选择“预共享密钥”，输入（并记下）一个预共享密钥。  

    > [!Note]  
    > 稍后需要使用此密钥来设置本地 VPN 设备上的连接，即，密钥必须完全匹配。 

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image17.png)

17. 选择“**下一步**”。

18. 对于“本地接口”，请选择“port2”。  

19. 输入本地子网范围：
    - forti1：172.16.0.0/16
    - forti2：172.17.0.0/16

    如果你的 IP 范围与此不同，请使用自己的 IP 范围。

20. 输入代表本地网络的相应远程子网，你将通过本地 VPN 设备连接到此网络。
    - forti1：172.16.0.0/16
    - forti2：172.17.0.0/16

    如果你的 IP 范围与此不同，请使用自己的 IP 范围。

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image18.png)

21. 选择“创建” 

22. 选择“网络” > “接口”。  

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image19.png)

23. 双击“port2”。 

24. 在“角色”列表中选择“LAN”，选择“DHCP”作为寻址模式。   

25. 选择“确定”  。

对另一个 NVA 重复上述步骤。

## <a name="bring-up-all-phase-2-selectors"></a>启动所有阶段 2 选择器 

对*两个* NVA 完成上述步骤后：

1.  在 forti2 FortiGate Web 控制台上，选择“监视” > “IPsec 监视器”。   

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image20.png)

2.  突出显示 `conn1`，选择“启动” > “所有的阶段 2 选择器”。  

    ![](./media/azure-stack-network-howto-vnet-to-vnet/image21.png)

## <a name="test-and-validate-connectivity"></a>测试并验证连接

现在，应该可以通过 FortiGate NVA 在每个 VNET 之间进行路由。 若要验证连接，请在每个 VNET 的 InsideSubnet 中创建一个 Azure Stack Hub VM。 可以通过门户、CLI 或 PowerShell 创建 Azure Stack Hub VM。 创建 VM 时：

-   Azure Stack Hub VM 放在每个 VNET 的 **InsideSubnet** 上。

-   创建 VM 时，请**不要**将任何 NSG 应用到该 VM（即，如果从门户创建 VM，请删除默认添加的 NSG）。

-   确保 VM 防火墙规则允许用来测试连接的通信。 出于测试目的，建议在 OS 中完全禁用防火墙（如果可能）。

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 网络的差异和注意事项](azure-stack-network-differences.md)  
[使用 Fortinet FortiGate 在 Azure Stack Hub 中提供网络解决方案](../operator/azure-stack-network-solutions-enable.md)  