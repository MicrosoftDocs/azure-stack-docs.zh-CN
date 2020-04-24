---
title: 如何在 Azure Stack Hub 中设置多个站点到站点 VPN 隧道
description: 了解如何在 Azure Stack Hub 中设置多个站点到站点 VPN 隧道。
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: ccfab1377a21c9de1df46c9695d10e4afab84dea
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661500"
---
# <a name="how-to-set-up-a-multiple-site-to-site-vpn-tunnel-in-azure-stack-hub"></a>如何在 Azure Stack Hub 中设置多个站点到站点 VPN 隧道

本文介绍如何使用 Azure Stack Hub 资源管理器模板部署解决方案。 该解决方案使用关联的虚拟网络创建多个资源组，并演示如何连接这些系统。

可以在 [Azure 智能边缘模式](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) GitHub 存储库中找到这些模板。 该模板位于 **rras-gre-vnet-vnet** 文件夹中。 

## <a name="scenarios"></a>方案

![](./media/azure-stack-network-howto-vpn-tunnel/scenarios.png)

## <a name="create-multiple-vpn-tunnels"></a>创建多个 VPN 隧道

![](./media/azure-stack-network-howto-vpn-tunnel/image1.png)

-  部署三层式应用程序：Web 层、应用层和数据库层。

-  将前两个模板部署在单独的 Azure Stack Hub 实例上。

-  **WebTier** 部署在 PPE1 上，**AppTier** 部署在 PPE2 上。

-  使用 IKE 隧道来连接 **WebTier** 和 **AppTier**。

-  将 **AppTier** 连接到你要在其中调用 **DBTier** 的本地系统。

## <a name="steps-to-deploy-multiple-vpns"></a>部署多个 VPN 的步骤

此过程包括多个步骤。 对于此解决方案，你将使用 Azure Stack Hub 门户。 但是，也可以使用 PowerShell、Azure CLI 或其他基础结构即代码工具链来捕获输出并将其用作输入。

![替换文字](./media/azure-stack-network-howto-vpn-tunnel/image2.png)

## <a name="walkthrough"></a>演练

### <a name="deploy-web-tier-to-azure-stack-hub-instances-ppe1"></a>将 Web 层部署到 Azure Stack Hub 实例 PPE1

1.  打开 Azure Stack Hub 用户门户，选择“创建资源”。 

2.  选择“模板部署”。 

    ![](./media/azure-stack-network-howto-vpn-tunnel/image3.png)

3.  将 **azure-intelligent-edge-patterns/rras-vnet-vpntunnel** 存储库中 azuredeploy.json 的内容复制并粘贴到模板窗口中。 此时会显示包含在模板中的资源。选择“保存”。 

    ![](./media/azure-stack-network-howto-vpn-tunnel/image4.png)

4.  输入**资源组**名称并检查参数。

    > ![Note]  
    > WebTier 地址空间将是 **10.10.0.0/16**，可以看到资源组位置是 **PPE1**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image5.png)

### <a name="deploy-app-tier-to-the-second-azure-stack-hub-instances"></a>将应用层部署到第二个 Azure Stack Hub 实例

可以使用与 **WebTier** 相同的过程，但要使用不同的参数，如下所示：

> [!Note]  
> AppTier 地址空间将是**10.20.0.0/16** ，你会看到 "资源组位置" 为 " **WestUS2**"。

![](./media/azure-stack-network-howto-vpn-tunnel/image6.png)

### <a name="review-the-deployments-for-web-tier-and-app-tier-and-capture-outputs"></a>查看 Web 层和应用层的部署并捕获输出

1.  查看部署是否已成功完成。 选择“输出”。 

    ![](./media/azure-stack-network-howto-vpn-tunnel/image7.png)

3.  将前四个值复制到记事本应用中。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image8.png)

5.  针对 **AppTier** 部署重复相同的操作。

![](./media/azure-stack-network-howto-vpn-tunnel/image9.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image10.png)

### <a name="create-tunnel-from-web-tier-to-app-tier"></a>创建从 Web 层到应用层的隧道

1.  打开 Azure Stack Hub 用户门户，选择“创建资源”。 

2.  选择“模板部署”。 

3.  粘贴 **azuredeploy.tunnel.ike.json** 的内容。

4.  选择“编辑参数”。 

![](./media/azure-stack-network-howto-vpn-tunnel/image11.png)

### <a name="create-tunnel-from-app-tier-to-web-tier"></a>创建从应用层到 Web 层的隧道

1.  打开 Azure Stack Hub 用户门户，选择“创建资源”。 

2.  选择“模板部署”。 

3.  粘贴 **azuredeploy.tunnel.ike.json** 的内容。

4.  选择“编辑参数”。 

![](./media/azure-stack-network-howto-vpn-tunnel/image12.png)

### <a name="viewing-tunnel-deployment"></a>查看隧道部署

查看自定义脚本扩展的输出时，可以看到正在创建隧道，输出中应会显示状态。 你将看到，其中一端显示 **connecting** 并正在等待另一端准备就绪，而另一端在部署后会显示 **connected**。

![](./media/azure-stack-network-howto-vpn-tunnel/image13.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image14.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image15.png)

### <a name="troubleshooting-on-the-rras-vm"></a>RRAS VM 故障排除

1.  将 RDP 规则从“拒绝”更改为“允许”。  

2.  使用部署期间设置的凭据，通过远程桌面 (DRP) 客户端连接到系统。

3.  使用权限提升的提示符打开 PowerShell，并运行 `get-VPNS2SInterface`。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image16.png)

5.  使用 **RemoteAccess** cmdlet 来管理系统。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image17.png)

### <a name="install-rras-on-a-on-premises-vm-db-tier"></a>在本地 VM 数据库层上安装 RRAS

1.  目标为 Windows 2016 映像。

2.  如果从存储库复制 `Add-Site2SiteIKE.ps1` 脚本并在本地运行该脚本，它会安装 **WindowsFeature** 和 **RemoteAccess**。

    > [!Note]
    > 根据具体的环境，可能需要重新启动系统。

    请参考本地计算机网络配置。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image18.png)

3.  运行该脚本，并添加从 AppTier 模板部署中记录的 **Output** 参数。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image19.png)

5.  隧道现已完成配置，正在等待 AppTier 连接。

### <a name="configure-app-tier-to-db-tier"></a>配置应用层到数据库层的隧道

1.  打开 Azure Stack Hub 用户门户，选择“创建资源”。 

2.  选择“模板部署”。 

3.  粘贴 **azuredeploy.tunnel.ike.json** 的内容。

4.  选择“编辑参数”。 

    ![](./media/azure-stack-network-howto-vpn-tunnel/image20.png)

5.  检查是否已选择 AppTier，并已将远程内部网络设置为 10.99.0.1。

### <a name="confirm-tunnel-between-app-tier-and-db-tier"></a>确认应用层与数据库层之间的隧道

1.  若要在不登录到 VM 的情况下检查隧道，请运行自定义脚本扩展。

2.  转到 RRAS VM (AppTier)。

3.  依次选择“扩展”、“运行自定义脚本扩展”。  

4.  导航到 **azure-intelligent-edge-patterns/rras-vnet-vpntunnel** 存储库中的 scripts 目录。 选择“Get-VPNS2SInterfaceStatus.ps1”。 

    ![](./media/azure-stack-network-howto-vpn-tunnel/image21.png)

5.  如果启用 RDP 并登录，请打开 PowerShell 并运行 `get-vpns2sinterface`，随即会看到隧道已连接。

    **DBTier**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image22.png)

    **AppTier**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image23.png)

    > [!Note]  
    > 可以测试从第一台计算机到第二台计算机的 RDP 连接，或反之。

    > [!Note]  
    > 若要在本地实施此解决方案，需要将到 Azure Stack Hub 远程网络的路由部署到交换基础结构中，或至少部署到特定 VM 上

### <a name="deploying-a-gre-tunnel"></a>部署 GRE 隧道

对于此模板，本演练使用了 [IKE 模板](network-howto-vpn-tunnel-ipsec.md)。 但是，也可以部署 [GRE 隧道](network-howto-vpn-tunnel-gre.md)。 此隧道提供更高的吞吐量。

过程几乎完全相同。 但是，在将隧道模板部署到现有基础结构时，需要使用另一系统的输出作为前三个输入。 需要知道用作部署目标的资源组（而不是尝试连接到的资源组）的 **LOCALTUNNELGATEWAY**。

![](./media/azure-stack-network-howto-vpn-tunnel/image24.png)

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 网络的差异和注意事项](azure-stack-network-differences.md)  
[如何使用 GRE 创建 VPN 隧道](network-howto-vpn-tunnel-gre.md)  
[如何使用 IPSEC 创建 VPN 隧道](network-howto-vpn-tunnel-ipsec.md)