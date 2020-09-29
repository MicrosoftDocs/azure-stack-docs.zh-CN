---
title: 在 Azure Stack 集线器中设置多个站点到站点 VPN 隧道
description: 了解如何在 Azure Stack Hub 中设置多个站点到站点 VPN 隧道。
author: mattbriggs
ms.topic: how-to
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: dc74c40611dd680b8b0d893e06b6935beae7a5f6
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573898"
---
# <a name="how-to-set-up-a-multiple-site-to-site-vpn-tunnel-in-azure-stack-hub"></a>如何在 Azure Stack Hub 中设置多个站点到站点 VPN 隧道

本文介绍如何使用 Azure Stack Hub 资源管理器模板部署解决方案。 该解决方案使用关联的虚拟网络创建多个资源组，并演示如何连接这些系统。

可以在 [Azure 智能边缘模式](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) GitHub 存储库中找到这些模板。 该模板位于 **rras-gre-vnet-vnet** 文件夹中。 

## <a name="scenarios"></a>方案

![有五种 VPN 方案图解：单个订阅中的两个资源组之间;在两个组之间，每个组在其自己的订阅中;在单独的堆栈实例中的两个组之间;在本地的组和本地资源之间;和多个 VPN 隧道。](./media/azure-stack-network-howto-vpn-tunnel/scenarios.png)

## <a name="create-multiple-vpn-tunnels"></a>创建多个 VPN 隧道

![此关系图显示两个资源组，每个组位于其自己的订阅和堆栈实例中，并通过 VPN 进行连接;这两个组中的一个通过 VPN 连接到本地本地资源。](./media/azure-stack-network-howto-vpn-tunnel/image1.png)

-  部署三层式应用程序：Web 层、应用层和数据库层。

-  将前两个模板部署在单独的 Azure Stack 中心实例上。

-  **WebTier** 部署在 PPE1 上，**AppTier** 部署在 PPE2 上。

-  使用 IKE 隧道来连接 **WebTier** 和 **AppTier**。

-  将 **AppTier** 连接到你要在其中调用 **DBTier** 的本地系统。

## <a name="steps-to-deploy-multiple-vpns"></a>部署多个 VPN 的步骤

此过程包括多个步骤。 对于此解决方案，将使用 Azure Stack 中心门户。 但是，也可以使用 PowerShell、Azure CLI 或其他基础结构即代码工具链来捕获输出并将其用作输入。

![此图显示了在两个基础结构之间部署 VPN 隧道的五个步骤。 前两个步骤根据模板创建两个基础结构。 接下来的两个步骤根据模板创建两个 VPN 隧道，最后一步是连接隧道。](./media/azure-stack-network-howto-vpn-tunnel/image2.png)

## <a name="walkthrough"></a>演练

### <a name="deploy-web-tier-to-azure-stack-hub-instances-ppe1"></a>将 web 层部署到 Azure Stack 中心实例 PPE1

1. 打开 Azure Stack 集线器用户门户，然后选择 " **创建资源**"。

2. 选择 " **模板部署**"。

    !["仪表板 > 新" 对话框显示了各种选项。 将突出显示 "模板部署" 按钮。](./media/azure-stack-network-howto-vpn-tunnel/image3.png)

3. 将 **azure-intelligent-edge-patterns/rras-vnet-vpntunnel** 存储库中 azuredeploy.json 的内容复制并粘贴到模板窗口中。 此时会显示包含在模板中的资源。选择“保存”。****

    !["仪表板 > 新的 > 部署解决方案模板 > 编辑模板" 对话框中有一个用于粘贴 azuredeploy.json 文件的窗口。](./media/azure-stack-network-howto-vpn-tunnel/image4.png)

4. 输入**资源组**名称并检查参数。

    > [!Note]  
    > WebTier 地址空间将是 **10.10.0.0/16**，可以看到资源组位置是 **PPE1**

    !["仪表板 > 新建 > 部署解决方案模板 > 参数" 对话框具有 "资源组" 文本框和单选按钮。 选择 "新建" 按钮，文本为 "WebTier"。](./media/azure-stack-network-howto-vpn-tunnel/image5.png)

### <a name="deploy-app-tier-to-the-second-azure-stack-hub-instances"></a>将应用层部署到第二个 Azure Stack 中心实例

可以使用与 **WebTier** 相同的过程，但要使用不同的参数，如下所示：

> [!NOTE]  
> AppTier 地址空间将是 **10.20.0.0/16** ，你会看到 "资源组位置" 为 " **WestUS2**"。

!["仪表板 > 部署解决方案模板 > 参数" 对话框有一个 "资源组" 文本框和一个单选按钮。 选择 "新建" 按钮，文本为 "AppTier"。 其他八个突出显示的文本框包含模板参数。](./media/azure-stack-network-howto-vpn-tunnel/image6.png)

### <a name="review-the-deployments-for-web-tier-and-app-tier-and-capture-outputs"></a>查看 Web 层和应用层的部署并捕获输出

1. 查看部署是否已成功完成。 选择“输出”。

    !["面板 >" 面板-概述 "对话框中突出显示了" 输出 "选项。 消息为 "你的部署已完成"，后跟组 WebTier 的资源列表，每个资源都具有名称、类型、状态和指向详细信息的链接。](./media/azure-stack-network-howto-vpn-tunnel/image7.png)

1. 将前四个值复制到记事本应用中。

    ![此对话框为 "仪表板 >" 仪表板 "。 将突出显示从 web 层部署中复制的四个文本框： LOCALTUNNELENDPOINT、LOCALVNETADDRESSSPACE、LOCALVNETGATEWAY 和 LOCALTUNNELGATEWAY。](./media/azure-stack-network-howto-vpn-tunnel/image8.png)

1. 针对 **AppTier** 部署重复相同的操作。

    !["面板 >" 面板-概述 "对话框中突出显示了" 输出 "选项。 消息为 "你的部署已完成"，后跟组 AppTier 的资源列表，每个资源都具有名称、类型、状态和指向详细信息的链接。](./media/azure-stack-network-howto-vpn-tunnel/image9.png)

    ![此对话框为 "仪表板 >" 仪表板 "。 将突出显示从应用层部署中复制的四个文本框： LOCALTUNNELENDPOINT、LOCALVNETADDRESSSPACE、LOCALVNETGATEWAY 和 LOCALTUNNELGATEWAY。](./media/azure-stack-network-howto-vpn-tunnel/image10.png)

### <a name="create-tunnel-from-web-tier-to-app-tier"></a>创建从 Web 层到应用层的隧道

1. 打开 Azure Stack 集线器用户门户，然后选择 " **创建资源**"。

2. 选择 " **模板部署**"。

3. 粘贴 **azuredeploy.tunnel.ike.json** 的内容。

4. 选择“编辑参数”。****

!["仪表板 > 新建 > 部署解决方案模板 > 参数" 对话框具有 "资源组" 文本框和单选按钮。 "使用现有的" 按钮已选中，并且文本为 "WebTier"。 其他四个突出显示的文本框包含模板参数。](./media/azure-stack-network-howto-vpn-tunnel/image11.png)

### <a name="create-tunnel-from-app-tier-to-web-tier"></a>创建从应用层到 Web 层的隧道

1. 打开 Azure Stack 集线器用户门户，然后选择 " **创建资源**"。

2. 选择“模板部署”。

3. 粘贴 **azuredeploy.tunnel.ike.json** 的内容。

4. 选择“编辑参数”。****

!["仪表板 > 新建 > 部署解决方案模板 > 参数" 对话框具有 "资源组" 文本框和单选按钮。 "使用现有的" 按钮已选中，并且文本为 "AppTier"。 其他四个突出显示的文本框包含模板参数。](./media/azure-stack-network-howto-vpn-tunnel/image12.png)

### <a name="viewing-tunnel-deployment"></a>查看隧道部署

查看自定义脚本扩展的输出时，可以看到正在创建隧道，输出中应会显示状态。 你将看到，其中一端显示 **connecting** 并正在等待另一端准备就绪，而另一端在部署后会显示 **connected**。

![此对话框为 "仪表板 > 资源组 > WebTier > WebTier"。 列出了两个扩展： CustomScriptExtension，它突出显示，InstallRRAS。 两者都显示 "预配" 状态 "成功"。](./media/azure-stack-network-howto-vpn-tunnel/image13.png)

![此对话框为 "仪表板 > 资源组 > WebTier > WebTier > CustomScriptExtension"。 详细状态值为 "预配成功"。](./media/azure-stack-network-howto-vpn-tunnel/image14.png)

![来自 AppTier 和 WebTier 的自定义脚本扩展的输出显示在 "并排记事本" 窗口中。 AppTier 显示连接的隧道状态。 WebTier 显示已连接。](./media/azure-stack-network-howto-vpn-tunnel/image15.png)

### <a name="troubleshooting-on-the-rras-vm"></a>RRAS VM 故障排除

1. 将远程桌面 (RDP) 规则从 **Deny** 更改为 " **允许**"。

1. 使用在部署过程中设置的凭据，通过 RDP 客户端连接到系统。

1. 使用权限提升的提示符打开 PowerShell，并运行 `get-VpnS2SInterface`。

    ![PowerShell 窗口将显示执行 VpnS2SInterface 命令，该命令显示站点到站点接口的详细信息。 ConnectionState 已连接。](./media/azure-stack-network-howto-vpn-tunnel/image16.png)

1. 使用 **RemoteAccess** cmdlet 来管理系统。

    ![在应用层上运行 cmd.exe 窗口，并显示 ipconfig 命令的输出。 两个窗口显示为覆盖：连接到 Web 层的 RDP 连接窗口，以及连接到 Web 层的 Windows 安全窗口。](./media/azure-stack-network-howto-vpn-tunnel/image17.png)

### <a name="install-rras-on-an-on-premises-vm-db-tier"></a>在本地 VM DB 层上安装 RRAS

1. 目标为 Windows 2016 映像。

1. 如果从存储库复制 `Add-Site2SiteIKE.ps1` 脚本并在本地运行该脚本，它会安装 **WindowsFeature** 和 **RemoteAccess**。

    > [!NOTE]
    > 根据具体的环境，可能需要重新启动系统。

    请参考本地计算机网络配置。

    ![有一个显示 ipconfig 输出的 cmd.exe 窗口和一个 "网络和共享中心" 窗口。](./media/azure-stack-network-howto-vpn-tunnel/image18.png)

1. 运行该脚本，并添加从 AppTier 模板部署中记录的 **Output** 参数。

    ![Add-Site2SiteIKE 脚本将在 PowerShell 窗口中执行，并显示输出。](./media/azure-stack-network-howto-vpn-tunnel/image19.png)

1. 隧道现已完成配置，正在等待 AppTier 连接。

### <a name="configure-app-tier-to-db-tier"></a>配置应用层到数据库层的隧道

1. 打开 Azure Stack 集线器用户门户，然后选择 " **创建资源**"。

2. 选择“模板部署”。

3. 粘贴 **azuredeploy.tunnel.ike.json** 的内容。

4. 选择“编辑参数”。****

    !["仪表板 > 新建 > 部署解决方案模板 > 参数" 对话框具有 "资源组" 文本框和单选按钮。 "使用现有的" 按钮已选中，并且文本为 "AppTier"。 其他三个突出显示的文本框包含模板参数。](./media/azure-stack-network-howto-vpn-tunnel/image20.png)

5. 检查是否已选择 AppTier，并已将远程内部网络设置为 10.99.0.1。

### <a name="confirm-tunnel-between-app-tier-and-db-tier"></a>确认应用层与数据库层之间的隧道

1. 若要在不登录到 VM 的情况下检查隧道，请运行自定义脚本扩展。

2. 转到 RRAS VM (AppTier)。

3. 依次选择“扩展”、“运行自定义脚本扩展”。********

4. 导航到 **azure-intelligent-edge-patterns/rras-vnet-vpntunnel** 存储库中的 scripts 目录。 选择“Get-VPNS2SInterfaceStatus.ps1”。****

    ![此对话框为 "仪表板 > 资源组 > AppTier > AppTier > CustomScriptExtension"。 详细状态的值为 "预配成功"。 有关详细信息，请在记事本窗口覆盖中进行。](./media/azure-stack-network-howto-vpn-tunnel/image21.png)

5. 如果启用 RDP 并登录，请打开 PowerShell 并运行 `get-vpns2sinterface`，随即会看到隧道已连接。

    **DBTier**

    ![在 Partner-dbtier 上，PowerShell 窗口将显示 VpnS2SInterface 命令的执行，其中显示了站点到站点接口的详细信息。 ConnectionState 已连接。](./media/azure-stack-network-howto-vpn-tunnel/image22.png)

    **AppTier**

    ![在 AppTier 上，PowerShell 窗口将显示 VpnS2SInterface 命令的执行，其中显示了站点到站点接口的详细信息。 ConnectionState 连接两个目标。](./media/azure-stack-network-howto-vpn-tunnel/image23.png)

    > [!NOTE]  
    > 可以测试从第一台计算机到第二台计算机的 RDP 连接，或反之。

    > [!NOTE]  
    > 若要在本地实施此解决方案，你将需要将路由到 Azure Stack 集线器远程网络，以便切换基础结构或在特定 Vm 上至少进行部署

### <a name="deploying-a-gre-tunnel"></a>部署 GRE 隧道

对于此模板，本演练使用了 [IKE 模板](network-howto-vpn-tunnel-ipsec.md)。 但是，也可以部署 [GRE 隧道](network-howto-vpn-tunnel-gre.md)。 此隧道提供更高的吞吐量。

过程几乎完全相同。 但是，在将隧道模板部署到现有基础结构时，需要使用另一系统的输出作为前三个输入。 需要知道用作部署目标的资源组（而不是尝试连接到的资源组）的 **LOCALTUNNELGATEWAY**。

!["仪表板 > 新建 > 部署解决方案模板 > 参数" 对话框具有 "资源组" 文本框和单选按钮。 "使用现有的" 按钮已选中，并且文本为 "AppTier"。 其他四个突出显示的文本框包含来自 WebTier 输出的数据。](./media/azure-stack-network-howto-vpn-tunnel/image24.png)

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 网络的差异和注意事项](azure-stack-network-differences.md)  
[如何使用 GRE 创建 VPN 隧道](network-howto-vpn-tunnel-gre.md)  
[如何使用 IPSEC 创建 VPN 隧道](network-howto-vpn-tunnel-ipsec.md)