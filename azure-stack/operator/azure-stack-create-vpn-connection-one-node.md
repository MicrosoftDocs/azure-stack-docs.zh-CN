---
title: 在不同 Azure Stack 开发工具包环境中的两个虚拟网络之间创建站点到站点 VPN 连接 | Microsoft Docs
description: 逐步指导云管理员在两个单节点 Azure Stack 开发工具包环境之间创建站点到站点 VPN 连接。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3f1b4e02-dbab-46a3-8e11-a777722120ec
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 09/12/2018
ROBOTS: NOINDEX
ms.openlocfilehash: cee1ca68caa6742eb5d965b53b685746d9057691
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985397"
---
# <a name="create-a-site-to-site-vpn-connection-between-two-virtual-networks-in-different-azure-stack-development-kit-environments"></a>在不同 Azure Stack 开发工具包环境中的两个虚拟网络之间创建站点到站点 VPN 连接

## <a name="overview"></a>概述

本文介绍如何创建在两个单独的 Azure Stack 开发工具包 (ASDK) 环境中的两个虚拟网络之间的站点到站点 VPN 连接。 在配置连接时，你了解 Azure Stack 中的 VPN 网关的工作方式。

### <a name="connection"></a>连接

下图显示了连接配置示意图完成后。

![站点到站点 VPN 连接配置](media/azure-stack-create-vpn-connection-one-node-tp2/OneNodeS2SVPN.png)

### <a name="before-you-begin"></a>开始之前

若要完成连接配置，请确保在开始之前准备好以下各项：

* 两个服务器和其他满足 ASDK 硬件要求，如中所述的先决条件[快速入门：评估 Azure Stack 开发工具包](../asdk/asdk-download.md)中所述 Azure Stack 开发工具包硬件要求的两个服务器和其他必备组件。
* [Azure Stack 开发工具包](https://azure.microsoft.com/overview/azure-stack/try/)部署包。

## <a name="deploy-the-azure-stack-development-kit-environments"></a>部署 Azure Stack 开发工具包环境

若要完成连接配置，必须部署两个 ASDK 环境。

> [!NOTE]
> 对于每个 ASDK 部署中，请按照[部署说明](../asdk/asdk-install.md)。 在本文中，称为 ASDK 环境**POC1**并**POC2**。

## <a name="prepare-an-offer-on-poc1-and-poc2"></a>在 POC1 和 POC2 中准备产品

在 POC1 和 POC2 中准备一个产品，使用户能够订阅该产品并部署虚拟机。 有关如何创建产品的信息，请参阅[向 Azure Stack 用户提供虚拟机](azure-stack-tutorial-tenant-vm.md)。

## <a name="review-and-complete-the-network-configuration-table"></a>查看并填写网络配置表

下表总结了这两个 ASDK 环境的网络配置。 使用此表格后面提供的过程来添加网络特定的外部 BGPNAT 地址。

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
> 示例环境中的外部 BGPNAT IP 地址是 10.16.167.195（用于 POC1）和 10.16.169.131（用于 POC2）。 使用以下过程来确定的外部 BGPNAT IP 地址为 ASDK 主机，并将它们添加到以前的网络配置表。

### <a name="get-the-ip-address-of-the-external-adapter-of-the-nat-vm"></a>获取 NAT VM 的外部适配器的 IP 地址

1. 登录到 POC1 的 Azure Stack 物理机。
2. 编辑以下 PowerShell 代码以替换管理员密码，并在 POC 主机上运行的代码：

   ```powershell
   cd \AzureStack-Tools-master\connect
   Import-Module .\AzureStack.Connect.psm1
   $Password = ConvertTo-SecureString "<your administrator password>" `
    -AsPlainText `
    -Force
   Get-AzureStackNatServerAddress `
    -HostComputer "AzS-bgpnat01" `
    -Password $Password
   ```

3. 将 IP 地址添加到上一部分中的网络配置表。

4. 在 POC2 上重复运行此过程。

## <a name="create-the-network-resources-in-poc1"></a>在 POC1 中创建网络资源

现在可以创建所需设置网关的 POC1 网络资源。 下面的说明介绍如何使用 Azure Stack 用户门户中创建的资源。 也可以使用 PowerShell 代码来创建资源。

![用于创建资源的工作流](media/azure-stack-create-vpn-connection-one-node-tp2/image2.png)

### <a name="sign-in-as-a-tenant"></a>以租户身份登录

服务管理员可以租户身份登录，以便对其租户可能使用的计划、套餐和订阅进行测试。 如果还没有的话[创建租户帐户](azure-stack-add-new-user-aad.md)在登录之前。

### <a name="create-the-virtual-network-and-vm-subnet"></a>创建虚拟网络和 VM 子网

1. 使用租户帐户登录到用户门户。
2. 在用户门户中，选择“+ 创建资源”。
3. 转到**市场**，然后选择“网络”。
4. 选择“虚拟网络”。
5. 对于“名称”、“地址空间”、“子网名称”和“子网地址范围”，请使用前面网络配置表中显示的值。
6. “订阅”中显示了前面创建的订阅。
7. 对于“资源组”，可以创建资源组，也可以选择“使用现有项”（如果已有了一个）。
8. 验证默认位置。
9. 选择“固定到仪表板”。
10. 选择“创建”。

### <a name="create-the-gateway-subnet"></a>创建网关子网

1. 在仪表板中，打开前面创建的 vnet-01 虚拟网络资源。
2. 在“设置”边栏选项卡中，选择“子网”。
3. 若要将网关子网添加到虚拟网络，请选择“网关子网”。

    ![添加网关子网](media/azure-stack-create-vpn-connection-one-node-tp2/image4.png)

4. 子网名称默认设置为 **GatewaySubnet**。 网关子网相当特殊。 若要正常运行，它们必须使用 **GatewaySubnet** 名称。
5. 在“地址范围”中，确认地址是 **10.0.11.0/24**。
6. 选择“确定”创建网关子网。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关

1. 在 Azure 门户中，选择“+ 创建资源”。
2. 转到**市场**，然后选择“网络”。
3. 从网络资源列表中选择“虚拟网络网关”。
4. 在“名称”中输入 **GW1**。
5. 选择“虚拟网络”项以选择虚拟网络。 从列表中选择“VNET-01”。
6. 选择“公共 IP 地址”菜单项。 当**选择公共 IP 地址**窗口将打开，选择**新建**。
7. 在“名称”中输入 **GW1-PiP**，然后选择“确定”。
8. 对于“VPN 类型”，默认已选择“基于路由”。 保留“基于路由”VPN 类型。
9. 验证“订阅”和“位置”是否正确。 可将资源固定到仪表板。 选择“创建”。

### <a name="create-the-local-network-gateway"></a>创建本地网关

在此 Azure Stack 评估部署中实现*本地网关* 稍微不同于实际 Azure 部署中的情况。

在 Azure 部署中，本地网关代表一个本地（租户处）物理设备，用于连接到 Azure 中的虚拟网络网关。 在此 Azure Stack 评估部署中，连接的两端都是虚拟网络网关。

从更通用的角度来看，存在本地网关资源通常意味着在连接的另一端存在远程网关。 由于 Azure Stack 开发工具包的设计的方式，必须在网络地址转换 (NAT) 的其他 ASDK 作为本地网络网关的公共 IP 地址的 VM 上提供的外部网络适配器的 IP 地址。 然后需要在 NAT VM 上创建 NAT 映射，确保正确连接两端。

### <a name="create-the-local-network-gateway-resource"></a>创建本地网关资源

1. 登录到 POC1 的 Azure Stack 物理机。
2. 在用户门户中，选择“+ 创建资源”。
3. 转到**市场**，然后选择“网络”。
4. 从资源列表中选择“本地网络网关”。
5. 在“名称”中输入 **POC2-GW**。
6. 在“IP 地址”中，输入 POC2 的外部 BGPNAT 地址。 此地址已显示在前面的网络配置表中。
7. 在“地址空间”中，对于稍后要创建的 POC2 VNET 的地址空间，输入 **10.0.20.0/23**。
8. 确认“订阅”、“资源组”和“位置”值正确无误，然后选择“创建”。

### <a name="create-the-connection"></a>创建连接

1. 在用户门户中，选择“+ 创建资源”。
2. 转到**市场**，然后选择“网络”。
3. 从资源列表中选择“连接”。
4. 在“基本”设置边栏选项卡上，针对“连接类型”选择“站点到站点(IPSec)”。
5. 选择“订阅”、“资源组”和“位置”，然后选择“确定”。
6. 在“设置”边栏选项卡上，依次选择“虚拟网络网关”、“GW1”。
7. 依次选择“本地网络网关”、“POC2-GW”。
8. 在“连接名称”中，输入 **POC1-POC2**。
9. 在“共享密钥(PSK)”中输入 **12345**，然后选择“确定”。
10. 在“摘要”边栏选项卡上，选择“确定”。

### <a name="create-a-vm"></a>创建 VM

若要验证通过 VPN 连接传输的数据，需要使用虚拟机在每个 Azure Stack 开发工具包中发送和接收数据。 现在请在 POC1 中创建虚拟机，然后将它放在虚拟网络的 VM 子网上。

1. 在 Azure 门户中，选择“+ 创建资源”。
2. 转到“市场”，选择“计算”。
3. 在虚拟机映像列表中，选择“Windows Server 2016 Datacenter Eval”映像。
4. 在“基本”边栏选项卡的“名称”中，输入 **VM01**。
5. 输入有效的用户名和密码。 创建 VM 之后，将使用此帐户来登录 VM。
6. 提供“订阅”、“资源组”和“位置”，并选择“确定”。
7. 在“大小”边栏选项卡上，为此实例选择一种虚拟机大小，然后选择“选择”。
8. 在“设置”边栏选项卡上接受默认值。 务必选择 **VNET-01** 虚拟网络。 确认子网已设置为 **10.0.10.0/24**。 然后选择“确定”。
9. 在“摘要”边栏选项卡上检查设置，并选择“确定”。

## <a name="create-the-network-resources-in-poc2"></a>在 POC2 中创建网络资源

下一步骤是创建 POC2 的网络资源。 以下说明介绍如何使用用户门户来创建资源。

### <a name="sign-in-as-a-tenant-again"></a>再次登录以租户身份

服务管理员可以租户身份登录，以便对其租户可能使用的计划、套餐和订阅进行测试。 如果还没有的话[创建租户帐户](azure-stack-add-new-user-aad.md)在登录之前。

### <a name="create-virtual-network-and-vm-subnet"></a>创建虚拟网络和 VM 子网

1. 使用租户帐户登录。
2. 在用户门户中，选择“+ 创建资源”。
3. 转到**市场**，然后选择“网络”。
4. 选择“虚拟网络”。
5. 使用前面网络配置表中显示的信息来识别 POC2 的“名称”、“地址空间”、“子网名称”和“子网地址范围”的值。
6. “订阅”中显示了前面创建的订阅。
7. 对于“资源组”，可以创建新的资源组，也可以选择“使用现有项”（如果已有了一个）。
8. 确认默认“位置”。
9. 选择“固定到仪表板”。
10. 选择“创建”。

### <a name="create-gateway-subnet"></a>创建网关子网

1. 从仪表板打开刚创建的虚拟网络资源 (**VNET-02**)。
2. 在“设置”边栏选项卡中，选择“子网”。
3. 选择“网关子网”，将网关子网添加到虚拟网络。
4. 默认情况下，子网的名称设置为 **GatewaySubnet**。 网关子网很特殊，必须使用该特定名称才能正常运行。
5. 在“地址范围”字段中，检查地址是否为 **10.0.21.0/24**。
6. 选择“确定”创建网关子网。

### <a name="create-virtual-network-gateway"></a>创建虚拟网络网关

1. 在 Azure 门户中，选择“+ 创建资源”。  
2. 转到**市场**，然后选择“网络”。
3. 从网络资源列表中选择“虚拟网络网关”。
4. 在“名称”中输入 **GW2**。
5. 若要选择虚拟网络，请选择“虚拟网络”。 然后从列表中选择 **VNET-02**。
6. 选择“公共 IP 地址”。 当“选择公共 IP 地址”边栏选项卡打开时，选择“新建”。
7. 在“名称”中输入 **GW2-PiP**，然后选择“确定”。
8. 对于“VPN 类型”，默认已选择“基于路由”。 保留“基于路由”VPN 类型。
9. 验证“订阅”和“位置”是否正确。 可将资源固定到仪表板。 选择“创建”。

### <a name="create-local-network-gateway-resource"></a>创建本地网络网关资源

1. 在 POC2 用户门户中，选择“+ 创建资源”。
2. 转到**市场**，然后选择“网络”。
3. 从资源列表中选择“本地网络网关”。
4. 在“名称”中输入 **POC1-GW**。
5. 在中**IP 地址**，输入网络配置表中前面列出的 POC1 外部 BGPNAT 地址。
6. 在 POC1 的“地址空间”字段中，输入 **VNET-01** 的地址空间 **10.0.10.0/23**。
7. 确认“订阅”、“资源组”和“位置”正确无误，然后选择“创建”。

## <a name="create-connection"></a>创建连接

1. 在用户门户中，选择“+ 创建资源”。
2. 转到**市场**，然后选择“网络”。
3. 从资源列表中选择“连接”。
4. 在“基本”设置边栏选项卡中，选择“站点到站点(IPSec)”作为“连接类型”。
5. 选择“订阅”、“资源组”和“位置”，然后选择“确定”。
6. 在“设置”边栏选项卡上，依次选择“虚拟网络网关”、“GW2”。
7. 依次选择“本地网络网关”、“POC1-GW”。
8. 在“连接名称”中，输入 **POC2-POC1**。
9. 在“共享密钥(PSK)”中，输入 **12345**。 如果您选择不同的值，请记住它必须与在 POC1 上创建的共享密钥值匹配。 选择“确定”。
10. 查看“摘要”边栏选项卡，然后选择“确定”。

## <a name="create-a-virtual-machine"></a>创建虚拟机

现在在 POC2 中, 创建虚拟机并将其放在虚拟网络中的 VM 子网上：

1. 在 Azure 门户中，选择“+ 创建资源”。
2. 转到“市场”，选择“计算”。
3. 在虚拟机映像列表中，选择“Windows Server 2016 Datacenter Eval”映像。
4. 在“基本”边栏选项卡上的“名称”中，输入 **VM02**。
5. 输入有效的用户名和密码。 创建虚拟机之后，将使用此帐户来登录虚拟机。
6. 提供“订阅”、“资源组”和“位置”，并选择“确定”。
7. 在“大小”边栏选项卡上，为此实例选择一种虚拟机大小，然后选择“选择”。
8. 在“设置”边栏选项卡上，可以接受默认值。 确保选择 **VNET-02** 虚拟网络，并确认子网已设置为 **10.0.20.0/24**。 选择“确定”。
9. 在“摘要”边栏选项卡上检查设置，然后选择“确定”。

## <a name="configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal"></a>在每个 Azure Stack 开发工具包上配置 NAT 虚拟机以进行网关遍历

由于 ASDK 是自包含，并且与部署物理主机的网络隔离*外部*网关连接到的 VIP 网络实际上并非位于外部。 而是隐藏在执行网络地址转换的路由器后面。

该路由器是 Windows Server 虚拟机，称为**AzS-bgpnat01**，ASDK 基础结构中运行路由和远程访问服务 (RRAS) 角色。 必须对 AzS-bgpnat01 虚拟机上的 NAT 进行配置，允许站点到站点 VPN 连接在两端进行连接。

若要配置 VPN 连接，必须创建静态 NAT 映射路由，将 BGPNAT 虚拟机上的外部接口映射到边缘网关池的 VIP。 VPN 连接中的每个端口都必须有一个静态 NAT 映射路由。

> [!NOTE]
> 只有 Azure Stack 开发工具包环境才需要此配置。
>
>

### <a name="configure-the-nat"></a>配置 NAT

> [!IMPORTANT]
> 必须完成此过程的这两个 ASDK 环境。

1. 确定要在以下 PowerShell 脚本中使用的“内部 IP 地址”。 打开虚拟网络网关（GW1 和 GW2），然后在“概述”边栏选项卡上，保存“公共 IP 地址”的值供稍后使用。

   ![内部 IP 地址](media/azure-stack-create-vpn-connection-one-node-tp2/InternalIP.PNG)

2. 登录到 POC1 的 Azure Stack 物理机。
3. 复制并编辑以下 PowerShell 脚本。 若要在每个 Azure Stack 开发工具包中配置 NAT，请在权限提升的 Windows PowerShell ISE 中运行该脚本。 在脚本中，将值添加到`External BGPNAT address`和`Internal IP address`占位符：

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

4. 在 POC2 上重复运行此过程。

## <a name="test-the-connection"></a>测试连接

建立站点到站点连接以后，应验证其流量是否正常。 若要验证，请登录到在任一 ASDK 环境中创建的虚拟机之一。 然后，ping 另一环境中创建的虚拟机。

为了确保流量通过站点到站点连接，必须 ping 远程子网上虚拟机的直接 IP (DIP) 地址，而不是 VIP。 为此，请找到连接另一端的 DIP 地址。 保存该地址供稍后使用。

### <a name="sign-in-to-the-tenant-vm-in-poc1"></a>登录到 POC1 中的租户 VM

1. 登录 POC1 的 Azure Stack 物理机，然后使用租户帐户登录到用户门户。
2. 在左导航栏中，选择“计算”。
3. 在 VM 列表中，找到前面创建的 **VM01**，并选择它。
4. 在虚拟机的边栏选项卡上，单击“连接”，然后打开 VM01.rdp 文件。

     ![“连接”按钮](media/azure-stack-create-vpn-connection-one-node-tp2/image17.png)

5. 使用创建虚拟机时所配置的帐户登录。
6. 打开权限提升的 **Windows PowerShell** 窗口。
7. 输入 **ipconfig /all**。
8. 在输出中找到“IPv4 地址”，然后保存该地址供稍后使用。 这是要从 POC2 ping 的地址。 在示例环境中，该地址为 **10.0.10.4**，但用户环境中的该地址可能有所不同。 该地址应在此前创建的 **10.0.10.0/24** 子网范围内。
9. 若要创建允许虚拟机响应 ping 的防火墙规则，请运行以下 PowerShell 命令：

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-poc2"></a>登录到 POC2 中的租户 VM

1. 登录 POC2 的 Azure Stack 物理机，然后使用租户帐户登录到用户门户。
2. 在左侧导航栏中，单击“计算”。
3. 在虚拟机列表中，找到前面创建的 **VM02**，并选择它。
4. 在虚拟机边栏选项卡上，单击“连接”。
5. 使用创建虚拟机时所配置的帐户登录。
6. 打开权限提升的 **Windows PowerShell** 窗口。
7. 输入 **ipconfig /all**。
8. IPv4 地址显示内**10.0.20.0/24**。 在示例环境中，该地址为 **10.0.20.4**，但你的地址可能有所不同。
9. 若要创建允许虚拟机响应 ping 的防火墙规则，请运行以下 PowerShell 命令：

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. 在 POC2 上的虚拟机中，通过隧道 ping POC1 上的虚拟机。 为此，请 ping 从 VM01 中记录的 DIP。 在示例环境中，该地址为 **10.0.10.4**，但请确保 ping 实验室中记下的地址。 应看到如以下示例所示的结果：

    ![ping 成功](media/azure-stack-create-vpn-connection-one-node-tp2/image19b.png)
11. 获得远程虚拟机的答复表示测试成功。 可以关闭虚拟机窗口。 若要测试连接，可以尝试其他类型的数据传输，例如文件副本。

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>通过网关连接查看数据传输统计信息

若要了解通过站点到站点连接传送的数据量，可在“连接”边栏选项卡中查看该信息。 也可利用此测试来验证刚发送的 ping 是否确实通过了 VPN 连接。

1. 在已登录到 POC2 中租户虚拟机的情况下，使用租户帐户登录到用户门户。
2. 转到“所有资源”，选择“POC2-POC1”连接。 此时会显示“连接”。
3. 在**连接**窗口中的统计信息**中的数据**并**输出数据量**出现。 在以下屏幕截图中，较大的数字是附加的文件传输造成的。 应会看到其中有一些非零值。

    ![传入和传出数据](media/azure-stack-create-vpn-connection-one-node-tp2/image20.png)
