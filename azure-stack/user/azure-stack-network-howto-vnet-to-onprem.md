---
title: 为 Azure Stack Hub 设置 VPN 网关
description: 了解如何为 Azure Stack Hub 设置 VPN 网关。
author: mattbriggs
ms.topic: how-to
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/03/2019
ms.openlocfilehash: 75138fa2b74666fee4149bf444558c98c250b4c8
ms.sourcegitcommit: 9557a5029cf329599f5b523c68e8305b876108d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2020
ms.locfileid: "88965154"
---
# <a name="set-up-vpn-gateway-for-azure-stack-hub-using-fortigate-nva"></a>使用 FortiGate NVA 为 Azure Stack Hub 设置 VPN 网关

本文介绍如何与 Azure Stack Hub 建立 VPN 连接。 VPN 网关是一种虚拟网络网关，可在 Azure Stack Hub 中的虚拟网络与远程 VPN 网关之间发送加密流量。 以下过程在资源组中部署一个具有 FortiGate NVA（网络虚拟设备）的 VNET。 此外，还提供在 FortiGate NVA 上设置 IPSec VPN 的步骤。

## <a name="prerequisites"></a>先决条件

-  有权访问可提供足够容量用于部署此解决方案所需的计算、网络和资源的 Azure Stack Hub 集成系统。 

    > [!NOTE]  
    > 由于 ASDK 的网络限制，本文中的说明**不**适用于 Azure Stack 开发工具包 (ASDK)。 有关详细信息，请参阅 [ASDK 的要求和注意事项](../asdk/asdk-deploy-considerations.md)。

-  有权访问本地网络中的托管 Azure Stack Hub 集成系统的 VPN 设备。 该设备需要根据[部署参数](#deployment-parameters)中所述的参数创建 IPSec 隧道。

-  Azure Stack Hub 市场中已提供网络虚拟设备 (NVA) 解决方案。 NVA 控制从外围网络到其他网络或子网的网络流量。 此过程使用 [Fortinet FortiGate 下一代防火墙单一 VM 解决方案](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm)。

    > [!NOTE]  
    > 如果 Azure Stack Hub 市场中没有**适用于 Azure BYOL 的 Fortinet FortiGate-VM** 和 **FortiGate NGFW - 单一 VM 部署 (BYOL)** ，请联系云操作员。

-  若要激活 FortiGate NVA，至少需要一个可用的 FortiGate 许可证文件。 有关如何获取这些许可证的信息，请参阅 Fortinet 文档库文章[注册和下载许可证](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)。

    此过程使用[单一 FortiGate-VM 部署](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/632940/single-FortiGate-vm-deployment)。 其中提供了在本地网络中将 FortiGate NVA 连接到 Azure Stack Hub VNET 的步骤。

    有关如何在主动-被动 (HA) 设置中部署 FortiGate 解决方案的详细信息，请参阅 Fortinet 文档库文章 [Azure 上的 FortiGate-VM 的 HA](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/983245/ha-for-FortiGate-vm-on-azure) 中的详细信息。

## <a name="deployment-parameters"></a>部署参数

下表汇总了在这些部署中使用的参数供用户参考。

| 参数 | Value |
|-----------------------------------|---------------------------|
| FortiGate 实例名称 | forti1 |
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

> [!NOTE]
> \* 如果 `172.16.0.0/16` 与本地网络或 Azure Stack Hub VIP 池重叠，请选择不同的地址空间和子网前缀。

## <a name="deploy-the-fortigate-ngfw-marketplace-items"></a>部署 FortiGate NGFW 市场项

1. 打开 Azure Stack Hub 用户门户。

    ![主屏幕将显示，并在左窗格中选择 "+ 创建资源"。](./media/azure-stack-network-howto-vnet-to-onprem/image5.png)

1. 选择“创建资源”，然后搜索 `FortiGate`。****

    ![搜索结果列表显示 FortiGate NGFW-单 VM 部署。](./media/azure-stack-network-howto-vnet-to-onprem/image6.png)

1. 依次选择“FortiGate NGFW”、“创建”。********

1. 使用[部署参数](#deployment-parameters)表格中的参数填写“基本信息”。****

    !["基本信息" 屏幕具有 "列表" 和 "文本框" 中输入的 "部署参数" 表中的值。](./media/azure-stack-network-howto-vnet-to-onprem/image7.png)

1. 选择“确定”  。

1. 使用[部署参数](#deployment-parameters)表格提供“虚拟网络”、“子网”和“VM 大小”详细信息。

    > [!Warning] 
    > 如果本地网络与 IP 范围 `172.16.0.0/16` 重叠，则必须选择并设置不同的网络范围和子网。 若要使用与[部署参数](#deployment-parameters)表格中不同的名称和范围，请使用**不**与本地网络冲突的参数。 在 VNET 中设置 VNET IP 范围和子网范围时，请多加留意。 范围不应与本地网络中存在的 IP 范围重叠。

1. 选择“确定”  。

1. 为 FortiGate NVA 配置公共 IP：

    ![IP 分配对话框显示 "公共 IP 地址名称" 的值 forti1-publicip1 和 "公共 IP 地址类型" 的静态值。](./media/azure-stack-network-howto-vnet-to-onprem/image8.png)

1. 选择“确定”  。 再选择“确定”。****

1. 选择“创建”。

    完成部署大约需要 10 分钟。

## <a name="configure-routes-udr-for-the-vnet"></a>为 VNET 配置路由 (UDR)

1. 打开 Azure Stack Hub 用户门户。

2. 选择资源组。 在筛选器中键入 `forti1-rg1`，然后双击“forti1-rg1”资源组。

    ![为 forti1-rg1 资源组列出了10个资源。](./media/azure-stack-network-howto-vnet-to-onprem/image9.png)

2. 选择“forti1-forti1-InsideSubnet-routes-xxxx”资源。

3. 选择 "**设置**" 下的 "**路由**"。

    !["路由" 按钮在 "设置" 对话框中处于选中状态。](./media/azure-stack-network-howto-vnet-to-onprem/image10.png)

4. 删除“to-Internet”路由。****

    !["到 Internet" 路由是唯一列出的路由，并且处于选中状态。 有一个删除按钮。](./media/azure-stack-network-howto-vnet-to-onprem/image11.png)

5. 请选择“是”。

6. 选择“添加”以添加新路由。****

7. 将路由命名为 `to-onprem`。

8. 输入 IP 网络范围，用于定义 VPN 所要连接到的本地网络的网络范围。

9. 对于“下一跃点类型”，请选择“虚拟设备”；选择 `172.16.1.4`。******** 如果你的 IP 范围与此不同，请使用自己的 IP 范围。

    !["添加路由" 对话框显示已在文本框中输入的四个值。](./media/azure-stack-network-howto-vnet-to-onprem/image12.png)

10. 选择“保存”。

## <a name="activate-the-fortigate-nva"></a>激活 FortiGate NVA

激活 FortiGate NVA，并在每个 NVA 上设置 IPSec VPN 连接。

若要激活每个 FortiGate NVA，需要 Fortinet 提供的有效许可证文件。 在激活每个 NVA 之前，NVA **无法**正常运行。 有关如何获取许可证文件和 NVA 激活步骤的详细信息，请参阅 Fortinet 文档库文章[注册和下载许可证](https://docs2.fortinet.com/vm/azure/FortiGate/6.2/azure-cookbook/6.2.0/19071/registering-and-downloading-your-license)。

激活 NVA 之后，在 NVA 上创建 IPSec VPN 隧道。

1. 打开 Azure Stack Hub 用户门户。

2. 选择资源组。 在筛选器中输入 `forti1`，然后双击“forti1”资源组。

3. 在资源组边栏选项卡上的资源类型列表中，双击“forti1”虚拟机。****

    ![Forti1 虚拟机概述页显示 forti1 的值，例如 "资源组" 和状态。](./media/azure-stack-network-howto-vnet-to-onprem/image13.png)

4. 复制分配的 IP 地址，打开浏览器，然后将该 IP 地址粘贴到地址栏中。 站点可能会触发一条警告，指出安全证书不受信任。 请继续操作。

5. 输入在部署期间提供的 FortiGate 管理用户名和密码。

    ![登录对话框包含 "用户" 和 "密码" 文本框，以及一个 "登录" 按钮。](./media/azure-stack-network-howto-vnet-to-onprem/image14.png)

6. 选择**系统**  >  **固件**。

7. 选中显示最新固件的框，例如 `FortiOS v6.2.0 build0866`。

    ![固件对话框具有固件标识符 "FortiOS v 6.2.0 build0866"。 有一个指向发行说明和两个按钮的链接： "备份配置和升级" 和 "升级"。](./media/azure-stack-network-howto-vnet-to-onprem/image15.png)

8. 选择 "**备份配置"，然后**  >  **继续**升级。

9. NVA 会将其固件更新到最新内部版本，然后重新启动。 此过程大约需要五分钟时间。 重新登录到 FortiGate Web 控制台。

10. 单击 " **VPN**  >  **IPSec 向导**"。

11. 在“VPN 创建向导”中输入 VPN 的名称，例如 `conn1`。****

12. 选择“此站点位于 NAT 后”。****

    ![VPN 创建向导的屏幕截图显示在第一步 "VPN 设置"。 选择以下值： "站点到站点" （对于模板类型为 "FortiGate"）; 对于 NAT 配置，为 "此站点位于 NAT 后面"。](./media/azure-stack-network-howto-vnet-to-onprem/image16.png)

13. 选择“下一步”  。

14. 输入要连接到的本地 VPN 设备的远程 IP 地址。

15. 选择“port1”作为“传出接口”。********

16. 选择“预共享密钥”，输入（并记下）一个预共享密钥。**** 

    > [!NOTE]  
    > 稍后需要使用此密钥来设置本地 VPN 设备上的连接，即，密钥必须完全匹配。**

    ![VPN 创建向导的屏幕截图显示其在第二步 "身份验证" 中，并突出显示所选值。](./media/azure-stack-network-howto-vnet-to-onprem/image17.png)

17. 选择“下一步”  。

18. 对于“本地接口”，请选择“port2”。********

19. 输入本地子网范围：
    - forti1： 172.16.0.0/16
    - forti2： 172.17.0.0/16

    如果你的 IP 范围与此不同，请使用自己的 IP 范围。

20. 输入代表本地网络的相应远程子网，你将通过本地 VPN 设备连接到此网络。

    ![VPN 创建向导的屏幕截图显示在第三步 "策略 & 路由"。 它显示选定的和输入的值。](./media/azure-stack-network-howto-vnet-to-onprem/image18.png)

21. 选择“创建”

22. 选择 "**网络**  >  **接口**"。

    !["接口" 列表显示两个接口： port1，已配置，并且 port2 为 "未配置"。 有一些用于创建、编辑和删除接口的按钮。](./media/azure-stack-network-howto-vnet-to-onprem/image19.png)

23. 双击“port2”。****

24. 在“角色”列表中选择“LAN”，选择“DHCP”作为寻址模式。************

25. 选择“确定”  。

## <a name="configure-the-on-premises-vpn"></a>配置本地 VPN

必须设置本地 VPN 设备以创建 IPSec VPN 隧道。 下表提供了在设置本地 VPN 设备时所需的参数。 有关如何配置本地 VPN 设备的信息，请参阅设备的文档。

| 参数 | 值 |
| --- | --- |
| 远程网关 IP | 分配给 forti1 的公共 IP 地址 – 请参阅[激活 FortiGate NVA](#activate-the-fortigate-nva)。 |
| 远程 IP 网络 | 172.16.0.0/16（如果使用这些说明中为 VNET 提供的 IP 范围）。 |
| Auth. Method = 预共享密钥 (PSK)  | 来自步骤 16。
| SDK 版本 | 1 |
| IKE 模式 | 主要（ID 保护） |
| 阶段 1 提议算法 | AES128-SHA256、AES256-SHA256、AES128-SHA1、AES256-SHA1 |
| Diffie-Hellman 组 | 14、5 |

## <a name="create-the-vpn-tunnel"></a>创建 VPN 隧道

正确配置本地 VPN 设备后，可以建立 VPN 隧道。

在 FortiGate NVA 中：

1. 在 forti1 FortiGate web 控制台上，中转到 "**监视**  >  **IPsec 监视器**"。

    ![已列出 VPN 连接 conn1 的监视器。 它显示为 "已关闭"，与对应的 "阶段 2" 选择器相同。](./media/azure-stack-network-howto-vnet-to-onprem/image20.png)

2. 突出显示“conn1”，选择“启动” > “所有的阶段 2 选择器”。************

    !["监视器" 和 "阶段 2" 选择器都显示为 up。](./media/azure-stack-network-howto-vnet-to-onprem/image21.png)

## <a name="test-and-validate-connectivity"></a>测试并验证连接

可以通过本地 VPN 设备在 VNET 网络与本地网络之间进行路由。

若要验证连接：

1. 在 Azure Stack Hub Vnet 中创建一个 VM，并在本地网络中创建一个系统。 可以按照以下说明在 [快速入门：使用 Azure Stack 集线器门户创建 Windows SERVER VM](./azure-stack-quick-windows-portal.md)。

2. 创建 Azure Stack 中心 VM 并准备本地系统时，请检查：

-  Azure Stack 中心 VM 位于 VNET 的 **InsideSubnet** 上。

-  本地系统放置在 IPSec 配置中定义的 IP 范围内的本地网络上。 还要确保本地 VPN 设备的本地接口 IP 地址提供给本地系统，作为可以访问 Azure Stack 集线器 VNET 网络的路由，例如， `172.16.0.0/16` 。

-  创建时 **，不要将任何 nsg 应用到** AZURE STACK 中心 VM。 如果从门户创建 VM，可能需要删除默认添加的 NSG。

-  确保本地系统 OS 和 Azure Stack 中心 VM 操作系统没有可用于测试连接的操作系统防火墙规则，这些规则将禁止使用通信。 出于测试目的，建议在两个系统的操作系统中完全禁用防火墙。

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 网络的差异和注意事项](azure-stack-network-differences.md)  
[使用 Fortinet FortiGate 在 Azure Stack Hub 中提供网络解决方案](../operator/azure-stack-network-solutions-enable.md)  
