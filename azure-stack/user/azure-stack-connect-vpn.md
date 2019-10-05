---
title: 使用 VPN 将 Azure Stack 连接到 Azure | Microsoft Docs
description: 如何使用 VPN 将 Azure Stack 中的虚拟网络连接到 Azure 中的虚拟网络。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 10/24/2018
ms.openlocfilehash: 844162e4f31a6f543a9fe774aa40bd606dad85b9
ms.sourcegitcommit: f91979c1613ea1aa0e223c818fc208d902b81299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974109"
---
# <a name="connect-azure-stack-to-azure-using-vpn"></a>使用 VPN 将 Azure Stack 连接到 Azure

适用于：*Azure Stack 集成系统*

本文介绍如何创建站点到站点 VPN，以将 Azure Stack 中的虚拟网络连接到 Azure 中的虚拟网络。

## <a name="before-you-begin"></a>开始之前

若要完成连接配置，请确保在开始之前准备好以下各项：

* 直接连接到 Internet 的 Azure Stack 集成系统（多节点）部署。 必须能够从公共 Internet 直接连接到外部公共 IP 地址范围。
* 有效的 Azure 订阅。 如果没有 Azure 订阅，可以在此处创建一个免费的[azure 帐户](https://azure.microsoft.com/free/?b=17.06)。

### <a name="vpn-connection-diagram"></a>VPN 连接关系图

下图显示了完成后的连接配置示意图：

![站点到站点 VPN 连接配置](media/azure-stack-connect-vpn/image2.png)

### <a name="network-configuration-example-values"></a>网络配置示例值

网络配置示例表显示本文中用于示例的值。 可以使用这些值或参考这些值，以更好地了解本文中的示例：

|   |Azure Stack|Azure|
|---------|---------|---------|
|虚拟网络名称     |Azs-VNet|AzureVNet |
|虚拟网络地址空间 |10.1.0.0/16|10.100.0.0/16|
|子网名称     |前端|前端|
|子网地址范围|10.1.0.0/24 |10.100.0.0/24 |
|网关子网     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>在 Azure 中创建网络资源

首先，创建 Azure 的网络资源。 以下说明介绍如何使用 [Azure 门户](https://portal.azure.com/)来创建资源。

### <a name="create-the-virtual-network-and-virtual-machine-vm-subnet"></a>创建虚拟网络和虚拟机 (VM) 子网

1. 使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 在用户门户中，选择“+ 创建资源”。
3. 转到**市场**，然后选择“网络”。
4. 选择“虚拟网络”。
5. 使用网络配置表中的信息来识别 Azure 的“名称”、“地址空间”、“子网名称”和“子网地址范围”的值。
6. 对于“资源组”，可以创建新的资源组，也可以选择“使用现有项”（如果已有了一个）。
7. 选择 VNet 的**位置**。  如果使用示例值，请选择 "**美国东部**" 或 "使用其他位置"。
8. 选择“固定到仪表板”。
9. 选择“创建”。

### <a name="create-the-gateway-subnet"></a>创建网关子网

1. 从仪表板打开刚创建的虚拟网络资源 (**AzureVNet**)。
2. 在“设置”部分中选择“子网”。
3. 选择“网关子网”，将网关子网添加到虚拟网络。
4. 默认情况下，子网的名称设置为 **GatewaySubnet**。

   >[!IMPORTANT]
   >网关子网很特殊，必须使用该特定名称才能正常运行。

5. 在“地址范围”字段中，检查地址是否为 **10.100.1.0/24**。
6. 选择“确定”创建网关子网。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关

1. 在 Azure 门户中，选择“+ 创建资源”。

2. 转到**市场**，然后选择“网络”。
3. 从网络资源列表中选择“虚拟网络网关”。
4. 在“名称”字段中，键入 **Azure-GW**。
5. 若要选择虚拟网络，请选择“虚拟网络”。 然后从列表中选择“AzureVnet”。
6. 选择“公共 IP 地址”。 当“选择公共 IP 地址”部分打开时，选择“新建”。
7. 在“名称”字段中键入 **Azure-GW-PiP**，然后选择“确定”。
8. 验证“订阅”和“位置”是否正确。 可将资源固定到仪表板。 选择“创建”。

### <a name="create-the-local-network-gateway-resource"></a>创建本地网关资源

1. 在 Azure 门户中，选择“+ 创建资源”。

2. 转到**市场**，然后选择“网络”。
3. 从资源列表中选择“本地网络网关”。
4. 在“名称”字段中，键入 **Azs-GW**。
5. 在“IP 地址”字段中，键入前面在网络配置表中列出的 Azure Stack 虚拟网关的公共 IP 地址。
6. 在 Azure Stack 的“地址空间”字段中，键入 **AzureVNet** 的 **10.1.0.0/24** 和 **10.1.1.0/24** 地址空间。
7. 确认“订阅”、“资源组”和“位置”正确无误，然后选择“创建”。

## <a name="create-the-connection"></a>创建连接

1. 在用户门户中，选择“+ 创建资源”。
2. 转到**市场**，然后选择“网络”。
3. 从资源列表中选择“连接”。
4. 在“基本”设置部分，选择“站点到站点(IPSec)”作为“连接类型”。
5. 选择“订阅”、“资源组”和“位置”，然后选择“确定”。
6. 在“设置”部分，依次选择“虚拟网络网关”、“Azure-GW”。
7. 依次选择“本地网络网关”、“Azs-GW”。
8. 在“连接名称”中，键入 **Azure-Azs**。
9. 在“共享密钥(PSK)”中键入 **12345**，然后选择“确定”。

   >[!NOTE]
   >如果将另一值用于共享密钥，请记住，该值必须与在连接另一端创建的共享密钥值匹配。

10. 查看“摘要”部分，然后选择“确定”。

## <a name="create-a-vm"></a>创建 VM

现在请在 Azure 中创建一个 VM，然后将其置于虚拟网络的 VM 子网上。

1. 在 Azure 门户中，选择“+ 创建资源”。
2. 转到“市场”，选择“计算”。
3. 在 VM 映像列表中，选择“Windows Server 2016 Datacenter Eval”映像。
4. 在“基本”部分的“名称”中，键入 **AzureVM**。
5. 键入有效的用户名和密码。 创建 VM 之后，将使用此帐户来登录 VM。
6. 提供“订阅”、“资源组”和“位置”，并选择“确定”。
7. 在“大小”部分，为此实例选择一种 VM 大小，然后选择“选择”。
8. 在“设置”部分，可以使用默认设置。 在选择“确定”之前，请确认：

   * **AzureVnet** 虚拟网络已选中。
   * 子网已设置为 **10.100.0.0/24**。

   选择“确定”。

9. 在“摘要”部分检查设置，然后选择“确定”。

## <a name="create-the-network-resources-in-azure-stack"></a>在 Azure Stack 中创建网络资源

接下来，在 Azure Stack 中创建网络资源。

### <a name="sign-in-as-a-user"></a>以用户身份登录

服务管理员可以用户身份登录，对其用户可能使用的计划、套餐和订阅进行测试。 请在登录之前[创建用户帐户](../operator/azure-stack-add-new-user-aad.md)（如果尚未这样做）。

### <a name="create-the-virtual-network-and-a-vm-subnet"></a>创建虚拟网络和 VM 子网

1. 使用用户帐户登录到用户门户。
2. 在用户门户中，选择“+ 创建资源”。

    ![创建新的虚拟网络](media/azure-stack-connect-vpn/image3.png)

3. 转到**市场**，然后选择“网络”。
4. 选择“虚拟网络”。
5. 对于“名称”、“地址空间”、“子网名称”和“子网地址范围”，请使用网络配置表中的值。
6. “订阅”中显示了前面创建的订阅。
7. 对于“资源组”，可以创建资源组，也可以选择“使用现有项”（如果已有了一个）。
8. 验证默认位置。
9. 选择“固定到仪表板”。
10. 选择“创建”。

### <a name="create-the-gateway-subnet"></a>创建网关子网

1. 在仪表板上，打开创建的 Azs-VNet 虚拟网络资源。
2. 在“设置”部分中选择“子网”。
3. 若要将网关子网添加到虚拟网络，请选择“网关子网”。

    ![添加网关子网](media/azure-stack-connect-vpn/image4.png)

4. 子网名称默认设置为 **GatewaySubnet**。 要使网关子网正常运行，它们必须使用 **GatewaySubnet** 名称。
5. 在“地址范围”中，确认地址是 **10.1.1.0/24**。
6. 选择“确定”创建网关子网。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关

1. 在 Azure Stack 门户中，选择“+ 创建资源”。
2. 转到**市场**，然后选择“网络”。
3. 从网络资源列表中选择“虚拟网络网关”。
4. 在“名称”中，键入 **Azs-GW**。
5. 选择“虚拟网络”项以选择虚拟网络。 从列表中选择“Azs-VNet”。
6. 选择“公共 IP 地址”菜单项。 当“选择公共 IP 地址”部分打开时，选择“新建”。
7. 在“名称”中键入 **Azs-GW-PiP**，然后选择“确定”。
8. 对于“VPN 类型”，默认选择“基于路由”。 保留“基于路由”VPN 类型。

9. 验证“订阅”和“位置”是否正确。 可将资源固定到仪表板。 选择“创建”。

### <a name="create-the-local-network-gateway"></a>创建本地网关

在 Azure Stack 中，“本地网关”的概念稍微不同于 Azure 部署。

在 Azure 部署中，本地网关代表一个本地（位于用户位置）物理设备，可连接到 Azure 中的虚拟网关。 但在 Azure Stack 中，连接的两端都是虚拟网关。

更通俗的解释是，存在本地网络网关资源往往意味着在连接的另一端存在远程网关。

### <a name="create-the-local-network-gateway-resource"></a>创建本地网关资源

1. 登录到 Azure Stack 门户。
2. 在用户门户中，选择“+ 创建资源”。
3. 转到**市场**，然后选择“网络”。
4. 从资源列表中选择“本地网络网关”。
5. 在“名称”字段中，键入 **Azure-GW**。
6. 在“IP 地址”字段中，键入 Azure **Azure-GW-PiP** 中的虚拟网关公共 IP 地址。 此地址已显示在前面的网络配置表中。
7. 在“地址空间”字段中键入 **10.100.0.0/24** 和 **10.100.1.0/24**，用作创建的 Azure VNET 的地址空间。

8. 确认“订阅”、“资源组”和“位置”值正确无误，然后选择“创建”。

### <a name="create-the-connection"></a>创建连接

1. 在用户门户中，选择“+ 创建资源”。
2. 转到**市场**，然后选择“网络”。
3. 从资源列表中选择“连接”。
4. 在“基本”设置部分，针对“连接类型”选择“站点到站点(IPSec)”。
5. 选择“订阅”、“资源组”和“位置”，然后选择“确定”。
6. 在“设置”部分，依次选择“虚拟网络网关”、“Azs-GW”。
7. 依次选择“本地网络网关”、“Azure-GW”。
8. 在“连接名称”中，键入 **Azs-Azure**。
9. 在“共享密钥(PSK)”中键入 **12345**，然后选择“确定”。

10. 在“摘要”部分，选择“确定”。

### <a name="create-a-vm"></a>创建 VM

若要检查 VPN 连接，请创建两个 VM：一个在 Azure 中，一个在 Azure Stack 中。 创建这些 VM 以后，即可使用它们通过 VPN 隧道发送和接收数据。

1. 在 Azure 门户中，选择“+ 创建资源”。
2. 转到“市场”，选择“计算”。
3. 在 VM 映像列表中，选择“Windows Server 2016 Datacenter Eval”映像。
4. 在“基本”部分的“名称”中，键入 **Azs-VM**。
5. 键入有效的用户名和密码。 创建 VM 之后，将使用此帐户来登录 VM。
6. 提供“订阅”、“资源组”和“位置”，并选择“确定”。
7. 在“大小”部分，为此实例选择一种 VM 大小，然后选择“选择”。
8. 在“设置”部分接受默认值。 务必选择 **Azs-VNet** 虚拟网络。 确认子网已设置为 **10.1.0.0/24**。 然后选择“确定”。

9. 在“摘要”部分中，查看设置并选择“确定”。

## <a name="test-the-connection"></a>测试连接

建立站点到站点连接以后，应验证数据是否可以往两个方向流动。 若要测试连接，最容易的方式是进行 ping 测试：

* 登录到在 Azure Stack 中创建的 VM，然后 ping Azure 中的 VM。
* 登录到在 Azure 中创建的 VM，然后 ping Azure Stack 中的 VM。

>[!NOTE]
>为了确保发送的流量通过站点到站点连接，必须 ping 远程子网上 VM 的直接 IP (DIP) 地址，而不是 VIP。

### <a name="sign-in-to-the-user-vm-in-azure-stack"></a>登录到 Azure Stack 中的用户 VM

1. 登录到 Azure Stack 门户。
2. 从左侧导航栏中选择“虚拟机”。
3. 在 VM 列表中，找到前面创建的 **Azs-VM**，并选择它。
4. 在 VM 部分选择“连接”，然后打开 Azs-VM.rdp 文件。

     ![“连接”按钮](media/azure-stack-connect-vpn/image17.png)

5. 使用创建 VM 时所配置的帐户登录。
6. 打开提升的 Windows PowerShell 提示符。
7. 键入 **ipconfig /all**。
8. 在输出中找到“IPv4 地址”，然后保存该地址供稍后使用。 这是要从 Azure ping 的地址。 在示例环境中，该地址为 **10.1.0.4**，但用户环境中的该地址可能有所不同。 该地址应在此前创建的 **10.1.0.0/24** 子网范围内。
9. 若要创建允许 VM 响应 ping 的防火墙规则，请运行以下 PowerShell 命令：

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>登录到 Azure 中的租户 VM

1. 登录到 Azure 门户。
2. 从左侧导航栏中选择“虚拟机”。
3. 从 VM 列表中找到前面创建的 **Azure-VM**，并选择它。
4. 在 VM 部分选择“连接”。
5. 使用创建 VM 时所配置的帐户登录。
6. 打开权限提升的 **Windows PowerShell** 窗口。
7. 键入 **ipconfig /all**。
8. 应会看到 IPv4 地址位于 **10.100.0.0/24** 范围内。 在示例环境中，该地址为 **10.100.0.4**，但你的地址可能有所不同。
9. 若要创建允许 VM 响应 ping 的防火墙规则，请运行以下 PowerShell 命令：

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. 在 Azure 上的 VM 中，通过隧道 ping Azure Stack 中的 VM。 为此，请 ping 从 Azs-VM 中记录的 DIP。 在示例环境中，该地址为 **10.1.0.4**，但请确保 ping 实验室中记下的地址。 会看到以下屏幕捕获所示的结果：

    ![ping 成功](media/azure-stack-connect-vpn/image19b.png)

11. 获得远程 VM 的答复表示测试成功。 此时可关闭“VM”窗口。

还应执行更严格的数据传输测试（例如，朝两个方向复制不同大小的文件）。

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>通过网关连接查看数据传输统计信息

若要了解通过站点到站点连接传送的数据量，可在“连接”部分查看该信息。 也可利用此测试来验证刚发送的 ping 是否确实通过了 VPN 连接。

1. 在已登录到 Azure Stack 中用户 VM 的情况下，使用用户帐户登录到用户门户。
2. 转到“所有资源”，选择“Azs-Azure”连接。 此时会显示“连接”。
3. “连接”部分显示了“传入数据”和“传出数据”的统计信息。 在以下屏幕捕获中，较大的数字是附加的文件传输造成的。 应会看到其中有一些非零值。

    ![传入和传出数据](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>后续步骤

* [将应用部署到 Azure 和 Azure Stack](azure-stack-solution-pipeline.md)
