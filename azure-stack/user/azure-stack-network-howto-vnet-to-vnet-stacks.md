---
title: 使用 Fortinet FortiGate NVA 建立 Azure Stack Hub VNET 到 VNET 连接
description: 了解如何使用 Fortinet FortiGate NVA 在 Azure Stack Hub 中建立 VNET 到 VNET 连接
author: mattbriggs
ms.topic: how-to
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: d8c20d3fe7b80a7ace90422a622c4f067f631954
ms.sourcegitcommit: b50dd116d6d1f89d42bd35ad0f85bb25c5192921
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2020
ms.locfileid: "94547103"
---
# <a name="vnet-to-vnet-connectivity-between-azure-stack-hub-instances-with-fortinet-fortigate-nva"></a>与 Fortinet FortiGate NVA Azure Stack 集线器实例之间的 VNet 到 VNet 连接

本文介绍了如何使用 Fortinet FortiGate NVA（网络虚拟设备）将一个 Azure Stack Hub 中的 VNET 连接到另一个 Azure Stack Hub 中的 VNET。

本文介绍如何应对当前的 Azure Stack Hub 限制，该限制使租户在两个环境之间只能设置一个 VPN 连接。 用户将了解如何在 Linux 虚拟机上设置自定义网关，以便在不同的 Azure Stack Hub 之间建立多个 VPN 连接。 本文中的过程将在每个 VNET 中部署两个具有 FortiGate NVA 的 VNET：每个 Azure Stack Hub 环境各部署一个。 此外，其中详细说明了在这两个 VNET 之间设置 IPSec VPN 所要做出的更改。 对于每个 Azure Stack Hub 中的每个 VNET，应该重复本文中的步骤。 

## <a name="prerequisites"></a>先决条件

-  有权访问可提供足够容量用于部署此解决方案所需的计算、网络和资源的 Azure Stack Hub 集成系统。 

    > [!NOTE]  
    > 由于 ASDK 的网络限制，本文中的说明 **不** 适用于 Azure Stack 开发工具包 (ASDK)。 有关详细信息，请参阅 [ASDK 的要求和注意事项](../asdk/asdk-deploy-considerations.md)。

-  已下载网络虚拟设备 (NVA) 解决方案并将其发布到 Azure Stack Hub 市场。 NVA 控制从外围网络到其他网络或子网的网络流量。 此过程使用 [Fortinet FortiGate 下一代防火墙单一 VM 解决方案](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm)。

-  至少有两个可用于激活 FortiGate NVA 的 FortiGate 许可证文件。 有关如何获取这些许可证的信息，请参阅 Fortinet 文档库文章[注册和下载许可证](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)。

    此过程使用[单一 FortiGate-VM 部署](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment)。 其中提供了在本地网络中将 FortiGate NVA 连接到 Azure Stack Hub VNET 的步骤。

    有关如何在主动-被动 (HA) 设置中部署 FortiGate 解决方案的详细信息，请参阅 Fortinet 文档库文章 [Azure 上的 FortiGate-VM 的 HA](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure)。

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

> [!NOTE]
> \* 如果上述设置与本地网络环境存在任何重叠情况（包括任一 Azure Stack Hub 的 VIP 池），请选择一组不同的地址空间和子网前缀。 另请确保地址范围不相互重叠。**

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>部署 FortiGate NGFW 市场项

对两个 Azure Stack Hub 环境重复这些步骤。 

1. 打开 Azure Stack Hub 用户门户。 请务必使用至少拥有订阅“参与者”权限的凭据。

1. 选择“创建资源”，然后搜索 `FortiGate`。

    ![该屏幕截图显示在搜索“fortigate”后返回的单行结果。 找到的项的名称是“FortiGate NGFW - 单 VM 部署 (BYOL)”。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image6.png)

1. 依次选择“FortiGate NGFW”、“创建”。

1. 使用[部署参数](#deployment-parameters)表格中的参数填写“基本信息”。

    窗体中应包含以下信息：

    ![已使用部署表中的值填充了“基本信息”对话框的文本框（如“实例名称”和“BYOL 许可证”）。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image7.png)

1. 选择“确定”  。

1. 提供[部署参数](#deployment-parameters)中的虚拟网络、子网和 VM 大小详细信息。

    若要使用不同的名称和范围，请小心不要使用与其他 Azure Stack Hub 环境中的其他 VNET 和 FortiGate 资源冲突的参数。 在 VNET 中设置 VNET IP 范围和子网范围时，请特别留意。 请检查它们是否不与创建的其他 VNET 的 IP 范围重叠。

1. 选择“确定”  。

1. 配置 FortiGate NVA 要使用的公共 IP：

    ![“IP 分配”对话框的“公共 IP 地址名称”文本框中显示的值为“forti1-publicip1”（来自部署表）。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image8.png)

1. 选择“确定”，然后再次选择“确定”。

1. 选择“创建” 。

完成部署大约需要 10 分钟。 现在可以重复上述步骤，以在另一 Azure Stack Hub 环境中创建另一个 FortiGate NVA 和 VNET 部署。

## <a name="configure-routes-udrs-for-each-vnet"></a>配置每个 VNET 的路由 (UDR)

对 forti1-rg1 和 forti2-rg1 这两个部署执行以下步骤。

1. 在 Azure Stack Hub 门户中导航到“forti1-rg1”资源组。

    ![这是 forti1-rg1 资源组中资源列表的屏幕截图。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image9.png)

2. 选择“forti1-forti1-InsideSubnet-routes-xxxx”资源。

3. 在“设置”下选择“路由”。 

    ![该屏幕截图显示了“设置”下已突出显示的“路由”项。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image10.png)

4. 删除“to-Internet”路由。

    ![该屏幕截图显示了突出显示的“to-Internet”路由。 有一个“删除”按钮。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image11.png)

5. 请选择“是”。 

6. 选择 **添加** 。

7. 将 **路由命名为** `to-forti1` 或 `to-forti2`。 如果你的 IP 范围与此不同，请使用自己的 IP 范围。

8. 输入：
    - forti1：`172.17.0.0/16`  
    - forti2：`172.16.0.0/16`  

    如果你的 IP 范围与此不同，请使用自己的 IP 范围。

9. 对于“下一跃点类型”，请选择“虚拟设备”。
    - forti1：`172.16.1.4`  
    - forti2：`172.17.0.4`  

    如果你的 IP 范围与此不同，请使用自己的 IP 范围。

    ![to-forti2 的“编辑路由”对话框中包含带有值的文本框。 “地址前缀”为 172.17.0.0/16，“下一跃点类型”为“虚拟设备”，“下一跃点地址”为 172.16.1.4。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image12.png)

10. 选择“保存”。

对每个资源组的每个 **InsideSubnet** 路由重复上述步骤。

## <a name="activate-the-fortigate-nvas-and-configure-an-ipsec-vpn-connection-on-each-nva"></a>激活 FortiGate NVA，并在每个 NVA 上配置 IPSec VPN 连接

 需要使用 Fortinet 提供的有效许可证文件来激活每个 FortiGate NVA。 在激活每个 NVA 之前，NVA **无法** 正常运行。 有关如何获取许可证文件和 NVA 激活步骤的详细信息，请参阅 Fortinet 文档库文章[注册和下载许可证](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)。

需要两个许可证文件 – 每个 NVA 各需一个。

## <a name="create-an-ipsec-vpn-between-the-two-nvas"></a>在两个 NVA 之间创建 IPSec VPN

激活 NVA 之后，遵循以下步骤在两个 NVA 之间创建 IPSec VPN。

对 forti1 NVA 和 forti2 NVA，请执行以下步骤：

1. 导航到 fortiX VM 的“概述”页，获取分配的公共 IP 地址：

    ![Forti1 的“概述”页显示了资源组、状态等。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image13.png)

1. 复制分配的 IP 地址，打开浏览器，然后将该地址粘贴到地址栏中。 浏览器可能会警告安全证书不受信任。 请继续操作。

1. 输入在部署期间提供的 FortiGate 管理用户名和密码。

    ![该屏幕截图是登录屏幕的屏幕截图，此登录屏幕中包含“登录”按钮以及用户名与密码的文本框。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image14.png)

1. 选择“系统” > “固件”。 

1. 选中显示最新固件的框，例如 `FortiOS v6.2.0 build0866`。

    ![“FortiOS v 6.2.0 build0866”固件的屏幕截图中有发行说明的链接，以及两个按钮：“备份配置并升级”和“升级”。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image15.png)

1. 选择“备份配置并升级”，并在出现提示时选择“继续”。

1. NVA 会将其固件更新到最新内部版本，然后重新启动。 此过程大约需要五分钟时间。 重新登录到 FortiGate Web 控制台。

1. 单击“VPN” > “IPSec 向导”。 

1. 在“VPN 创建向导”中输入 VPN 的名称，例如 `conn1`。

1. 选择“此站点位于 NAT 后”。

    ![“VPN 创建向导”的屏幕截图显示它处于第一步（“VPN 设置”）。 已选择以下值：为“模板类型”选择了“站点到站点”，为“远程设备类型”选择了“FortiGate”，并且为“NAT 配置”选择了“此站点位于 NAT 后”。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image16.png)

1. 选择“**下一页**”。

1. 输入要连接到的本地 VPN 设备的远程 IP 地址。

1. 选择“port1”作为“传出接口”。

1. 选择“预共享密钥”，输入（并记下）一个预共享密钥。 

    > [!NOTE]  
    > 稍后需要使用此密钥来设置本地 VPN 设备上的连接，即，密钥必须完全匹配。

    ![“VPN 创建向导”的屏幕截图显示它处于第二步（“身份验证”），并且所选值已突出显示。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image17.png)

1. 选择“**下一页**”。

1. 对于“本地接口”，请选择“port2”。

1. 输入本地子网范围：
    - forti1：172.16.0.0/16
    - forti2：172.17.0.0/16

    如果你的 IP 范围与此不同，请使用自己的 IP 范围。

1. 输入代表本地网络的相应远程子网，你将通过本地 VPN 设备连接到此网络。
    - forti1：172.16.0.0/16
    - forti2：172.17.0.0/16

    如果你的 IP 范围与此不同，请使用自己的 IP 范围。

    ![“VPN 创建向导”的屏幕截图显示它处于第三步（“策略和路由”），并显示了已选择的值和已输入的值。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image18.png)

1. 选择“创建”

1. 选择“网络” > “接口”。   

    ![“接口”列表显示两个接口：已经过配置的 port1 和未经过配置的 port2。 有一些用于创建、编辑和删除接口的按钮。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image19.png)

1. 双击“port2”。

1. 在“角色”列表中选择“LAN”，选择“DHCP”作为寻址模式。

1. 选择“确定”  。

对另一个 NVA 重复上述步骤。


## <a name="bring-up-all-phase-2-selectors"></a>启动所有阶段 2 选择器 

对 **两个** NVA 完成上述步骤后：

1.  在 forti2 FortiGate Web 控制台上，选择“监视” > “IPsec 监视器”。  

    ![已列出 VPN 连接 conn1 的监视器。 它显示为正在关闭，与对应的“阶段 2 选择器”一样。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image20.png)

2.  突出显示 `conn1`，选择“启动” > “所有的阶段 2 选择器”。 

    ![监视器和“阶段 2 选择器”都显示为已启动。](./media/azure-stack-network-howto-vnet-to-vnet-stacks/image21.png)


## <a name="test-and-validate-connectivity"></a>测试并验证连接

现在，应该可以通过 FortiGate NVA 在每个 VNET 之间进行路由。 若要验证连接，请在每个 VNET 的 InsideSubnet 中创建一个 Azure Stack Hub VM。 可以通过门户、CLI 或 PowerShell 创建 Azure Stack Hub VM。 创建 VM 时：

-   Azure Stack Hub VM 放在每个 VNET 的 **InsideSubnet** 上。

-   创建 VM 时，请 **不要** 将任何 NSG 应用到该 VM（即，如果从门户创建 VM，请删除默认添加的 NSG）。

-   确保 VM 防火墙规则允许用来测试连接的通信。 出于测试目的，建议在 OS 中完全禁用防火墙（如果可能）。

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 网络的差异和注意事项](azure-stack-network-differences.md)  
[使用 Fortinet FortiGate 在 Azure Stack Hub 中提供网络解决方案](../operator/azure-stack-network-solutions-enable.md)  
