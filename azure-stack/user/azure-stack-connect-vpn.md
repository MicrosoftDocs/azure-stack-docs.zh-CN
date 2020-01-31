---
title: 使用 VPN 将 Azure Stack 中心连接到 Azure
description: 如何使用 VPN 将 Azure Stack 集线器中的虚拟网络连接到 Azure 中的虚拟网络。
author: sethmanheim
ms.topic: conceptual
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: scottnap
ms.lastreviewed: 10/24/2018
ms.openlocfilehash: c0a03b453a574b04e6b4a8654e3375d552acc5f5
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883913"
---
# <a name="connect-azure-stack-hub-to-azure-using-vpn"></a>使用 VPN 将 Azure Stack 中心连接到 Azure

本文介绍如何创建站点到站点 VPN，以将 Azure Stack 集线器中的虚拟网络连接到 Azure 中的虚拟网络。

## <a name="before-you-begin"></a>开始之前

若要完成连接配置，请确保在开始之前拥有以下项：

* 直接连接到 internet 的 Azure Stack 集线器集成系统（多节点）部署。 外部公共 IP 地址范围必须直接从公共 internet 访问。
* 有效的 Azure 订阅。 如果没有 Azure 订阅，可以在此处创建一个免费的[azure 帐户](https://azure.microsoft.com/free/?b=17.06)。

### <a name="vpn-connection-diagram"></a>VPN 连接示意图

下图显示了完成后的连接配置应如下所示：

![站点到站点 VPN 连接配置](media/azure-stack-connect-vpn/image2.png)

### <a name="network-configuration-example-values"></a>网络配置示例值

网络配置示例表显示了本文中的示例所使用的值。 您可以使用这些值，也可以参考这些值以更好地理解本文中的示例：

|   |Azure Stack Hub|Azure|
|---------|---------|---------|
|虚拟网络名称     |Azs-VNet|AzureVNet |
|虚拟网络地址空间 |10.1.0.0/16|10.100.0.0/16|
|子网名称     |FrontEnd|FrontEnd|
|子网地址范围|10.1.0.0/24 |10.100.0.0/24 |
|网关子网     |10.1.1.0/24|10.100.1.0/24|

## <a name="create-the-network-resources-in-azure"></a>在 Azure 中创建网络资源

首先，创建 Azure 的网络资源。 下面的说明演示如何使用[Azure 门户](https://portal.azure.com/)创建资源。

### <a name="create-the-virtual-network-and-virtual-machine-vm-subnet"></a>创建虚拟网络和虚拟机（VM）子网

1. 使用 Azure 帐户登录到 [Azure 门户](https://portal.azure.com/)。

2. 在用户门户中，选择 " **+ 创建资源**"。
3. 中转到 " **Marketplace**"，然后选择 "**网络**"。
4. 选择“虚拟网络”。
5. 使用网络配置表中的信息来标识 Azure**名称**、**地址空间**、**子网名称**和**子网地址范围**的值。
6. 对于 "**资源组**"，创建新的资源组，或者，如果已有资源组，请选择 "**使用现有**资源组"。
7. 选择 VNet 的**位置**。  如果使用示例值，请选择 "**美国东部**" 或 "使用其他位置"。
8. 选择“固定到仪表板”。
9. 选择“创建”。

### <a name="create-the-gateway-subnet"></a>创建网关子网

1. 在仪表板中打开创建的虚拟网络资源（**AzureVNet**）。
2. 在 "**设置**" 部分，选择 "**子网**"。
3. 选择 "**网关子网**"，将网关子网添加到虚拟网络。
4. 默认情况下，子网的名称设置为 **GatewaySubnet**。

   >[!IMPORTANT]
   >网关子网很特殊，必须使用该特定名称才能正常运行。

5. 在 "**地址范围**" 字段中，验证地址是否为**10.100.1.0/24**。
6. 选择 **"确定"** 创建网关子网。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关

1. 在 Azure 门户中，选择 " **+ 创建资源**"。

2. 中转到 " **Marketplace**"，然后选择 "**网络**"。
3. 从网络资源列表中，选择 "**虚拟网络网关**"。
4. 在 "**名称**" 字段中，键入**Azure-GW**。
5. 若要选择虚拟网络，请选择 "**虚拟网络**"。 然后从列表中选择 " **AzureVnet** "。
6. 选择“公共 IP 地址”。 当 "**选择公共 IP 地址**" 部分打开时，选择 "**新建**"。
7. 在 "**名称**" 字段中，键入**Azure-GW-PiP**，然后选择 **"确定"** 。
8. 验证“订阅”和“位置”是否正确。 可以将资源固定到仪表板。 选择“创建”。

### <a name="create-the-local-network-gateway-resource"></a>创建本地网络网关资源

1. 在 Azure 门户中，选择 " **+ 创建资源**"。

2. 中转到 " **Marketplace**"，然后选择 "**网络**"。
3. 从资源列表中选择 "**本地网络网关**"。
4. 在 "**名称**" 字段中，键入**Azs-GW**。
5. 在 " **IP 地址**" 字段中，键入之前在网络配置表中列出的 Azure Stack 中心虚拟网络网关的公共 IP 地址。
6. 在 "**地址空间**" 字段中，从 Azure Stack 中心键入用于**AzureVNet**的**10.1.0.0/24**和**10.1.1.0/24**地址空间。
7. 验证你的**订阅**、**资源组**和**位置**是否正确，然后选择 "**创建**"。

## <a name="create-the-connection"></a>创建连接

1. 在用户门户中，选择 " **+ 创建资源**"。
2. 中转到 " **Marketplace**"，然后选择 "**网络**"。
3. 从资源列表中选择 "**连接**"。
4. 在 "**基本**设置" 部分中，为 "**连接类型**" 选择 "**站点到站点（IPSec）** "。
5. 选择 "**订阅**"、"**资源组**" 和 "**位置**"，然后选择 **"确定"** 。
6. 在 "**设置**" 部分，选择 "**虚拟网络网关**"，然后选择 " **Azure-GW**"。
7. 选择 "**本地网络网关**"，然后选择 " **Azs**"。
8. 在 "**连接名称**" 中，键入 " **Azs**"。
9. 在 "**共享密钥（PSK）** " 中，键入**12345**，然后选择 **"确定"** 。

   >[!NOTE]
   >如果对共享密钥使用其他值，请记住，它必须与你在连接的另一端上创建的共享密钥的值匹配。

10. 查看 "**摘要**" 部分，然后选择 **"确定"** 。

## <a name="create-a-vm"></a>创建 VM

现在，在 Azure 中创建 VM，并将其放在虚拟网络中的 VM 子网上。

1. 在 Azure 门户中，选择 " **+ 创建资源**"。
2. 选择 "应用**商店**"，然后选择 "**计算**"。
3. 在 VM 映像列表中，选择 " **Windows Server 2016 Datacenter Eval** " 映像。
4. 在 "**基本**信息" 部分，为 "**名称**" 键入 " **add-azurevm**"。
5. 键入有效的用户名和密码。 创建 VM 后，可以使用此帐户登录到该 VM。
6. 提供 "**订阅**"、"**资源组**" 和 "**位置**"，然后选择 **"确定"** 。
7. 在 "**大小**" 部分，为此实例选择 VM 大小，然后选择 "**选择**"。
8. 在 "**设置**" 部分中，可以使用默认设置。 选择 **"确定"** 之前，请确认：

   * 已选择**AzureVnet**虚拟网络。
   * 子网已设置为**10.100.0.0/24**。

   选择“确定”。

9. 查看 "**摘要**" 部分中的设置，然后选择 **"确定"** 。

## <a name="create-the-network-resources-in-azure-stack-hub"></a>在 Azure Stack 集线器中创建网络资源

接下来，在 Azure Stack 集线器中创建网络资源。

### <a name="sign-in-as-a-user"></a>以用户身份登录

服务管理员可以以用户身份登录，以测试其用户可能使用的计划、产品/服务和订阅。 如果尚未登录，请先[创建一个用户帐户](../operator/azure-stack-add-new-user-aad.md)。

### <a name="create-the-virtual-network-and-a-vm-subnet"></a>创建虚拟网络和 VM 子网

1. 使用用户帐户登录到用户门户。
2. 在用户门户中，选择 " **+ 创建资源**"。

    ![创建新的虚拟网络](media/azure-stack-connect-vpn/image3.png)

3. 中转到 " **Marketplace**"，然后选择 "**网络**"。
4. 选择“虚拟网络”。
5. 对于 "**名称**"、"**地址空间**"、"**子网名称**" 和 "**子网地址范围**"，请使用网络配置表中的值。
6. 在**订阅**中，你之前创建的订阅将会出现。
7. 对于 "**资源组**"，可以创建资源组，也可以选择 "**使用现有**项"。
8. 验证默认位置。
9. 选择“固定到仪表板”。
10. 选择“创建”。

### <a name="create-the-gateway-subnet"></a>创建网关子网

1. 在仪表板上，打开创建的 Azs 虚拟网络资源。
2. 在 "**设置**" 部分，选择 "**子网**"。
3. 若要将网关子网添加到虚拟网络，请选择 "**网关子**网"。

    ![添加网关子网](media/azure-stack-connect-vpn/image4.png)

4. 默认情况下，子网名称设置为**GatewaySubnet**。 要使网关子网正常工作，必须使用**GatewaySubnet**名称。
5. 在 "**地址范围**" 中，验证地址是否为**10.1.1.0/24**。
6. 选择 **"确定"** 创建网关子网。

### <a name="create-the-virtual-network-gateway"></a>创建虚拟网络网关

1. 在 Azure Stack 中心门户中，选择 " **+ 创建资源**"。
2. 中转到 " **Marketplace**"，然后选择 "**网络**"。
3. 从网络资源列表中，选择 "**虚拟网络网关**"。
4. 在 "**名称**" 中，键入**Azs-GW**。
5. 选择**虚拟网络**项目以选择一个虚拟网络。 从列表中选择**Azs-VNet** 。
6. 选择 "**公共 IP 地址**" 菜单项。 当 "**选择公共 IP 地址**" 部分打开时，选择 "**新建**"。
7. 在 "**名称**" 中，键入**Azs**，然后选择 **"确定"** 。
8. 默认情况下，为**VPN 类型**选择 "**基于路由**"。 保留**基于路由**的 VPN 类型。

9. 验证“订阅”和“位置”是否正确。 可以将资源固定到仪表板。 选择“创建”。

### <a name="create-the-local-network-gateway"></a>创建本地网关

Azure Stack 集线器中的*本地网络网关*的概念与在 Azure 部署中不同。

在 Azure 部署中，本地网关代表连接到 Azure 中的虚拟网络网关的本地（在用户位置）物理设备。 但是，在 Azure Stack 中心，连接的两端都是虚拟网络网关。

更一般的说明是，本地网络网关资源始终指示连接的另一端的远程网关。

### <a name="create-the-local-network-gateway-resource"></a>创建本地网络网关资源

1. 登录到 Azure Stack 中心门户。
2. 在用户门户中，选择 " **+ 创建资源**"。
3. 中转到 " **Marketplace**"，然后选择 "**网络**"。
4. 从资源列表中选择 "**本地网络网关**"。
5. 在 "**名称**" 字段中，键入**Azure-GW**。
6. 在 " **IP 地址**" 字段中，键入 azure 中虚拟网络网关的公共 IP 地址 **-GW-PiP**。 此地址显示在网络配置表的前面。
7. 在 "**地址空间**" 字段中，对于创建的 Azure VNET 的地址空间，键入**10.100.0.0/24**和**10.100.1.0/24**。

8. 验证 "**订阅**"、"**资源组**" 和 "**位置**" 值正确，然后选择 "**创建**"。

### <a name="create-the-connection"></a>创建连接

1. 在用户门户中，选择 " **+ 创建资源**"。
2. 中转到 " **Marketplace**"，然后选择 "**网络**"。
3. 从资源列表中选择 "**连接**"。
4. 在 "**基本**设置" 部分中，为 "**连接类型**" 选择 "**站点到站点（IPSec）** "。
5. 选择 "**订阅**"、"**资源组**" 和 "**位置**"，然后选择 **"确定"** 。
6. 在 "**设置**" 部分，选择 "**虚拟网络网关**"，然后选择 " **Azs**"。
7. 选择 "**本地网络网关**"，然后选择 " **Azure-GW**"。
8. 在 "**连接名称**" 中，键入**Azs-Azure**。
9. 在 "**共享密钥（PSK）** " 中，键入**12345**，然后选择 **"确定"** 。

10. 在 "**摘要**" 部分，选择 **"确定"** 。

### <a name="create-a-vm"></a>创建 VM

若要检查 VPN 连接，请创建两个 Vm：一个在 Azure 中，另一个在 Azure Stack 集线器中。 创建这些 Vm 后，可以使用它们通过 VPN 隧道来发送和接收数据。

1. 在 Azure 门户中，选择 " **+ 创建资源**"。
2. 选择 "应用**商店**"，然后选择 "**计算**"。
3. 在 VM 映像列表中，选择 " **Windows Server 2016 Datacenter Eval** " 映像。
4. 在 "**基本**信息" 部分的 "**名称**" 中，键入**Azs**。
5. 键入有效的用户名和密码。 创建 VM 后，可以使用此帐户登录到该 VM。
6. 提供 "**订阅**"、"**资源组**" 和 "**位置**"，然后选择 **"确定"** 。
7. 在 "**大小**" 部分，为此实例选择 VM 大小，然后选择 "**选择**"。
8. 在 "**设置**" 部分，接受默认值。 请确保选择**Azs**虚拟网络。 验证子网是否设置为**10.1.0.0/24**。 然后选择“确定”。

9. 在 "**摘要**" 部分中，查看设置，然后选择 **"确定"** 。

## <a name="test-the-connection"></a>测试连接

建立站点到站点连接后，您应该验证是否可以在这两个方向上流动数据。 测试连接的最简单方法是执行 ping 测试：

* 登录到在 Azure Stack Hub 中创建的 VM，并在 Azure 中对 VM 执行 ping 操作。
* 登录到在 Azure 中创建的 VM，并在 Azure Stack Hub 中对 VM 执行 ping 操作。

>[!NOTE]
>若要确保通过站点到站点连接发送流量，请对远程子网上的 VM 的直接 IP （DIP）地址进行 ping 操作，而不是 VIP。

### <a name="sign-in-to-the-user-vm-in-azure-stack-hub"></a>在 Azure Stack 中心登录到用户 VM

1. 登录到 Azure Stack 中心门户。
2. 在左侧导航栏中，选择 "**虚拟机**"。
3. 在 Vm 列表中，找到之前创建的**Azs-vm** ，然后选择它。
4. 在 VM 的节中，选择 "**连接**"，然后打开 Azs-VM 文件。

     ![连接按钮](media/azure-stack-connect-vpn/image17.png)

5. 用创建 VM 时配置的帐户登录。
6. 打开提升的 Windows PowerShell 提示符。
7. 键入 **ipconfig /all**。
8. 在输出中，找到**IPv4 地址**，然后保存该地址供以后使用。 这是从 Azure ping 的地址。 在示例环境中，地址是**10.1.0.4**，但在您的环境中，它可能不同。 它应位于你之前创建的**10.1.0.0/24**子网中。
9. 若要创建允许 VM 响应 ping 的防火墙规则，请运行以下 PowerShell 命令：

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

### <a name="sign-in-to-the-tenant-vm-in-azure"></a>登录到 Azure 中的租户 VM

1. 登录到 Azure 门户。
2. 在左侧导航栏中，选择 "**虚拟机**"。
3. 在 Vm 列表中，找到之前创建的**AZURE vm** ，然后选择它。
4. 在 VM 的部分，选择 "**连接**"。
5. 用创建 VM 时配置的帐户登录。
6. 打开提升的**Windows PowerShell**窗口。
7. 键入 **ipconfig /all**。
8. 应会看到位于**10.100.0.0/24**内的 IPv4 地址。 在示例环境中，地址是**10.100.0.4**，但你的地址可能不同。
9. 若要创建允许 VM 响应 ping 的防火墙规则，请运行以下 PowerShell 命令：

   ```powershell
   New-NetFirewallRule `
    -DisplayName "Allow ICMPv4-In" `
    -Protocol ICMPv4
   ```

10. 从 Azure 中的 VM，通过隧道 ping Azure Stack 集线器中的 VM。 为此，请对从 Azs 中记录的 DIP 进行 ping 操作。 在示例环境中，这是**10.1.0.4**，但请务必对实验室中记下的地址执行 ping 操作。 应会看到类似于以下屏幕截图的结果：

    ![Ping 成功](media/azure-stack-connect-vpn/image19b.png)

11. 来自远程 VM 的答复表示测试成功。 可以关闭 VM 窗口。

还应执行更严格的数据传输测试（例如，在两个方向复制大小不同的文件）。

### <a name="viewing-data-transfer-statistics-through-the-gateway-connection"></a>通过网关连接查看数据传输统计信息

如果要了解通过站点到站点连接的数据量，可以在 "**连接**" 部分获取此信息。 此测试也是验证刚发送的 ping 是否确实通过了 VPN 连接的另一种方法。

1. 登录到 Azure Stack 中心内的用户 VM 时，请使用用户帐户登录到用户门户。
2. 中转到 "**所有资源**"，然后选择 " **Azs-Azure**连接"。 **连接**随即出现。
3. 在 "**连接**" 部分中，将显示**中的数据**和**数据输出**的统计信息。 在下面的屏幕截图中，较大的数字将被附加到文件传输。 应会在此处看到一些非零值。

    ![传入和传出数据](media/azure-stack-connect-vpn/Connection.png)

## <a name="next-steps"></a>后续步骤

* [将应用部署到 Azure 和 Azure Stack 中心](azure-stack-solution-pipeline.md)
