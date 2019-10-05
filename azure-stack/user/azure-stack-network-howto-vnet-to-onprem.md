---
title: 为 Azure Stack 设置 VPN 网关 |Microsoft Docs
description: 了解如何为 Azure Stack 设置 VPN 网关。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/03/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: 722cd99a53a0c08e7b981a571726b378e54cd288
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2019
ms.locfileid: "71962371"
---
# <a name="setup-vpn-gateway-for-azure-stack-using-fortigate-nva"></a>使用 FortiGate NVA 设置 Azure Stack 的 VPN 网关

本文介绍如何创建与 Azure Stack 的 VPN 连接。 VPN 网关是一种虚拟网络网关，可在 Azure Stack 中的虚拟网络与远程 VPN 网关之间发送加密流量。 以下过程使用资源组中的 FortiGate NVA （网络虚拟设备）部署一个 VNET。 它还提供了在 FortiGate NVA 上设置 IPSec VPN 的步骤。

## <a name="prerequisites"></a>先决条件

-  访问具有可用容量的 Azure Stack 集成系统，以部署此解决方案所需的计算、网络和资源要求。 

    > [!Note]  
    > 由于 ASDK 中的网络 limitions，这些说明**不适**用于 AZURE STACK 开发工具包（ASDK）。 有关详细信息，请参阅[ASDK 要求和注意事项](https://docs.microsoft.com/azure-stack/asdk/asdk-deploy-considerations)。

-  在本地网络中访问承载 Azure Stack 集成系统的 VPN 设备。 设备需要创建 IPSec 隧道，该隧道符合[部署参数](#deployment-parameters)中所述的参数。

-  Azure Stack Marketplace 中提供的网络虚拟设备（NVA）解决方案。 NVA 控制从外围网络到其他网络或子网的网络流量。 此过程使用[Fortinet FortiGate 下一代防火墙单一 VM 解决方案](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm)。

    > [!Note]  
    > 如果你的 Azure Stack Marketplace 中没有适用于 Azure BYOL 和**FORTIGATE NGFW-单 Vm 部署（BYOL）** 的**FORTINET FortiGate-vm** ，请联系你的云操作员。

-  若要激活 FortiGate NVA，你将需要至少一个可用的 FortiGate 许可证文件。 有关如何获取这些许可证的信息，请参阅 Fortinet 文档库文章[注册和下载许可证](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)。

    此过程使用[单个 FortiGate 部署](ttps://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment)。 可以在本地网络中找到有关如何将 FortiGate NVA 连接到 Azure Stack VNET 的步骤。

    有关如何在主动-被动（HA）设置中部署 FortiGate 解决方案的详细信息，请参阅 Fortinet 文档库[中的详细信息。](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure)

## <a name="deployment-parameters"></a>部署参数

下表汇总了这些部署中用于引用的参数。

| 参数 | ReplTest1 |
|-----------------------------------|---------------------------|
| FortiGate 实例名称 | forti1 |
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
| 公共 IP 地址类型 | Static |

> [!Note]
> \* 如果 `172.16.0.0/16` 与本地网络或 Azure Stack VIP 池重叠，请选择其他地址空间和子网前缀。

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>部署 FortiGate NGFW Marketplace 项

1. 打开 Azure Stack 用户门户。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image5.png)

1. 选择 "**创建资源**" 并搜索 `FortiGate` "。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image6.png)

2. 选择**FORTIGATE NGFW** ，然后选择 "**创建**"。

3. 使用 "[部署参数](#deployment-parameters)" 表中的参数完成**基本**操作。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image7.png)

1. 选择“确定”。

2. 使用 "[部署参数](#deployment-parameters)" 表提供 "虚拟网络"、"子网" 和 "VM 大小" 的详细信息。

    > [!Warning] 
    > 如果本地网络与 IP 范围 `172.16.0.0/16` 重叠，则必须选择并设置其他网络范围和子网。 如果要使用不同于 "[部署参数](#deployment-parameters)" 表中的名称和范围，请使用**不**会与本地网络冲突的参数。 在 VNET 中设置 VNET IP 范围和子网范围时请小心。 不希望范围与本地网络中存在的 IP 范围重叠。

3. 选择“确定”。

4. 为 Fortigate NVA 配置公共 IP：

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image8.png)

5. 选择“确定”。 再选择“确定”。

6. 选择“创建”。

    部署需要大约10分钟。

## <a name="configure-routes-udr-for-the-vnet"></a>为 VNET 配置路由（UDR）

1. 打开 Azure Stack 用户门户。

2. 选择 "资源组"。 在筛选器中键入 `forti1-rg1`，然后双击 "forti1-rg1" 资源组。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image9.png)

2. 选择 "forti1-forti1-InsideSubnet" 资源。

3. 选择 "**设置**" 下的 "**路由**"。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image10.png)

4. 删除**到 Internet**路由。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image11.png)

5. 请选择“是”。

6. 选择 "**添加**" 以添加新路由。

7. 将路由命名 `to-onprem`。

8. 输入定义 VPN 将连接到的本地网络的网络范围的 IP 网络范围。

9. 选择 "**虚拟设备**" 作为**下一跃点类型**和 `172.16.1.4`。 如果要使用不同的 IP 范围，请使用 IP 范围。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image12.png)

10. 选择“保存”。

## <a name="activate-the-fortigate-nva"></a>激活 FortiGate NVA

激活 FortiGate NVA 并在每个 NVA 上设置 IPSec VPN 连接。

若要激活每个 FortiGate NVA，将需要 Fortinet 中的有效许可证文件。 在激活每个 NVA 之前，Nva 将**不**起作用。 有关如何获取许可证文件和激活 NVA 的步骤的详细信息，请参阅 Fortinet 文档库文章[注册和下载许可证](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)。

激活 Nva 后，请在 NVA 上创建 IPSec VPN 隧道。

1. 打开 Azure Stack 用户门户。

2. 选择 "资源组"。 在筛选器中输入 `forti1`，然后双击 "forti1" 资源组。

3. 在资源组边栏选项卡的资源类型列表中，双击 " **forti1** " 虚拟机。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image13.png)

4. 复制分配的 IP 地址，打开浏览器，然后将 IP 地址粘贴到地址栏中。 该站点可能会触发安全证书不受信任的警告。 仍继续。

5. 输入在部署期间提供的 FortiGate 管理用户名和密码。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image14.png)

6. 选择**System**@no__t 1**固件**。

7. 选择显示最新固件的框，例如 `FortiOS v6.2.0 build0866`。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image15.png)

8. 选择**备份配置并升级** > **继续**。

9. NVA 会将其固件更新到最新版本，然后重新启动。 此过程大约需五分钟。 重新登录到 FortiGate web 控制台。

10. 单击 " **VPN** > **IPSec 向导**"。

11. 输入 VPN 的名称，例如，在**Vpn 创建向导**中 `conn1`。

12. 选择**此站点位于 NAT 后面**。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image16.png)

13. 选择“**下一步**”。

14. 输入要连接到的本地 VPN 设备的远程 IP 地址。

15. 选择**port1**作为**传出接口**。

16. 选择 "**预共享密钥**"，然后输入（并记录）预共享密钥。 

    > [!Note]  
    > 你将需要此密钥来设置本地 VPN 设备上的连接，即，它们必须*完全*匹配。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image17.png)

17. 选择“**下一步**”。

18. 选择 " **port2** " 作为**本地接口**。

19. 输入本地子网范围：
    - forti1:172.16.0.0/16
    - forti2:172.17.0.0/16

    如果使用不同的 IP 范围，请使用 IP 范围。

20. 输入代表本地网络的适当远程子网，你将通过本地 VPN 设备连接到该网络。

    [](./media/azure-stack-network-howto-vnet-to-onprem/image18.png)

21. 选择“创建”

22. 选择 "**网络** > "**接口**。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image19.png)

23. 双击 " **port2**"。

24. 在**角色**列表中选择 " **LAN** "，并选择 " **DHCP** " 作为寻址模式。

25. 选择“确定”。

## <a name="configure-the-on-premises-vpn"></a>配置本地 VPN

必须配置本地 VPN 设备以创建 IPSec VPN 隧道。 下表提供了设置本地 VPN 设备时需要的参数。 有关如何配置本地 VPN 设备的信息，请参阅 tp 的设备文档。

| 参数 | ReplTest1 |
| --- | --- |
| 远程网关 IP | 分配给 forti1 的公共 IP 地址–请参阅[激活 FORTIGATE NVA](#activate-the-fortigate-nva)。 |
| 远程 IP 网络 | 172.16.0.0/16 （如果对 VNET 使用这些说明中的 IP 范围）。 |
| 验证.方法 = 预共享密钥（PSK） | 从步骤16开始。
| SDK 版本 | 1 |
| IKE 模式 | Main （ID 保护） |
| 阶段1提议算法 | AES128、AES256、AES128、AES256-SHA1 |
| Diffe-Diffie-hellman 组 | 14、5 |

## <a name="create-the-vpn-tunnel"></a>创建 VPN 隧道

正确配置本地 VPN 设备后，即可建立 VPN 隧道。

从 FortiGate NVA：

1. 在 forti1 FortiGate web 控制台上，中转到**Monitor**@no__t "监视 **"。**

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image20.png)

2. 突出显示 " **conn1** "，然后选择 "**打开** > **所有阶段 2" 选择器**。

    ![](./media/azure-stack-network-howto-vnet-to-onprem/image21.png)

## <a name="test-and-validate-connectivity"></a>测试和验证连接

可以通过本地 VPN 设备在 VNET 网络与本地网络之间进行路由。

验证连接：

1. 在 Azure Stack Vnet 和本地网络上的系统中创建 VM。 可以按照 [Quickstart 创建 VM 的说明进行操作：使用 Azure Stack 门户 @ no__t 创建 Windows server VM。

2. 创建 Azure Stack VM 并准备本地系统时，请检查：

-  Azure Stack VM 位于 VNET 的**InsideSubnet**上。

-  本地系统位于定义的 IP 范围内的本地网络中，如 IPSec 配置中所定义。 还要确保本地 VPN 设备的本地接口 IP 地址提供给本地系统，作为可以访问 Azure Stack VNET 网络的路由，例如 `172.16.0.0/16`。

-  创建时 **，不要将任何 nsg 应用到**Azure Stack VM。 如果通过门户创建 VM，则可能需要删除默认情况下添加的 NSG。

-  确保本地系统 OS 和 Azure Stack VM 操作系统没有可用于测试连接的操作系统防火墙规则，这些规则将禁止使用通信。 出于测试目的，建议完全在两个系统的操作系统内禁用防火墙。

## <a name="next-steps"></a>后续步骤

[Azure Stack 网络的差异和注意事项](azure-stack-network-differences.md)  
[通过 Fortinet FortiGate 在 Azure Stack 中提供网络解决方案](../operator/azure-stack-network-solutions-enable.md)  