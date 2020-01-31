---
title: 如何跨两个 Azure Stack 中心实例部署 F5
description: 了解如何跨两个 Azure Stack 中心实例部署 F5。
author: mattbriggs
ms.topic: how-to
ms.date: 11/06/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: 87c924ff78e4209b300bc7fd68e7cc5b59640026
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885337"
---
# <a name="how-to-deploy-f5-across-two-azure-stack-hub-instances"></a>如何跨两个 Azure Stack 中心实例部署 F5

本文逐步讲解如何在两个 Azure Stack 中心环境中设置外部负载均衡器。 你可以使用此配置来管理不同的工作负荷。 本文介绍如何将 F5 作为全局负载均衡解决方案部署在两个独立的 Azure Stack 中心实例上。 你还将在两个实例中部署在 NGINX 服务器上运行的负载平衡 web 应用。 它们将在高可用性、延迟的 F5 虚拟设备上运行。

可以在[test-azurestack-gslb](https://github.com/Mikej81/f5-azurestack-gslb) GitHub 存储库中找到 Azure 资源管理器模板。

## <a name="overview-of-load-balancing-with-f5"></a>带有 F5 的负载均衡概述

F5 硬件（负载平衡器）可以在 Azure Stack 中心外，也可以在承载 Azure Stack 中心的数据中心内。 Azure Stack 中心没有在两个单独的 Azure Stack 中心部署之间对工作负荷进行负载均衡的本机功能。 F5 bigip 大型 IP 虚拟版（VE）在这两个平台上运行。 此设置支持 Azure 与 Azure Stack 中心体系结构之间通过复制支持的应用程序服务进行奇偶校验。 可以在一个环境中开发应用，并将其移到另一个环境中。 还可以镜像整个生产就绪的 Azure Stack 中心，包括相同的大 IP 配置、策略和应用程序服务。 此方法不再需要无数小时的应用程序重构和测试，并使你能够编写代码。

对于将应用程序移动到公有云的开发人员而言，保护应用程序及其数据的工作通常是一个问题。 这不是必需的。 可以在 Azure Stack 中心环境中生成应用程序，而安全体系结构会配置 F5 bigip web 应用程序防火墙（WAF）上的必要设置。 可以将整个堆栈复制到 Azure Stack 中心，了解应用程序将受到相同行业领先 WAF 的保护。 使用相同的策略和规则集，不会有任何安全漏洞或可能通过使用不同的 Waf 生成的漏洞。

Azure Stack 集线器具有独立于 Azure 的 marketplace。 仅添加某些项。 在这种情况下，如果想要在每个 Azure Stack 集线器上创建新的资源组，并部署已有的 F5 虚拟设备。 在这里，你将看到需要**公共 IP**地址才能允许两个 Azure Stack 集线器实例之间建立网络连接。 实质上，它们都是孤岛，**公共 IP**允许它们在两个位置之间进行通信。

## <a name="prerequisites-for-big-ip-ve"></a>大 IP VE 的先决条件

-  将**F5 大 IP VE （BYOL，2个引导位置）** 下载到每个 Azure Stack 中心市场。 如果门户中没有可用的用户，请与云操作员联系。

-  可在以下 GitHub 存储库中找到 Azure 资源管理器模板： https://github.com/Mikej81/f5-azurestack-gslb 。

## <a name="deploy-f5-big-ip-ve-on-each-instance"></a>在每个实例上部署 F5 大 IP VE

部署到 Azure Stack 集线器实例 A 和实例 B。

1. 登录到 Azure Stack 集线器用户门户。

2. 选择“+ 创建资源”。

3. 通过键入 `F5`搜索 marketplace。

4. 选择 **"F5 大 IP VE –所有（BYOL，2个引导位置）"** 。

    ![](./media/network-howto-f5/image1.png)

5. 在下一页的底部，选择 "**创建**"。

    ![](./media/network-howto-f5/image2.png)

6. 创建名为**GSLB 的**新资源组。

7. 使用以下值作为示例来完成部署：

    ![](./media/network-howto-f5/image3.png)

8. 验证你的部署是否成功完成。

    ![](./media/network-howto-f5/image4.png)

    > [!Note]  
    > 每个大 IP 部署需要大约20分钟。

## <a name="configure-big-ip-appliances"></a>配置大 IP 设备

对于 Azure Stack 集线器 A 和 B，请遵循以下步骤。

1. 登录到 Azure Stack 中心实例 A 上的 Azure Stack 集线器用户门户，查看通过大 IP 模板部署创建的资源。

    ![](./media/network-howto-f5/image18.png)

2. 对于[大 IP 配置项目](https://clouddocs.f5.com/training/community/dns/html/class1/class1.html)，请按照 F5 中的说明进行操作。 

3. 配置大 IP 范围的 IP 列表，在部署到 Azure Stack 集线器实例 A 和 B 的两个设备之间进行侦听。有关说明，请参阅[大 IP 的操作配置](https://techdocs.f5.com/kb/en-us/products/big-ip_gtm/manuals/product/gtm-concepts-11-5-0/4.html)。


4. 验证大 IP 设备的故障转移。 在测试系统上，将 DNS 服务器配置为使用以下各项：
    - Azure Stack 中心实例 A = `f5stack1-ext` 的公共 IP 地址
    - Azure Stack 中心实例 B = `f5stack1-ext` 公共 IP 地址

5. 浏览到 `www.contoso.com`，浏览器将加载 NGINX 默认页。

## <a name="create-a-dns-sync-group"></a>创建 DNS 同步组

1. 允许根帐户建立信任。 按照[更改系统维护帐户密码（11. x-15. x）](https://support.f5.com/csp/article/K13121)中的说明进行操作。 设置信任（证书交换）后，禁用根帐户。

1. 登录到大 IP，并创建 DNS 同步组。 有关说明，请参阅[创建大 IP DNS 同步组](https://f5-dns-automation-demo-12-1-x.readthedocs.io/en/latest/lab2/sync-group.html)。

    > [!Note]  
    > 可以在**GSLB**资源组中找到 BIP-IP 设备的本地 ip。 网络接口为 "f5stack1"，并且你想要连接到公共或专用 IP （取决于访问权限）。

    ![](./media/network-howto-f5/image5.png)
          
    ![](./media/network-howto-f5/image6.png)

1. 选择新的资源组**F5-GSLB** ，并选择 " **f5stack1** " 虚拟机，在 "**设置**" 下选择 "**网络**"。

## <a name="post-install-configurations"></a>安装后配置

安装之后，需要配置 Azure Stack 集线器 Nsg 并锁定源 IP 地址。

1. 建立信任关系后禁用端口22。

2. 当系统处于联机状态时，请阻止源 Nsg。 管理 NSG 应锁定到管理源，外部（4353/TCP） NSG 应锁定到其他实例进行同步。在部署具有虚拟服务器的应用程序之前，443也应被锁定。

3. GTM_DNS 规则设置为允许中的端口53（DNS）流量，并且大 IP 解析程序将开始运行一次。 创建侦听器。

    ![](./media/network-howto-f5/image7.png)

4. 在 Azure Stack 中心环境中部署基本 web 应用程序工作负载，以便在大 IP 后进行负载平衡。 可以在[Docker 上的部署 NGINX 和 NGINX Plus](https://docs.nginx.com/nginx/admin-guide/installing-nginx/installing-nginx-docker/)中找到使用 NGNIX 服务器的示例。

    > [!Note]  
    > 在 Azure Stack 中心 A 和 Azure Stack 中心 B 上部署 NGNIX 的实例。

5. 将 NGINX 部署到每个 Azure Stack 集线器实例内 Ubuntu VM 的 docker 容器中后，验证是否可以访问服务器上的默认网页。

    ![](./media/network-howto-f5/image8.png)

6. 登录到大 IP 设备的管理界面。 在此示例中，请使用**stack1-ext**公共 IP 地址。

    ![](./media/network-howto-f5/image9.png)

7. 通过大 IP 发布对 NGINX 的访问权限。
    
    -  在此任务中，将使用虚拟服务器和池配置大 IP，以允许对 WordPress 应用程序进行入站 Internet 访问。 首先需要标识 NGINX 实例的专用 IP 地址。

8. 登录到 Azure Stack 集线器用户门户。 

9. 选择 NGINX 网络接口。

    ![](./media/network-howto-f5/image10.png)

10. 从大 IP 控制台中转到 "**本地流量" > 池 > 池列表**"，然后选择" **+** "。 使用表中的值配置池。 将其他所有字段保留为默认值。

    ![](./media/network-howto-f5/image11.png)
    
    | 密钥 | 值 |
    | --- | --- |
    | 名称 | NGINX_Pool |
    | 运行状况监视器 | HTTPS |
    | 节点名称 | NGINX |
    | 地址 | \<NGINX 专用 IP 地址 > |
    | 服务端口 | 443 |

11. 选择 "**完成**"。 如果配置正确，池状态为绿色。

    ![](./media/network-howto-f5/image12.png)

    现在需要配置虚拟服务器。 为此，首先需要查找 F5 大 IP 的专用 IP。

12. 从大 IP 控制台中转到**Network > 己方**ip，并记下 IP 地址。

    ![](./media/network-howto-f5/image13.png)

13. 通过转到 "**本地流量** **" > 虚拟服务器 "**  > **虚拟服务器" 列表**，然后选择 " **+** " 来创建虚拟服务器。 使用表中的值配置池。 将其他所有字段保留为默认值。

    | 密钥 | 值 |
    | --- | --- |
    |名称 | NGINX |
    |目标地址 | 大 IP > \<自 IP 地址 |
    |服务端口 | 443 |
    |SSL 配置文件（客户端） | clientssl |
    |源地址转换 | 自动地图 |
        
    ![](./media/network-howto-f5/image14.png)

    ![](./media/network-howto-f5/image15.png)

14. 你现在已经完成了 NGINX 应用程序的大 IP 配置。 若要验证正确的功能，请浏览网站并验证 F5 统计信息。

15. 打开浏览器 `https://<F5-public-VIP-IP>`，确保它显示 NGINX 的默认页面。

    ![](./media/network-howto-f5/image16.png)

16. 现在，通过导航到 "统计信息" **> 模块统计信息 > 本地流量**来检查虚拟服务器的统计信息。

17. 在 "**统计信息类型**" 下，选择 "**虚拟服务器**"。

    ![](./media/network-howto-f5/image17.png)


## <a name="for-more-information"></a>有关

您可以找到有关使用 F5 的一些参考文章：

- [使用大 IP DNS 的数据中心可用性服务](https://clouddocs.f5.com/training/community/dns/html/class3/class3.html)
- [向 HTTP 应用程序部署大 IP 系统](https://www.f5.com/content/dam/f5/corp/global/pdf/deployment-guides/iapp-http-dg.pdf)
- [为 GSLB 创建宽 IP](https://clouddocs.f5.com/training/community/big-iq-cloud-edition/html/class10/module2/lab1.html)

## <a name="next-steps"></a>后续步骤

[Azure Stack 集线器网络的差异和注意事项](azure-stack-network-differences.md) 
