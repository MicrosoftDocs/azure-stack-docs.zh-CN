---
title: 如何在 Azure Stack 中设置多个站点到站点 VPN 隧道Microsoft Docs
description: 了解如何在 Azure Stack 中设置多个站点到站点 VPN 隧道。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: d85de1892e2e6620249ff3a95ee2debb01b81981
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167659"
---
# <a name="how-to-set-up-a-multiple-site-to-site-vpn-tunnel-in-azure-stack"></a>如何在 Azure Stack 中设置多个站点到站点 VPN 隧道

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*

本文介绍如何使用 Azure Stack 资源管理器模板来部署解决方案。 解决方案会创建多个具有关联的虚拟网络的资源组，以及如何连接这些系统。

可以在[Azure 智能边缘模式](https://github.com/Azure-Samples/azure-intelligent-edge-patterns)GitHub 存储库中找到这些模板。 该模板位于**rras-gre-vnet-vnet**文件夹中。 

## <a name="scenarios"></a>方案

![](./media/azure-stack-network-howto-vpn-tunnel/scenarios.png)

## <a name="create-multiple-vpn-tunnels"></a>创建多个 VPN 隧道

![](./media/azure-stack-network-howto-vpn-tunnel/image1.png)

-  部署三层应用程序、Web 应用程序和数据库。

-  将前两个模板部署在单独的 Azure Stack 实例上。

-  将在 PPE1 上部署**WebTier** ，并在 PPE2 上部署**AppTier** 。

-  将**WebTier**和**APPTIER**连接到 IKE 隧道。

-  将**AppTier**连接到将调用**partner-dbtier**的本地系统。

## <a name="steps-to-deploy-multiple-vpns"></a>部署多个 Vpn 的步骤

这是一个多步骤过程。 对于此解决方案，将使用 Azure Stack 门户。 但是，可以使用 PowerShell、Azure CLI 或其他基础结构即代码工具链来捕获输出，并将其用作输入。

![替换文字](./media/azure-stack-network-howto-vpn-tunnel/image2.png)

## <a name="walkthrough"></a>演练

### <a name="deploy-web-tier-to-azure-stack-instances-ppe1"></a>将 web 层部署到 Azure Stack 实例 PPE1

1.  打开 Azure Stack 用户门户，然后选择 "**创建资源**"。

2.  选择 "**模板部署**"。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image3.png)

3.  将 azuredeploy.json 的内容从**azure 智能边缘模式/rras-vpntunnel**存储库复制并粘贴到模板窗口。 你将看到包含在模板中的资源，请选择 "**保存**"。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image4.png)

4.  输入**资源组**名称并检查参数。

    > !纪录  
    > WebTier 地址空间将是**10.10.0.0/16** ，你会看到 "资源组位置" 为 " **PPE1** "

    ![](./media/azure-stack-network-howto-vpn-tunnel/image5.png)

### <a name="deploy-app-tier-to-the-second-azure-stack-instances"></a>将应用层部署到第二个 Azure Stack 实例

您可以使用与**WebTier**相同的进程，但不同的参数如下所示：

> [!Note]  
> AppTier 地址空间将是**10.20.0.0/16** ，你会看到 "资源组位置" 为 " **WestUS2**"。

![](./media/azure-stack-network-howto-vpn-tunnel/image6.png)

### <a name="review-the-deployments-for-web-tier-and-app-tier-and-capture-outputs"></a>查看 web 层和应用层的部署并捕获输出

1.  查看部署是否已成功完成。 选择 "**输出**"。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image7.png)

3.  将前四个值复制到记事本应用。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image8.png)

5.  对于**AppTier**部署，重复此操作。

![](./media/azure-stack-network-howto-vpn-tunnel/image9.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image10.png)

### <a name="create-tunnel-from-web-tier-to-app-tier"></a>创建从 web 层到应用层的隧道

1.  打开 Azure Stack 用户门户，然后选择 "**创建资源**"。

2.  选择 "**模板部署**"。

3.  将内容粘贴到**azuredeploy.json**中。

4.  选择 "**编辑参数**"。

![](./media/azure-stack-network-howto-vpn-tunnel/image11.png)

### <a name="create-tunnel-from-app-tier-to-web-tier"></a>从应用层到 web 层创建隧道

1.  打开 Azure Stack 用户门户，然后选择 "**创建资源**"。

2.  选择“模板部署”。

3.  将内容粘贴到**azuredeploy.json**中。

4.  选择 "**编辑参数**"。

![](./media/azure-stack-network-howto-vpn-tunnel/image12.png)

### <a name="viewing-tunnel-deployment"></a>查看隧道部署

如果从自定义脚本扩展中查看输出，则可以看到正在创建的隧道，并且应显示状态。 你会看到一个显示**连接**正在等待另一方准备就绪，另一侧将显示已部署的**连接**。

![](./media/azure-stack-network-howto-vpn-tunnel/image13.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image14.png)

![](./media/azure-stack-network-howto-vpn-tunnel/image15.png)

### <a name="troubleshooting-on-the-rras-vm"></a>RRAS VM 的故障排除

1.  将 RDP 规则从**Deny**更改为**Allow**。

2.  使用远程桌面（DRP）客户端连接到系统，并使用在部署过程中设置的凭据。

3.  使用权限提升的提示符打开 PowerShell，并运行 `get-VPNS2SInterface`。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image16.png)

5.  使用**RemoteAccess** cmdlet 来管理系统。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image17.png)

### <a name="install-rras-on-a-on-premises-vm-db-tier"></a>在本地 VM DB 层上安装 RRAS

1.  目标是 Windows 2016 映像。

2.  如果从存储库中复制 `Add-Site2SiteIKE.ps1` 脚本并在本地运行该脚本，则该脚本**将安装该本地和** **远程访问**。

    > [!Note]
    > 根据你的环境，你可能需要重新启动系统。

    有关参考，请参阅本地计算机网络配置。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image18.png)

3.  运行脚本，添加从 AppTier 模板部署记录的**输出**参数。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image19.png)

5.  隧道现在已配置并等待 AppTier 连接。

### <a name="configure-app-tier-to-db-tier"></a>配置应用层到数据库层

1.  打开 Azure Stack 用户门户，然后选择 "**创建资源**"。

2.  选择“模板部署”。

3.  将内容粘贴到**azuredeploy.json**中。

4.  选择 "**编辑参数**"。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image20.png)

5.  检查是否已选择 AppTier，并在10.99.0.1 中设置了远程内部网络。

### <a name="confirm-tunnel-between-app-tier-and-db-tier"></a>确认应用层和数据库层之间的隧道

1.  若要在不登录到 VM 的情况下检查隧道，请运行自定义脚本扩展。

2.  中转到 RRAS VM （AppTier）。

3.  选择**扩展**和 R**取消自定义脚本扩展**。

4.  浏览到**azure 智能边缘模式/rras-vpntunnel**存储库中的 scripts 目录。 选择**Get-VPNS2SInterfaceStatus**。

    ![](./media/azure-stack-network-howto-vpn-tunnel/image21.png)

5.  如果启用 RDP 并登录，请打开 PowerShell 并运行 `get-vpns2sinterface`，可以看到隧道已连接。

    **Partner-dbtier**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image22.png)

    **AppTier**

    ![](./media/azure-stack-network-howto-vpn-tunnel/image23.png)

    > [!Note]  
    > 你可以从一台计算机到第二台计算机测试 RDP，从第二台计算机测试到第一台计算机。

    > [!Note]  
    > 若要在本地实施此解决方案，你需要将路由到 Azure Stack 的远程网络，以切换基础结构或在特定 Vm 上至少部署

### <a name="deploying-a-gre-tunnel"></a>部署 GRE 隧道

对于此模板，本演练使用了[IKE 模板](network-howto-vpn-tunnel-ipsec.md)。 但是，还可以部署[GRE 隧道](network-howto-vpn-tunnel-gre.md)。 此隧道提供更大的吞吐量。

此过程几乎完全相同。 但是，在将隧道模板部署到现有基础结构上时，需要使用另一个系统的输出来实现前三个输入。 需要知道要部署到的资源组的**LOCALTUNNELGATEWAY** ，而不是要尝试连接到的资源组。

![](./media/azure-stack-network-howto-vpn-tunnel/image24.png)

## <a name="next-steps"></a>后续步骤

[Azure Stack 网络的差异和注意事项](azure-stack-network-differences.md)  
[如何使用 GRE 创建 VPN 隧道](network-howto-vpn-tunnel-gre.md)  
[如何使用 IPSEC 创建 VPN 隧道](network-howto-vpn-tunnel-ipsec.md)