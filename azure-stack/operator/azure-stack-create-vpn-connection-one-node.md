---
title: 在不同 ASDK 环境中的两个虚拟网络之间创建站点到站点 VPN 连接
description: 用于在两个单节点 Azure Stack 开发工具包（ASDK）环境之间创建站点到站点 VPN 连接的云操作员教程。
author: justinha
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 01/22/2020
ms.openlocfilehash: fcfd453cf6ccbbbc8f5e65b9db475ab4fdad33b0
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78366298"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-asdk-environments"></a>在不同 ASDK 环境中的两个虚拟网络之间创建站点到站点 VPN 连接

## <a name="overview"></a>概述

本文介绍如何在两个单独的 Azure Stack 开发工具包（ASDK）环境中的两个虚拟网络之间创建站点到站点 VPN 连接。 配置连接时，将了解 Azure Stack 集线器中的 VPN 网关的工作原理。

### <a name="connection"></a>连接

下图显示了完成后连接配置的外观。

![站点到站点 VPN 连接配置](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>开始之前

若要完成连接配置，请确保在开始之前拥有以下各项：

* 满足 ASDK 硬件要求的两台服务器和其他先决条件，如[快速入门：评估 Azure Stack 开发工具包](../asdk/asdk-download.md)。
* [ASDK](https://azure.microsoft.com/overview/azure-stack/try/)部署包。

## <a name="deploy-the-azure-stack-development-kit-environments"></a>部署 Azure Stack 开发工具包环境

若要完成连接配置，必须部署两个 ASDK 环境。

> [!NOTE]
> 对于你部署的每个 ASDK，请按照[部署说明](../asdk/asdk-install.md)进行操作。 在本文中，ASDK 环境称为**POC1**和**POC2**。

## <a name="prepare-an-offer-on-poc1-and-poc2"></a>在 POC1 和 POC2 上准备产品/服务

在 POC1 和 POC2 上，准备一个产品/服务，以便用户可以订阅产品/服务并部署虚拟机（Vm）。 有关如何创建产品/服务的信息，请参阅[使 vm 对 Azure Stack 中心用户可用](azure-stack-tutorial-tenant-vm.md)。

## <a name="review-and-complete-the-network-configuration-table"></a>查看并完成网络配置表

下表总结了这两种 ASDK 环境的网络配置。 使用表后显示的过程来添加特定于您的网络的外部 BGPNAT 地址。

### <a name="network-configuration-table"></a>网络配置表

|   |POC1|POC2|
|---------|---------|---------|
|虚拟网络名称     |VNET-01|VNET-02 |
|虚拟网络地址空间 |10.0.10.0/23|10.0.20.0/23|
|子网名称     |Subnet-01|Subnet-02|
|子网地址范围|10.0.10.0/24 |10.0.20.0/24 |
|网关子网     |10.0.11.0/24|10.0.21.0/24|
|外部 BGPNAT 地址     |         |         |

> [!NOTE]
> 示例环境中的外部 BGPNAT IP 地址是 10.16.167.195 for POC1 和 10.16.169.131 for POC2。 使用以下过程来确定 ASDK 主机的外部 BGPNAT IP 地址，然后将其添加到以前的网络配置表中。

### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>获取 NAT VM 的外部适配器的 IP 地址

1. 登录到 POC1 的 Azure Stack 集线器物理计算机。
2. 以管理员身份打开 PowerShell 并运行以下 cmdlet：

   ```powershell
   Get-NetNatExternalAddress
   ```

3. 将 IP 地址添加到上一部分中显示的网络配置表。
4. 在 POC2 上重复此过程。

## <a name="create-the-network-resources-in-poc1"></a>在 POC1 中创建网络资源

现在，可以创建设置网关所需的 POC1 网络资源。 以下说明介绍了如何使用 Azure Stack 集线器用户门户创建资源。 你还可以使用 PowerShell 代码创建资源。

![用于创建资源的工作流](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>以租户身份登录

服务管理员可以登录为租户，以测试其租户可能使用的计划、产品/服务和订阅。 如果尚未登录，请在登录之前[创建租户帐户](azure-stack-add-new-user-aad.md)。

### <a name="create-the-virtual-network-and-vm-subnet"></a>创建虚拟网络和 VM 子网

1. 使用租户帐户登录到用户门户。
2. 在用户门户中，选择 " **+ 创建资源**"。
3. 中转到 " **Marketplace**"，然后选择 "**网络**"。
4. 选择“虚拟网络”。
5. 对于 "**名称**"、"**地址空间**"、"**子网名称**" 和 "**子网地址范围**"，请使用之前出现在网络配置表中的值。
6. 在**订阅**中，你之前创建的订阅将会出现。
7. 对于 "**资源组**"，可以创建资源组，也可以选择 "**使用现有**项"。
8. 验证默认位置。
9. 选择“固定到仪表板”。
10. 选择“创建”。

### <a name="create-the-gateway-subnet"></a>创建网关子网

1. 在仪表板上，打开之前创建的 VNET-01 虚拟网络资源。
2. 在“设置”边栏选项卡中，选择“子网”。
3. 若要将网关子网添加到虚拟网络，请选择 "**网关子**网"。

    ![添加网关子网](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. 默认情况下，子网名称设置为**GatewaySubnet**。 网关子网是特殊的。 若要正常工作，必须使用**GatewaySubnet**名称。
5. 在 "**地址范围**" 中，验证地址是否为**10.0.11.0/24**。
6. 选择 **"确定"** 创建网关子网。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关

1. 在 Azure 门户中，选择 " **+ 创建资源**"。
2. 中转到 " **Marketplace**"，然后选择 "**网络**"。
3. 从网络资源列表中，选择 "**虚拟网络网关**"。
4. 在 "**名称**" 中，输入**GW1**。
5. 选择**虚拟网络**项目以选择一个虚拟网络。 从列表中选择 " **VNET-01** "。
6. 选择 "**公共 IP 地址**" 菜单项。 当 "**选择公共 IP 地址**" 窗口打开时，选择 "**新建**"。
7. 在 "**名称**" 中，输入**GW1-PiP**，然后选择 **"确定"** 。
8. 默认情况下，对于**VPN 类型**，将选择 "**基于路由**"。 保留**基于路由**的 VPN 类型。
9. 验证“订阅”和“位置”是否正确。 可以将资源固定到仪表板。 选择“创建”。

### <a name="create-the-local-network-gateway"></a>创建本地网关

在此 Azure Stack 集线器评估部署中，*本地网络网关*的实现与实际 Azure 部署略有不同。

在 Azure 部署中，本地网关代表用于连接到 Azure 中的虚拟网络网关的本地（租户）物理设备。 在此 Azure Stack 中心评估部署中，连接的两端都是虚拟网络网关。

更常用的方法是，本地网络网关资源始终在连接的另一端指示远程网关。 由于 ASDK 的设计方式，必须提供另一个 ASDK 的网络地址转换（NAT） VM 上的外部网络适配器的 IP 地址，作为本地网络网关的公共 IP 地址。 然后在 NAT VM 上创建 NAT 映射，以确保两端连接正常。

### <a name="create-the-local-network-gateway-resource"></a>创建本地网络网关资源

1. 登录到 POC1 的 Azure Stack 集线器物理计算机。
2. 在用户门户中，选择 " **+ 创建资源**"。
3. 中转到 " **Marketplace**"，然后选择 "**网络**"。
4. 从资源列表中选择 "**本地网络网关**"。
5. 在 "**名称**" 中，输入**POC2-GW**。
6. 在 " **IP 地址**" 中，输入 POC2 的外部 BGPNAT 地址。 此地址显示在网络配置表的前面。
7. 在 "**地址空间**" 中，对于稍后创建的 POC2 VNET 的地址空间，请输入**10.0.20.0/23**。
8. 验证 "**订阅**"、"**资源组**" 和 "**位置**" 值正确，然后选择 "**创建**"。

### <a name="create-the-connection"></a>创建连接

1. 在用户门户中，选择 " **+ 创建资源**"。
2. 中转到 " **Marketplace**"，然后选择 "**网络**"。
3. 从资源列表中选择 "**连接**"。
4. 在 "**基本**设置" 边栏选项卡上，选择 "**站点到站点（IPSec）** " 作为 "**连接类型**"。
5. 选择 "**订阅**"、"**资源组**" 和 "**位置**"，然后选择 **"确定"** 。
6. 在 "**设置**" 边栏选项卡中，选择 "**虚拟网络网关**"，然后选择 " **GW1**"。
7. 选择 "**本地网络网关**"，然后选择 " **POC2**"。
8. 在 "**连接名称**" 中，输入**POC1-POC2**。
9. 在 "**共享密钥（PSK）** " 中，输入**12345**，然后选择 **"确定"** 。
10. 在 "**摘要**" 边栏选项卡中，选择 **"确定"** 。

### <a name="create-a-virtual-machine"></a>创建虚拟机

若要验证通过 VPN 连接传输的数据，需要使用 Vm 来发送和接收每个 ASDK 中的数据。 立即在 POC1 中创建 VM，然后在虚拟网络中将其放入 VM 子网：

1. 在 Azure 门户中，选择 " **+ 创建资源**"。
2. 选择 "应用**商店**"，然后选择 "**计算**"。
3. 在 VM 映像列表中，选择 " **Windows Server 2016 Datacenter Eval** " 映像。
4. 在 "**基本**信息" 边栏选项卡上的 "**名称**" 中，输入**VM01**。
5. 请输入有效的用户名和密码。 创建 VM 后，可以使用此帐户登录到该 VM。
6. 提供 "**订阅**"、"**资源组**" 和 "**位置**"，然后选择 **"确定"** 。
7. 在 "**大小**" 边栏选项卡上，为此实例选择 VM 大小，然后选择 "**选择**"。
8. 在 "**设置**" 边栏选项卡上，接受默认值。 确保已选择 " **VNET-01** " 虚拟网络。 验证子网是否设置为**10.0.10.0/24**。 然后选择“确定”。
9. 在 "**摘要**" 边栏选项卡上查看设置，然后选择 **"确定"** 。

## <a name="create-the-network-resources-in-poc2"></a>在 POC2 中创建网络资源

下一步是创建 POC2 的网络资源。 以下说明介绍如何使用用户门户来创建资源。

### <a name="sign-in-as-a-tenant-again"></a>重新以租户身份登录

服务管理员可以登录为租户，以测试其租户可能使用的计划、产品/服务和订阅。 如果尚未登录，请在登录之前[创建租户帐户](azure-stack-add-new-user-aad.md)。

### <a name="create-virtual-network-and-vm-subnet"></a>创建虚拟网络和 VM 子网

1. 使用租户帐户登录。
2. 在用户门户中，选择 " **+ 创建资源**"。
3. 中转到 " **Marketplace**"，然后选择 "**网络**"。
4. 选择“虚拟网络”。
5. 使用之前出现在网络配置表中的信息来标识 POC2**名称**、**地址空间**、**子网名称**和**子网地址范围**的值。
6. 在**订阅**中，你之前创建的订阅将会出现。
7. 对于 "**资源组**"，创建新的资源组，或者，如果已有资源组，请选择 "**使用现有**资源组"。
8. 验证默认**位置**。
9. 选择“固定到仪表板”。
10. 选择“创建”。

### <a name="create-gateway-subnet"></a>创建网关子网

1. 在仪表板中打开创建的虚拟网络资源（**VNET-02**）。
2. 在“设置”边栏选项卡中，选择“子网”。
3. 选择 "**网关子网**"，将网关子网添加到虚拟网络。
4. 默认情况下，子网的名称设置为 **GatewaySubnet**。 网关子网很特殊，必须使用该特定名称才能正常运行。
5. 在 "**地址范围**" 字段中，验证地址是否为**10.0.21.0/24**。
6. 选择 **"确定"** 创建网关子网。

### <a name="create-virtual-network-gateway"></a>创建虚拟网络网关

1. 在 Azure 门户中，选择 " **+ 创建资源**"。  
2. 中转到 " **Marketplace**"，然后选择 "**网络**"。
3. 从网络资源列表中，选择 "**虚拟网络网关**"。
4. 在 "**名称**" 中，输入**GW2**。
5. 若要选择虚拟网络，请选择 "**虚拟网络**"。 然后从列表中选择 " **VNET-02** "。
6. 选择“公共 IP 地址”。 当 "**选择公共 IP 地址**" 边栏选项卡打开时，选择 "**新建**"。
7. 在 "**名称**" 中，输入**GW2-PiP**，然后选择 **"确定"** 。
8. 默认情况下，为**VPN 类型**选择 "**基于路由**"。 保留**基于路由**的 VPN 类型。
9. 验证“订阅”和“位置”是否正确。 可以将资源固定到仪表板。 选择“创建”。

### <a name="create-local-network-gateway-resource"></a>创建本地网络网关资源

1. 在 POC2 用户门户中，选择 " **+ 创建资源**"。
2. 中转到 " **Marketplace**"，然后选择 "**网络**"。
3. 从资源列表中选择 "**本地网络网关**"。
4. 在 "**名称**" 中，输入**POC1-GW**。
5. 在 " **IP 地址**" 中，输入之前在网络配置表中列出的 POC1 的外部 BGPNAT 地址。
6. 在 "**地址空间**" 中，从 POC1 输入**VNET-01**的**10.0.10.0/23**地址空间。
7. 验证你的**订阅**、**资源组**和**位置**是否正确，然后选择 "**创建**"。

## <a name="create-connection"></a>创建连接

1. 在用户门户中，选择 " **+ 创建资源**"。
2. 中转到 " **Marketplace**"，然后选择 "**网络**"。
3. 从资源列表中选择 "**连接**"。
4. 在 "**基本**设置" 边栏选项卡中，选择 "**站点到站点（IPSec）** " 作为 "**连接类型**"。
5. 选择 "**订阅**"、"**资源组**" 和 "**位置**"，然后选择 **"确定"** 。
6. 在 "**设置**" 边栏选项卡中，选择 "**虚拟网络网关**"，然后选择 " **GW2**"。
7. 选择 "**本地网络网关**"，然后选择 " **POC1**"。
8. 在 "**连接名称**" 中，输入**POC2-POC1**。
9. 在 "**共享密钥（PSK）** " 中，输入**12345**。 如果选择其他值，请记住，该值必须与在 POC1 上创建的共享密钥的值匹配。 选择“确定”。
10. 查看 "**摘要**" 边栏选项卡，然后选择 **"确定"** 。

## <a name="create-a-virtual-machine"></a>创建虚拟机

现在，在 POC2 中创建一个 VM，并将其放在虚拟网络中的 VM 子网上：

1. 在 Azure 门户中，选择 " **+ 创建资源**"。
2. 选择 "应用**商店**"，然后选择 "**计算**"。
3. 在 VM 映像列表中，选择 " **Windows Server 2016 Datacenter Eval** " 映像。
4. 在 "**基本**信息" 边栏选项卡中，为 "**名称**" 输入**VM02**。
5. 请输入有效的用户名和密码。 创建 VM 后，可以使用此帐户登录到该 VM。
6. 提供 "**订阅**"、"**资源组**" 和 "**位置**"，然后选择 **"确定"** 。
7. 在 "**大小**" 边栏选项卡上，为此实例选择 VM 大小，然后选择 "**选择**"。
8. 在 "**设置**" 边栏选项卡上，可以接受默认值。 确保已选择 " **VNET-02**虚拟网络"，并验证子网是否设置为 " **10.0.20.0/24**"。 选择“确定”。
9. 查看 "**摘要**" 边栏选项卡上的设置，然后选择 **"确定"** 。

## <a name="configure-the-nat-vm-on-each-asdk-for-gateway-traversal"></a>在每个 ASDK 上配置 NAT VM 以进行网关遍历

由于 ASDK 是独立的，并且与部署物理主机的网络隔离，因此网关连接到的*外部*VIP 网络实际上并不是外部 VIP 网络。 而是隐藏在执行网络地址转换的路由器后面的 VIP 网络。

路由器是名为**AzS-azs-bgpnat01**的 WINDOWS Server VM，可在 ASDK 基础结构中运行路由和远程访问服务（RRAS）角色。 你必须在 AzS-azs-bgpnat01 VM 上配置 NAT，以允许站点到站点 VPN 连接两端连接。

若要配置 VPN 连接，你必须创建一个静态 NAT 映射路由，该路由将 BGPNAT VM 上的外部接口映射到边缘网关池的 VIP。 VPN 连接中的每个端口都需要静态 NAT 映射路由。

> [!NOTE]
> 仅 ASDK 环境需要此配置。

### <a name="configure-the-nat"></a>配置 NAT

> [!IMPORTANT]
> 必须为这两个 ASDK 环境完成此过程。

1. 确定要在以下 PowerShell 脚本中使用的**内部 IP 地址**。 打开虚拟网络网关（GW1 和 GW2）。 在 "**概述**" 边栏选项卡上，保存**公共 IP 地址**的值供以后使用。

   ![内部 IP 地址](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)

2. 登录到 POC1 的 Azure Stack 集线器物理计算机。
3. 复制并编辑以下 PowerShell 脚本。 若要在每个 ASDK 上配置 NAT，请在提升的 Windows PowerShell ISE 中运行该脚本。 在脚本中，将值添加到 `External BGPNAT address` 和 `Internal IP address` 占位符：

   ```powershell
   # Designate the external NAT address for the ports that use the IKE authentication.
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 499 `
      -PortEnd 501}
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress <External BGPNAT address> `
      -PortStart 4499 `
      -PortEnd 4501}
   # create a static NAT mapping to map the external address to the Gateway
   # Public IP Address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 500 `
      -InternalPort 500}
   # Finally, configure NAT traversal which uses port 4500 to
   # successfully establish the complete IPSEC tunnel over NAT devices
   Invoke-Command `
    -ComputerName AzS-bgpnat01 `
     {Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress <External BGPNAT address> `
      -InternalIPAddress <Internal IP address> `
      -ExternalPort 4500 `
      -InternalPort 4500}
   ```

4. 在 POC2 上重复此过程。

## <a name="test-the-connection"></a>测试连接

建立站点到站点连接后，你应该验证是否可以通过该连接进行流动。 若要验证，请登录到在任一 ASDK 环境中创建的 Vm 之一。 然后，对在其他环境中创建的 VM 执行 ping 操作。

若要确保通过站点到站点连接发送流量，请确保 ping 远程子网上 VM 的直接 IP （DIP）地址，而不是 VIP。 为此，请在连接的另一端查找 DIP 地址。 保存该地址供以后使用。

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>登录到 POC1 中的租户 VM

1. 登录到 POC1 的 Azure Stack 中心物理计算机，然后使用租户帐户登录到用户门户。
2. 在左侧导航栏中，选择 "**计算**"。
3. 在 Vm 列表中，找到之前创建的**VM01** ，然后选择它。
4. 在虚拟机的边栏选项卡上，单击 "**连接**"，然后打开 VM01 文件。

     ![连接按钮](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)

5. 用创建 VM 时配置的帐户登录。
6. 打开提升的**Windows PowerShell**窗口。
7. 输入**ipconfig/all**。
8. 在输出中，找到**IPv4 地址**，然后保存该地址供以后使用。 这是将从 POC2 ping 的地址。 在示例环境中，该地址为 **10.0.10.4**，但用户环境中的该地址可能有所不同。 它应位于你之前创建的**10.0.10.0/24**子网中。
9. 若要创建允许 VM 响应 ping 的防火墙规则，请运行以下 PowerShell 命令：

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>登录到 POC2 中的租户 VM

1. 登录到 POC2 的 Azure Stack 中心物理计算机，然后使用租户帐户登录到用户门户。
2. 在左侧导航栏中，单击 "**计算**"。
3. 在 Vm 列表中，找到之前创建的**VM02** ，然后选择它。
4. 在 VM 的边栏选项卡中，单击 "**连接**"。
5. 用创建 VM 时配置的帐户登录。
6. 打开提升的**Windows PowerShell**窗口。
7. 输入**ipconfig/all**。
8. IPv4 地址显示在**10.0.20.0/24**中。 在示例环境中，地址是**10.0.20.4**，但你的地址可能不同。
9. 若要创建允许 VM 响应 ping 的防火墙规则，请运行以下 PowerShell 命令：

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. 从 POC2 上的 VM，通过隧道 ping POC1 上的 VM。 为此，请对从 VM01 中记录的 DIP 进行 ping 操作。 在示例环境中，这是**10.0.10.4**，但请务必对实验室中记下的地址执行 ping 操作。 应看到类似于以下示例的结果：

    ![Ping 成功](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. 来自远程 VM 的答复表示测试成功。 可以关闭 VM 窗口。 若要测试连接，可以尝试其他类型的数据传输，例如文件副本。

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>通过网关连接查看数据传输统计信息

如果要了解通过站点到站点连接传递的数据量，此信息可在 "**连接**" 边栏选项卡上找到。 此测试也是验证刚发送的 ping 是否确实通过了 VPN 连接的另一种方法。

1. 登录到 POC2 中的租户 VM 时，请使用租户帐户登录到用户门户。
2. 中转到 "**所有资源**"，然后选择 " **POC2-POC1** " 连接。 **连接**随即出现。
3. 在 "**连接**" 窗口中，将显示**中的数据**和**数据输出**的统计信息。 在下面的屏幕截图中，较大的数字是其他文件传输的。 应会在此处看到一些非零值。

    ![传入和传出数据](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
