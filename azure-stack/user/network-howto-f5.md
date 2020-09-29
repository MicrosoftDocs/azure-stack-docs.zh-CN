---
title: 跨两个 Azure Stack 中心实例部署 F5
description: 了解如何跨两个 Azure Stack Hub 实例部署 F5。
author: mattbriggs
ms.topic: how-to
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: 3512584e4109d86a9ee6a0d71924d560b9ecba92
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90574204"
---
# <a name="how-to-deploy-f5-across-two-azure-stack-hub-instances"></a>如何跨两个 Azure Stack Hub 实例部署 F5

本文逐步讲解如何在两个 Azure Stack Hub 环境中设置外部负载均衡器。 你可使用此配置管理不同的工作负载。 在本文中，你将跨两个独立的 Azure Stack Hub 实例将 F5 部署为全局负载均衡解决方案。 你还将跨两个实例部署在 NGINX 服务器中运行的负载均衡的 Web 应用。 它们将在 F5 虚拟设备的高可用性故障转移对之后运行。

你可在 [f5-azurestack-gslb](https://github.com/Mikej81/f5-azurestack-gslb) GitHub 存储库中找到 Azure 资源管理器模板。

## <a name="overview-of-load-balancing-with-f5"></a>F5 负载均衡概述

F5 硬件（负载均衡器）可能在 Azure Stack Hub 外部，位于托管 Azure Stack Hub 的数据中心内。 Azure Stack Hub 没有用于跨两个单独的 Azure Stack Hub 部署对工作负载进行负载均衡的本机功能。 F5 的 BIG-IP 虚拟版 (VE) 在两个平台上运行。 此设置通过复制支持应用程序服务来支持 Azure 和 Azure Stack Hub 体系结构之间的奇偶校验。 你可在一个环境中开发应用，然后将其移至另一个环境。 你还可对整个生产就绪的 Azure Stack Hub 执行镜像操作，包括相同的 BIG-IP 配置、策略和应用程序服务。 借助该方法，不再需要进行无数小时的应用程序重构和测试，并能够专注于编写代码。

保护应用程序及其数据通常是开发人员在将应用移至公有云时要考虑的问题。 但不一定要这样做。 你可在 Azure Stack Hub 环境中生成应用，而安全架构师则在 F5 的 Web 应用程序防火墙 (WAF) 上配置必要的设置。 因为知道应用程序将受到相同的行业领先 WAF 的保护，所以可在 Azure Stack Hub 中复制整个堆栈。 使用相同的策略和规则集，就不会存在因使用不同的 WAF 而可能会产生的任何安全薄弱环节或漏洞。

Azure Stack Hub 具有独立于 Azure 的市场。 仅可添加特定项目。 在这种情况下，你可在每个 Azure Stack Hub 上创建一个新的资源组并部署已经可用的 F5 虚拟设备。 在此处，你将看到需要提供**公共 IP** 地址用于允许两个 Azure Stack Hub 实例之间的网络连接。 实质上，它们都是孤岛，而**公共 IP** 可让它们在两个位置之间进行通信。

## <a name="prerequisites-for-big-ip-ve"></a>BIG-IP VE 先决条件

-  将 **F5 BIG-IP VE - ALL (BYOL, 2 Boot Locations)** 下载到每个 Azure Stack Hub 市场中。 如果它们在门户中不可用，请联系云操作员。

-  可在以下 GitHub 存储库中找到 Azure 资源管理器模板： https://github.com/Mikej81/f5-azurestack-gslb 。

## <a name="deploy-f5-big-ip-ve-on-each-instance"></a>在每个实例上部署 F5 BIG-IP VE

部署到 Azure Stack Hub 实例 A 和实例 B。

1. 登录到 Azure Stack Hub 用户门户。

2. 选择“+ 创建资源”。 

3. 通过键入 `F5` 来搜索市场。

4. 选择“F5 BIG-IP VE – ALL (BYOL, 2 Boot Locations)”  。

    !["仪表板 > 新的 > Marketplace > 所有内容 > F5 大 IP VE –所有 (BYOL，2个启动位置) " 对话框在搜索框中显示 F5。 单个搜索结果为 "F5 大 IP VE –所有 (BYOL，2个启动位置) "。](./media/network-howto-f5/image1.png)

5. 在下一页的底部，选择 " **创建**"。

    !["F5 大 IP VE – ALL (BYOL，2 Boot 位置) " 对话框提供了有关大 IP VE 和可部署的模块的信息，具体取决于你的许可证。 有 "创建" 按钮。](./media/network-howto-f5/image2.png)

6. 创建一个名为“F5-GSLB”的新资源组****。

7. 使用以下值作为示例来完成部署：

    !["VIRTUALMACHINENAME" 对话框的 "输入" 页显示15个文本框，如 "" 和 "ADMINUSERNAME"，其中包含示例部署的值。](./media/network-howto-f5/image3.png)

8. 验证部署是否成功完成。

    !["Microsoft 模板" 对话框的 "概述" 页报告 "您的部署已完成"，并提供有关部署的详细信息。](./media/network-howto-f5/image4.png)

    > [!NOTE]  
    > 每个 BIG-IP 部署大约需要 20 分钟。

## <a name="configure-big-ip-appliances"></a>配置 BIG-IP 设备

对于 Azure Stack 集线器 A 和 B，请遵循以下步骤。

1. 登录到 Azure Stack 中心实例 A 上的 Azure Stack 集线器用户门户，查看通过大 IP 模板部署创建的资源。

    !["GSLB" 对话框的 "概述" 页列出了部署的资源和关联的信息。](./media/network-howto-f5/image18.png)

2. 遵循 F5 上有关 [BIG-IP 配置项](https://clouddocs.f5.com/training/community/dns/html/class1/class1.html)的说明。 

3. 配置大 IP 范围的 IP 列表，在部署到 Azure Stack 集线器实例 A 和 B 的两个设备之间进行侦听。有关说明，请参阅 [大 IP 的操作配置](https://techdocs.f5.com/kb/en-us/products/big-ip_gtm/manuals/product/gtm-concepts-11-5-0/4.html)。


4. 验证 BIG-IP 设备的故障转移。 在测试系统上，将 DNS 服务器配置为使用以下值：
    - Azure Stack 集线器实例 A = `f5stack1-ext` 公共 IP 地址
    - Azure Stack 中心实例 B = `f5stack1-ext` 公共 IP 地址

5. 浏览到 `www.contoso.com`，浏览器会加载 NGINX 默认页。

## <a name="create-a-dns-sync-group"></a>创建 DNS 同步组

1. 启用根帐户以建立信任。 请遵循[更改系统维护帐户密码 (11.x-15.x)](https://support.f5.com/csp/article/K13121) 中的说明。 设置信任（证书交换）后，请禁用根帐户。

1. 登录到 BIG-IP 并创建 DNS 同步组。 有关说明，请参阅[创建 BIG-IP DNS 同步组](https://f5-dns-automation-demo-12-1-x.readthedocs.io/en/latest/lab2/sync-group.html)。

    > [!NOTE]  
    > 可在 **F5-GSLB** 资源组中找到 BIP-IP 设备的本地 IP。 网络接口为“f5stack1-ext”，而你可连接到公共 IP 或专用 IP（取决于访问权限）。

    !["DNS >> GSLB：数据中心：数据中心列表" 对话框列出了数据中心和状态。 对于所选的数据中心，有 "启用"、"禁用" 和 "删除" 按钮适用。](./media/network-howto-f5/image5.png)
          
    !["DNS >> GSLB：服务器：服务器列表" 对话框列出服务器和状态。 对于所选服务器，有 "启用"、"禁用"、"删除" 和 "重新连接" 按钮适用。](./media/network-howto-f5/image6.png)

1. 选择新资源组“F5-GSLB”并选择“f5stack1”虚拟机，然后在“设置”下选择“网络”****************。

## <a name="post-install-configurations"></a>安装后配置

安装之后，需要配置 Azure Stack 集线器 Nsg 并锁定源 IP 地址。

1. 建立信任后，禁用端口 22。

2. 当系统联机时，阻止源 NSG。 管理 NSG 应锁定到管理源，外部 (4353/TCP) NSG 应锁定到其他实例进行同步。在部署具有虚拟服务器的应用程序之前，443也应被锁定。

3. GTM_DNS 规则设置为允许端口 53 (DNS) 流量进入，且 BIG-IP 解析程序将立即开始工作。 侦听器已创建。

    !["网络接口" 对话框的 "fStack1" 页显示有关 fStack1 接口的信息，以及有关其 NSG，fStack1-NSG 的信息。 有选项卡可供选择查看入站端口规则或出站端口规则。](./media/network-howto-f5/image7.png)

4. 在 Azure Stack 中心环境中部署基本 web 应用程序工作负载，以便在大 IP 后进行负载平衡。 可从[在 Docker 上部署 NGINX 和 NGINX Plus](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-docker/) 中找到使用 NGNIX 服务器的示例。

    > [!NOTE]  
    > 在 Azure Stack 中心 A 和 Azure Stack 中心 B 上部署 NGNIX 的实例。

5. 将 NGINX 部署到每个 Azure Stack 集线器实例内 Ubuntu VM 的 docker 容器中后，验证是否可以访问服务器上的默认网页。

    !["欢迎使用 nginx！" 页面指示已成功安装 nginx web 服务器，并且需要进一步的配置。 提供了两个链接来支持信息。](./media/network-howto-f5/image8.png)

6. 登录到 BIG-IP 设备的管理界面。 在此示例中，使用“f5-stack1-ext”公共 IP 地址****。

    ![大 IP 配置实用工具的登录屏幕要求提供用户名和密码。](./media/network-howto-f5/image9.png)

7. 通过 BIG-IP 公开访问 NGINX。
    
    -  在此任务中，将使用虚拟服务器和池配置 BIG-IP，以允许对 WordPress 应用程序的入站 Internet 访问。 首先，需要为 NGINX 实例标识专用 IP 地址。

8. 登录到 Azure Stack Hub 用户门户。 

9. 选择 NGINX 网络接口。

    !["仪表板 > 资源组 > NGINX > ubuntu2673" 对话框的 "概述" 页显示有关 ubuntu2673 网络接口的信息。](./media/network-howto-f5/image10.png)

10. 在 BIG-IP 控制台中，转到“本地流量”>“池”>“池列表”，然后选择“+”********。 使用表中的值配置池。 将所有其他字段保留为默认值。

    ![左窗格提供导航以创建新池的功能。 右窗格标题为 "本地流量 >> 池：池列表 >> 新池"，并提供指定新池相关信息的功能。 有 "完成" 按钮。](./media/network-howto-f5/image11.png)
    
    | 密钥 | 值 |
    | --- | --- |
    | 名称 | NGINX_Pool |
    | 运行状况监视器 | HTTPS |
    | 节点名称 | NGINX |
    | 地址 | \<your NGINX private IP address> |
    | 服务端口 | 443 |

11. 选择“完成”。 如果正确配置，池状态为绿色。

    ![右窗格标题为 "本地流量 >> 池：池列表"，新创建的池是列表中的唯一条目。](./media/network-howto-f5/image12.png)

    现在，你需要配置虚拟服务器。 为此，你首先需要找到 F5 BIG-IP 的专用 IP。

12. 在 BIG-IP 控制台中，转到“网络”>“Self IP”并记下 IP 地址****。

    ![左窗格提供导航以显示自 Ip 的功能。 右窗格标题为 "Network >> 己方 Ip"。 将列出两个自 Ip，并突出显示第一个 self_2nic。](./media/network-howto-f5/image13.png)

13. 转到 "**本地流量**" "虚拟服务器"  >  **Virtual Servers**  >  **列表**，然后选择 "创建虚拟服务器" **+** 。 使用表中的值配置池。 将所有其他字段保留为默认值。

    | 密钥 | 值 |
    | --- | --- |
    |名称 | NGINX |
    |目标地址 | \<Self IP address of the BIG-IP> |
    |服务端口 | 443 |
    |SSL 配置文件（客户端） | clientssl |
    |源地址转换 | 自动映射 |
        
    ![左窗格用于在右窗格中导航到 "本地流量 >> 虚拟服务器： Virtual Server List >> NGINX"，其中输入所需的信息。](./media/network-howto-f5/image14.png)

    ![此页提供了输入其他信息的能力。 有更新和删除按钮。](./media/network-howto-f5/image15.png)

14. 现在，你已完成 NGINX 应用程序的 BIG-IP 配置。 若要验证功能是否正常，请浏览站点并验证 F5 统计信息。

15. 打开浏览器以转到 `https://<F5-public-VIP-IP>`，并确保它显示 NGINX 默认页。

    !["欢迎使用 nginx！" 页面指示已成功安装 nginx web 服务器，并且需要进一步的配置。 提供了两个支持信息的链接。](./media/network-howto-f5/image16.png)

16. 现在，通过导航到“统计信息”>“模块统计信息”>“本地流量”检查虚拟服务器的统计信息，从而验证流量****。

17. 在“统计信息类型”下，选择“虚拟服务器”********。

    ![左窗格已导航到 "统计信息 >> 模块统计信息：本地流量 >> 虚拟服务器"，列表显示 NGINX 虚拟服务器和其他服务器。 将突出显示 NGINX。](./media/network-howto-f5/image17.png)


## <a name="for-more-information"></a>更多信息

可找到有关使用 F5 的一些参考文章：

- [使用大 IP DNS 的数据中心可用性服务](https://clouddocs.f5.com/training/community/dns/html/class3/class3.html)
- [使用 HTTP 应用程序部署 BIG-IP 系统](https://www.f5.com/content/dam/f5/corp/global/pdf/deployment-guides/iapp-http-dg.pdf)
- [为 GSLB 创建宽泛 IP](https://clouddocs.f5.com/training/community/big-iq-cloud-edition/html/class10/module2/lab1.html)

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 网络的差异和注意事项](azure-stack-network-differences.md) 
