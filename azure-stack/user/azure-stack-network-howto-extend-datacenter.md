---
title: 如何在 Azure Stack 上扩展数据中心 |Microsoft Docs
description: 了解如何在 Azure Stack 上扩展数据中心。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: 94a9398a68be06d4735e2c082e8dc0a02281b6eb
ms.sourcegitcommit: 58e1911a54ba249a82fa048c7798dadedb95462b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064786"
---
# <a name="how-to-extend-the-data-center-on-azure-stack"></a>如何在 Azure Stack 上扩展数据中心

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*

本文提供 Azure Stack 存储基础结构信息，以帮助你决定如何将 Azure Stack 集成到现有的网络环境中。 Azure Stack 上的存储空间受到限制。 可以将存储与 Azure Stack 以外的存储集成到本地数据中心。 在提供扩展数据中心的一般讨论后，本文介绍了两种不同的方案。 可以连接到 Windows 文件存储服务器。 你还可以连接到 Windows iSCSI 服务器。

## <a name="overview-of-extending-storage-to-azure-stack"></a>将存储扩展到 Azure Stack 概述

此关系图描述了一个方案，其中，运行工作负荷的单个虚拟机将连接到并利用外部（到 VM 和 Azure Stack 本身）存储，以便进行数据读取/写入等。对于本文，你将重点介绍文件的简单检索，但你可以扩展此示例，以实现更复杂的方案，例如数据库文件的远程存储。

![Azure Stack 扩展存储](./media/azure-stack-network-howto-extend-datacenter/image1.png)

在该图中，你会看到 Azure Stack 系统上的 VM 已部署了多个 Nic。 从这两个冗余中，同时作为存储最佳实践，在目标和目标之间有多个路径很重要。 在某些情况下，Azure Stack 中的 Vm 具有公共和专用 Ip，就像在 Azure 中一样。 如果需要外部存储来访问 VM，则它只能通过公共 IP 来实现，因为专用 ip 主要在 Vnet 和子网内的 Azure Stack 系统中使用。 外部存储将无法与 VM 的专用 IP 空间通信，除非它通过站点到站点 VPN，才能连接到 vNet 本身。 因此，在本示例中，你将重点介绍通过公共 IP 空间进行的通信。 请注意，在图中有公共 IP 空间，有2个不同的公共 IP 池子网。 默认情况下，Azure Stack 只需要一个池来实现公共 IP 地址，但要考虑冗余路由，则可能要添加另一个池。 但是，不能从特定池中选择 IP 地址，因此，你可能会在多个虚拟网卡上最终使用具有相同池的公共 Ip 的 Vm。

在此示例中，你可以假设边界设备和外部存储之间的路由处理，并且流量可以适当地遍历网络。 对于本示例，无论主干是 1 GbE、10 GbE、25 GbE 还是更快，都不重要，但是在规划集成时，请务必考虑到访问此外部存储的任何应用程序的性能需求。

## <a name="connect-to-a-windows-server-file-server-storage"></a>连接到 Windows Server 文件服务器存储

在此方案中，在 Azure Stack 上部署并配置 Windows Server 2019 虚拟机，并使其准备好连接到外部文件服务器，该服务器也可以运行 Windows Server 2019。 在适当的情况下，请设置关键功能（如 SMB 多通道），以便优化 VM 与外部存储之间的性能和连接性。

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack"></a>在 Azure Stack 上部署 Windows Server 2019 VM

1.  在**Azure Stack 管理员门户**中，假设此系统已正确注册并连接到 marketplace，请选择 " **marketplace 管理"** ，如果你还没有 Windows Server 2019 映像，请选择**从 Azure 添加**，然后搜索**windows server 2019**，添加**windows server 2019 Datacenter**映像。

    ![](./media/azure-stack-network-howto-extend-datacenter/image2.png)

    下载 Windows Server 2019 映像可能需要一些时间。

2.  在 Azure Stack 环境中拥有 Windows Server 2019 映像后，请登录 Azure Stack 用户门户 * *。

3.  登录到 Azure Stack 用户门户后，确保你有一个[产品/服务的订阅](https://docs.microsoft.com/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-1908)，以便预配 IaaS 资源（计算、存储和网络）。

4.  获得可用订阅后，请返回 Azure Stack 用户门户中的 "**仪表板**"，选择 "**创建资源**"，选择 "**计算**"，然后选择 " **Windows Server 2019 Datacenter 库" 项**。

5.  在“基本”边栏选项卡上：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。  **名称**： VM001

    b.  **用户名**： "localadmin"

    c.  **密码**和**确认密码**：所选 \<密码 >

    d.单击“下一步”。  **订阅**：你选择的 \<订阅和计算/存储/网络资源 >

    e. **资源组**：新建： storagetesting

    f.  然后选择 **"确定"**

6.  在 "**选择大小**" 边栏选项卡中，选择一个**Standard_F8s_v2**。

7.  在 "**设置**" 边栏选项卡上，选择 "**虚拟网络**"，然后在 "**创建虚拟网络**" 边栏选项卡中，将地址空间调整为**10.10.10.0/23** ，将子网地址范围更新为**10.10.10.0/24** ，然后选择 **"确定"** .

8.  选择 "**公共 ip 地址**"，然后在 "**创建公共 ip 地址**" 边栏选项卡中选择 "**静态**"。

9.  从 "**选择公共入站端口**" 中，选择 " **RDP （3389）** "。

10. 保留其他默认值，然后选择 **"确定"** 。
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image3.png)

11. 阅读摘要，等待验证，然后选择 **"确定"** 开始部署。 部署应在10分钟内完成。

12. 部署完成后，在 "**资源**" 下，选择虚拟机名称**VM001**以进入 "*概述*" 边栏选项卡。
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image4.png)

13. 在 "DNS 名称" 下，选择 "**配置**" 并提供 DNS 名称标签**vm001** ，并选择 "**保存**"，然后在痕迹导航中选择 " **vm001** " 以返回到 "*概述*" 边栏选项卡。

14. 在 "概述" 边栏选项卡的右侧，选择 "虚拟网络/子网" 文本下的**storagetesting/默认值**。

15. 在 storagetesting 边栏选项卡中，依次选择 "**子网**" 和 "**子网**"，然后在新的 "添加子网" 边栏选项卡中输入以下信息： 

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。  **名称**： subnet2

    b.  **地址范围（CIDR 块）** ： 10.10.11.0/24

    c. **网络安全组**：无

    d.单击“下一步”。  **路由表**：无

    e. 选择 **"确定"** ：

16. 保存后，在痕迹导航中选择 " **VM001** " 以返回到 "概述" 边栏选项卡。

17. 选择“网络”。

18. 选择 "**附加网络接口**"，然后选择 "**创建网络接口**"。

19. 在 "**创建网络接口**" 边栏选项卡上：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。  **名称**： vm001nic2

    b.  **子网**：确保子网为 10.10.11.0/24

    c. **网络安全组**： VM001-nsg

    d.单击“下一步”。  **资源组**： storagetesting

20. 成功创建后，在痕迹导航中选择 " **VM001** "，并选择 "**停止**" 以关闭 VM。

21. VM 停止（释放）后，选择 "**网络**"，选择 "**附加网络接口**"，然后选择 " **Vm001nic2**"，然后选择 **"确定"** 。 稍后，你将向 VM 添加其他 NIC。

22. 在 "**网络**" 边栏选项卡中，选择 " **vm001nic2** " 选项卡，然后选择 "**网络接口： vm001nic2**"。

23. 在 "vm001nic 接口" 边栏选项卡上，选择 " **IP 配置**"，然后在边栏选项卡的中心选择 " **ipconfig1**"。

24. 在 "ipconfig1 设置" 边栏选项卡上，选择 "为公共 IP 地址**启用**"，然后选择 "**配置所需设置**"、"**新建"，** 然后输入 Vm001nic2pip 作为名称，选择 "**静态**" **，然后选择** **"确定"**

25. 成功保存后，返回到 "VM001 概述" 边栏选项卡，并选择 "**启动**" 以启动配置的 Windows SERVER 2019 VM。

### <a name="configure-the-windows-server-2019-file-server-storage"></a>配置 Windows Server 2019 文件服务器存储

对于第一个示例，验证的是 Windows Server 2019 文件服务器是在 Hyper-v 上运行的虚拟机的配置。 此虚拟机将配置为具有8个虚拟处理器和一个 VHDX 文件，最重要的是两个虚拟网络适配器。 在理想情况下，这些网络适配器将具有不同的可路由子网，但在此测试中，它们将在同一子网上具有网络适配器。

![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

对于文件服务器，它可以是 Windows Server 2016 或2019（物理或虚拟），运行在 Hyper-v、VMware 或您选择的其他平台上。 这里的主要重点在于 Azure Stack 系统的连接性，但在源和目标之间有多个路径是最好的，因为它提供了额外的冗余，并允许使用更高级的功能来提高性能性能，例如 SMB 多通道。

在继续配置文件共享之前，请用最新的累积更新和修补程序更新文件服务器，然后重启。

更新并重新启动后，现在可以将此服务器配置为文件服务器。

1) 在文件服务器计算机上，从**CMD**运行**ipconfig/all**并记下文件服务器正在使用的**DNS 服务器**。

2)  打开**服务器管理器**，选择 "**管理**"，然后选择 "**添加角色和功能**"。

3)  选择 "**下一步**"，选择 "**基于角色或基于功能的安装**"，并在选择 "**服务器角色**" 页之前提前完成选择。

4)  展开 "**文件和存储服务**"，展开 "**文件 & iSCSI 服务**"，然后勾选 "**文件服务器**" 框。 完成后，关闭**服务器管理器**。

5)  重新打开**服务器管理器**，然后选择 "**文件和存储服务**"。

6)  选择 "**共享**"。

7)  在 "**共享" 框**中，选择**创建文件共享，启动 "新建共享向导"** 链接。

8)  在 "**新建共享向导**" 框中，选择 " **SMB 共享–快速**"，然后选择 "**下一步**"，然后在向导中选择 " **C：\\\\ 卷**。

9)  为共享命名， **TestStorage** ，然后选择 "**下一步**"。

10) 返回 "**新建共享" 向导**，选择 "**下一步**"，然后单击 "**创建**"，然后**关闭**。

你现在已在文件服务器上创建文件共享。

### <a name="testing-file-storage-performance-and-connectivity"></a>测试文件存储性能和连接性

若要检查通信并运行一些基本测试，请重新登录**Azure Stack**系统上的 Azure Stack 用户门户，然后导航到**VM001**的 "**概述**" 边栏选项卡。

1)  选择 "**连接**"，建立到 VM001 的 RDP 连接。

2)  若要通过主机名进行通信，需要编辑**主机**文件;但是，在生产环境中，将以最佳方式配置 DNS，以便名称自动解析。 你还可以使用 IP 地址。 但对于我们的示例，你将编辑**Hosts**文件。

3)  打开**记事本**，确保**以管理员身份运行**。

4)  打开记事本后，选择 "**文件**"，**打开**并浏览到 c：\\Windows\System32\Drivers\etc\, 并选择 "打开" 对话框右下角的 "**所有文件**"。 选择**hosts**文件，然后选择 "**打开**"。

5)  通过为文件服务器添加 IP 地址和 DNS 名称，编辑你的 hosts 文件。

    ![](./media/azure-stack-network-howto-extend-datacenter/image6.png)

6)  保存该文件，然后关闭记事本。

7)  打开**CMD** ，并对在 hosts 文件中输入的文件服务器的名称进行 ping 操作。 这应返回文件服务器上某个网络适配器的 IP 地址，因此，通过对 IP 地址进行 ping 操作，手动测试与其他适配器的通信。

## <a name="connect-to-a-windows-server-iscsi-target-server"></a>连接到 Windows Server iSCSI 目标服务器

在这种情况下，你将在 Azure Stack 上部署和配置 Windows Server 2019 虚拟机，并准备它连接到将运行 Windows Server 2019 的外部 iSCSI 目标服务器。

> [!Note]  
> 如果你已经完成了方案一，请直接跳到自定义计算机。

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack"></a>在 Azure Stack 上部署 Windows Server 2019 VM

如果你尚未在 Azure Stack 上部署 Windows Server 2019 VM，请按照在[Azure Stack 上部署 Windows server 2019 vm](#deploy-the-windows-server-2019-vm-on-azure-stack)中的步骤操作。 然后，你可以配置 Windows Server 2019 iSCSI 目标。

### <a name="configure-the-windows-server-2019-iscsi-target"></a>配置 Windows Server 2019 iSCSI 目标

对于这第二种情况，请验证配置，其中 Windows Server 2019 iSCSI 目标是在 Hyper-v 上运行的虚拟机。 此虚拟机将配置为具有8个虚拟处理器和一个 VHDX 文件，最重要的是两个虚拟网络适配器。 在理想情况下，这些网络适配器将具有不同的可路由子网，但在此测试中，同一子网中会有网络适配器。

![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

对于 iSCSI 目标，它可以是 Windows Server 2016 或2019（物理或虚拟），运行在 Hyper-v、VMware 或选择的其他平台上。 这里的主要重点是，连接到和传出 Azure Stack 系统，但在源和目标之间有多个路径最好，因为它提供了额外的冗余和吞吐量。

在继续配置 iSCSI 目标服务器之前，请用最新的累积更新和修补程序更新文件服务器，重新启动。

更新并重新启动后，现在可以将此服务器配置为 iSCSI 目标服务器。

## <a name="next-steps"></a>后续步骤

[Azure Stack 网络的差异和注意事项](azure-stack-network-differences.md)  