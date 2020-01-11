---
title: 如何在 Azure Stack 中心扩展数据中心 |Microsoft Docs
description: 了解如何在 Azure Stack 集线器上扩展数据中心。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/13/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/13/2019
ms.openlocfilehash: 292f210d8145bdac64e2f50f4c8ef85dc79d1a77
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883637"
---
# <a name="extending-storage-to-azure-stack-hub"></a>将存储扩展到 Azure Stack 集线器

本文提供 Azure Stack 中心存储基础结构信息，以帮助你决定如何将 Azure Stack 中心集成到现有网络环境中。 在提供扩展数据中心的一般讨论后，本文介绍了两种不同的方案。 可以连接到 Windows 文件存储服务器。 你还可以连接到 Windows iSCSI 服务器。

## <a name="overview-of-extending-storage-to-azure-stack-hub"></a>将存储扩展到 Azure Stack 集线器概述

在某些情况下，你的数据位于公有云中是不够的。 可能有一个计算密集型虚拟化数据库工作负荷，对延迟很敏感，而公有云的往返时间可能会影响数据库工作负荷的性能。 可能存在本地数据、保存在文件服务器、NAS 或 iSCSI 存储阵列上，需要由本地工作负荷进行访问，需要在本地以满足法规或遵从性目标。 这只是两种情况，即数据驻留在本地，对许多组织来说都是很重要的。

那么，为什么不只在存储帐户中将数据托管到 Azure Stack 集线器上，或在虚拟文件服务器中，在 Azure Stack 中心系统上运行？ 与在 Azure 中一样，Azure Stack 集线器存储是有限的。 使用的容量完全取决于你选择购买的每个节点的容量，以及你拥有的节点数。 由于 Azure Stack 中心是一种超聚合解决方案，因此，如果想要增长存储容量以满足使用量需求，还需要通过添加节点来增加计算空间。 这可能会导致成本不高，尤其是在需要额外容量的情况下，可以在 Azure Stack 中心系统以外为低成本添加的备用存档存储。

这会将你带到下面介绍的方案。 如何将 Azure Stack 集线器系统、Azure Stack 中心上运行的虚拟化工作负荷简单高效地连接到 Azure Stack 中心以外的存储系统，并可通过网络进行访问。

### <a name="design-for-extending-storage"></a>扩展存储的设计

该关系图描绘了这样一种方案：其中，运行工作负荷的单个虚拟机将连接到虚拟机，并利用外部（到 VM 和 Azure Stack 中心本身）存储，以便进行数据读取/写入等。对于本文，你将重点介绍文件的简单检索，但你可以扩展此示例，以实现更复杂的方案，例如数据库文件的远程存储。

![](./media/azure-stack-network-howto-extend-datacenter/image1.png)

在该图中，你会看到 Azure Stack 集线器系统上的 VM 已部署了多个 Nic。 从这两种冗余，以及存储最佳做法，在目标和目标之间有多个路径，这一点很重要。 在某些情况下，Azure Stack 集线器中的 Vm 具有公共和专用 Ip，就像在 Azure 中一样。 如果外部存储需要访问 VM，则它只能通过公共 IP 来实现，因为专用 ip 主要用于 Vnet 和子网中的 Azure Stack 中心系统内。 外部存储将无法与 VM 的专用 IP 空间通信，除非它通过站点到站点 VPN，才能打孔到 vNet 本身。 因此，在本示例中，我们将重点介绍通过公共 IP 空间进行的通信。 请注意，在关系图中，公共 IP 空间有2个不同的公共 IP 池子网。 默认情况下，Azure Stack 集线器只需要一个池来实现公共 IP 地址，但要考虑冗余路由，则可能要添加另一个池。 但是，此时不能从特定池中选择 IP 地址，因此，你可能确实会在多个虚拟网卡上，使用同一池中具有公共 Ip 的 Vm。

出于此讨论的目的，我们假设边界设备和外部存储之间的路由处理，并且流量可以适当地遍历网络。 对于本示例，无论主干是1GbE、10GbE、25 GbE 还是更快，都不重要，但是在规划集成时，请务必考虑到访问此外部存储的任何应用程序的性能需求。

## <a name="connect-to-a-windows-server-iscsi-target"></a>连接到 Windows Server iSCSI 目标

在此方案中，我们将在 Azure Stack 中心部署和配置 Windows Server 2019 虚拟机，并将其准备好连接到将运行 Windows Server 2019 的外部 iSCSI 目标。 在适当的情况下，我们将启用 MPIO 等关键功能，以优化 VM 与外部存储之间的性能和连接性。

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack-hub"></a>在 Azure Stack 集线器上部署 Windows Server 2019 VM

1.  在**Azure Stack 集线器管理门户**中，如果此系统已正确注册并连接到 marketplace，请选择 " **marketplace 管理"** ，然后，如果你还没有 Windows server 2019 映像，请选择 "**从 Azure 添加**"，然后搜索**windows Server 2019**，添加**windows server 2019 Datacenter**映像。

    ![](./media/azure-stack-network-howto-extend-datacenter/image2.png)

    下载 Windows Server 2019 映像可能需要一些时间。

2.  在 Azure Stack 中心环境中拥有 Windows Server 2019 映像后，**登录到 Azure Stack 中心用户门户**。

3.  登录到 Azure Stack 集线器用户门户后，确保你有产品/[服务的订阅](https://docs.microsoft.com/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-1908)，该产品可用于预配 IaaS 资源（计算、存储和网络）。

4.  获得可用订阅后，请返回 Azure Stack 中心用户门户中的 "**仪表板**"，选择 "**创建资源**"，选择 "**计算**"，然后选择 " **Windows Server 2019 Datacenter 库" 项**。

5.  在 "**基本**信息" 边栏选项卡上，填写以下信息：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  **名称**： VM001

    b.保留“数据库类型”设置，即设置为“共享”。  **用户名**： "localadmin"

    c.  **密码**和**确认密码**：所选 \<密码 >

    d.单击“下一步”。  **订阅**：选择 \<订阅，并 > 计算/存储/网络资源。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。  **资源组**： storagetesting （新建）

    f.  选择“确定”

6.  在 "**选择大小**" 边栏选项卡中，选择**Standard_F8s_v2** ，然后选择 "**选择**"。

7.  在 "**设置**" 边栏选项卡上，选择 "**虚拟网络**"，然后在 "**创建虚拟网络**" 边栏选项卡中，将地址空间调整为**10.10.10.0/23** ，将子网地址范围更新为**10.10.10.0/24** ，然后选择 **"确定"** 。

8.  选择 "**公共 ip 地址**"，然后在 "**创建公共 ip 地址**" 边栏选项卡中，选择 "**静态**" 单选按钮。

9.  在 "**选择公用入站端口**" 下拉列表中，选择 " **RDP （3389）** "。

10. 保留其他默认值，然后选择 **"确定"** 。

    ![](./media/azure-stack-network-howto-extend-datacenter/image3.png)

11. 阅读摘要，等待验证，然后选择 **"确定"** 开始部署。 部署应在10分钟内完成。

12. 部署完成后，在 "**资源**" 下，选择虚拟机名称**VM001** "打开**概述**"。

    ![](./media/azure-stack-network-howto-extend-datacenter/image4.png)

13. 在 "DNS 名称" 下，选择 "**配置**" 并提供 DNS 名称标签**vm001** ，并选择 "**保存**"，然后选择 " **vm001**"。

14. 在 "概述" 边栏选项卡的右侧，选择 "虚拟网络/子网" 文本下的**storagetesting/默认值**。

15. 在 storagetesting 边栏选项卡中，依次选择 "**子网**" 和 "**子网**"，然后在新的 "添加子网" 边栏选项卡中输入以下信息，然后选择 **"确定"** ：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  **名称**： subnet2

    b.保留“数据库类型”设置，即设置为“共享”。  **地址范围（CIDR 块）** ： 10.10.11.0/24

    c.  **网络安全组**：无

    d.单击“下一步”。  **路由表**：无

16. 保存后，选择 " **VM001**"。

17. 在 "概述" 边栏选项卡的左侧，选择 "**网络**"。

18. 选择 "**附加网络接口**"，然后选择 "**创建网络接口**"。

19. 在 "**创建网络接口**" 边栏选项卡中输入以下信息。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  **名称**： vm001nic2

    b.保留“数据库类型”设置，即设置为“共享”。  **子网**：确保子网为 10.10.11.0/24

    c.  **网络安全组**： VM001-nsg

    d.单击“下一步”。 **资源组**： storagetesting

20. 成功附加后，选择 " **VM001** "，并选择 "**停止**" 以关闭 VM。

21. 停止（释放） VM 后，在 "概述" 边栏选项卡的左侧，选择 "**网络**"，选择 "**附加网络接口**"，并选择 " **Vm001nic2**"，然后选择 **"确定"** 。 稍后会将其他 NIC 添加到 VM。

22. 仍在 "**网络**" 边栏选项卡中，选择 " **vm001nic2** " 选项卡，然后选择 "**网络接口： vm001nic2**"。

23. 在 "vm001nic 接口" 边栏选项卡上，选择 " **IP 配置**"，然后在边栏选项卡的中心选择 " **ipconfig1**"。

24. 在 "ipconfig1 设置" 边栏选项卡上，选择 "为公共 IP 地址**启用**"，然后选择 "**配置所需设置**"、"**新建"，** 然后输入 Vm001nic2pip 作为名称，选择 "**静态**" **，然后选择** **"确定"**

25. 成功保存后，返回到 "VM001 概述" 边栏选项卡，并选择 "**启动**" 以启动配置的 Windows SERVER 2019 VM。

26. 启动后，建立到 VM001 的**RDP 会话**。

27. 连接到 VM 后，请以管理员身份打开**CMD** ，并输入**主机名**来检索 OS 的计算机名。 **它应与 VM001 匹配**。 稍后记下此。

### <a name="configure-second-network-adapter-on-windows-server-2019-vm-on-azure-stack-hub"></a>在 Azure Stack 集线器上的 Windows Server 2019 VM 上配置第二个网络适配器

默认情况下，Azure Stack 集线器会将默认网关分配给附加到虚拟机的第一个（主）网络接口。 Azure Stack 中心不会将默认网关分配给附加到虚拟机的附加（辅助）网络接口。 因此，默认情况下无法与辅助网络接口所在子网的外部资源进行通信。 但是，辅助网络接口可以与子网外部的资源进行通信，尽管对不同操作系统而言，启用通信的步骤有所不同。

1.  如果还没有打开连接，请在**VM001**中建立 RDP 连接。

2.  以管理员身份打开**CMD**并运行**路由打印**，这应在此 VM 内返回两个接口（hyper-v 网络适配器）。

    ![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

3.  现在，运行**ipconfig**以查看分配给辅助网络接口的 IP 地址。 在此示例中，将10.10.11.4 分配给 interface 6。 辅助网络接口没有返回任何默认网关地址。

    ![](./media/azure-stack-network-howto-extend-datacenter/image6.png)

4.  若要将目标为辅助网络接口子网外的地址的所有流量路由到子网的网关，请从**CMD：** 运行以下命令。

    ```CMD  
    route add -p 0.0.0.0 MASK 0.0.0.0 <ipaddress> METRIC 5015 IF <interface>
    ```

    `<ipaddress>` 是当前子网的0.1 地址，`<interface>` 是接口号。

    ![](./media/azure-stack-network-howto-extend-datacenter/image7.png)

5.  若要确认添加的路由位于路由表中，请输入**route 打印**命令。

    ![](./media/azure-stack-network-howto-extend-datacenter/image8.png)

6.  还可以通过运行 ping 命令来验证出站通信：  
    `ping 8.8.8.8 -S 10.10.11.4`  
    `-S` 标志允许您指定源地址，在此示例中，10.10.11.4 是现在包含默认网关的 NIC 的 IP 地址。

7.  关闭**CMD**。

### <a name="configure-the-windows-server-2019-iscsi-target"></a>配置 Windows Server 2019 iSCSI 目标

在此方案中，你将验证配置，其中，Windows Server 2019 iSCSI 目标是在 Hyper-v 上运行的虚拟机，在 Azure Stack 中心环境之外。 此虚拟机将配置为具有8个虚拟处理器和一个 VHDX 文件，最重要的是2个虚拟网络适配器。 在理想情况下，这些网络适配器将具有不同的可路由子网，但在此验证中，它们将在同一子网上具有网络适配器。

![](./media/azure-stack-network-howto-extend-datacenter/image9.png)

对于 iSCSI 目标服务器，该服务器可以是 Windows Server 2016 或2019（物理或虚拟），运行在 Hyper-v、VMware 或你选择的备用设备上，如专用物理 iSCSI SAN。 这里的主要重点是 Azure Stack 集线器系统之间的连接，但在源和目标之间有多个路径是最好的，因为它提供了额外的冗余，并允许使用更高级的功能提高性能性能，如 MPIO。

我建议您在继续配置文件共享之前，用最新的累积更新和修补程序更新 Windows Server 2019 iSCSI 目标，并在必要时重新启动。

更新并重新启动后，现在可以将此服务器配置为 iSCSI 目标。

1.  打开**服务器管理器**，选择 "**管理**"，然后选择 "**添加角色和功能**"。

2.  打开后，选择 "**下一步**"，选择 "**基于角色或基于功能的安装**"，然后继续执行所选内容，直至到达 "**选择服务器角色**" 页。

3.  展开 "**文件和存储服务**"，展开 "**文件 & iscsi 服务**"，然后勾选 " **iscsi 目标服务器**" 框，接受任何弹出提示来添加新功能，然后继续完成。

    ![](./media/azure-stack-network-howto-extend-datacenter/image10.png)

    完成后，关闭**服务器管理器。**

4.  打开**文件资源管理器，** 导航到 C：\\ 并创建一个名为 " **iSCSI**"**的新文件夹**。

5.  重新打开**服务器管理器**，然后从左侧菜单中选择 "**文件和存储服务**"。

6.  选择**iSCSI** ，并选择右侧窗格中的 "**创建 iscsi 虚拟磁盘，启动新的 Iscsi 虚拟磁盘向导**" 链接。 选择它。 将弹出一个向导。

7.  在 "**选择 iSCSI 虚拟磁盘位置**" 页上，选择 "**键入自定义路径**" 单选按钮，并浏览到**C：\\ISCSI** ，然后选择 "**下一步**"。

8.  为 iSCSI 虚拟磁盘指定名称**iSCSIdisk1** ，并根据需要选择 "**下一步**"。

9.  将虚拟磁盘的大小设置为**10gb** ，并选择 "**固定大小**"，然后选择 "**下一步**"。

    ![](./media/azure-stack-network-howto-extend-datacenter/image11.png)

10) 由于这是一个新的目标，请选择 "**新建 iSCSI 目标**" 并选择 "**下一步**"。

11) 在 "**指定目标名称**" 页上，输入**TARGET1** ，然后选择 "**下一步**"。

12) 在 "**指定访问服务器**" 页上，选择 "**添加**"。 这将打开一个对话框，用于输入将被授权连接到 iSCSI 目标的特定**发起**程序。

13) 在 "**添加发起方 ID" 窗口**中，选择 **"输入所选类型的值**" 并在 "**类型**" 下，在下拉菜单中选择 "确保 IQN"。 输入 **\<computername >** ，其中 \<computername > 是**计算机名称** **VM001** ，然后选择 "**下一步**"。

    ![](./media/azure-stack-network-howto-extend-datacenter/image12.png)

14) 在 "**启用身份验证**" 页上，将框留空，然后选择 "**下一步**"。

15) 确认您的选择，然后选择 "**创建**"，然后单击 "关闭"。 应会看到在服务器管理器中创建的 iSCSI 虚拟磁盘。

    ![](./media/azure-stack-network-howto-extend-datacenter/image13.png)

### <a name="configure-the-windows-server-2019-iscsi-initiator-and-mpio"></a>配置 Windows Server 2019 iSCSI 发起程序和 MPIO

若要首先设置 iSCSI 发起程序，请首先登录到**Azure Stack 中心**系统上的**Azure Stack 集线器用户门户**，然后导航到 VM001 的 "**概述**" 边栏选项卡 **。**

1.  建立与 VM001 的 RDP 连接。 连接后，打开**服务器管理器**。

2.  选择 "**添加角色和功能**"，并接受默认值，直至到达 "**功能**" 页。

3.  在 "**功能**" 页上，添加**多路径 i/o** ，然后选择 "**下一步**"。

    ![](./media/azure-stack-network-howto-extend-datacenter/image14.png)

4.  勾选 "**如果需要，自动重新启动目标服务器"** 复选框并选择 "**安装**"，然后选择 "**关闭"。** 最有可能需要重新启动，因此完成后，请重新连接到 VM001。

5.  返回**服务器管理器**，等待**MPIO 安装完成**，选择 "**关闭**"，然后选择 "**工具**"，然后选择 " **MPIO**"。

6.  选择 "**发现多路径**" 选项卡，并勾选框以**添加对 iSCSI 设备的支持**并选择 "**添加**"，然后选择 **"是"** 以**重新启动**VM001。 如果未收到窗口，请选择 **"确定"，** 然后手动重新启动。

    ![](./media/azure-stack-network-howto-extend-datacenter/image15.png)

7.  重新启动后，建立**到 VM001 的新 RDP 连接**。

8.  连接后，请打开**服务器管理器**，选择 "**工具**"，然后选择 " **iSCSI 发起程序**"。

9.  当 "Microsoft iSCSI" 窗口弹出时，请选择 **"是"** 以允许 iSCSI 服务在默认情况下运行。

    ![](./media/azure-stack-network-howto-extend-datacenter/image16.png)

10. 在 "iSCSI 发起程序属性" 窗口中，选择 "**发现**" 选项卡。

11. 现在，你将添加2个目标，因此请先选择 "**发现门户**" 按钮。

12. 输入 iSCSI 目标服务器的第一个 IP 地址，然后选择 "**高级**"。

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

13. 在 "**高级设置**" 窗口中，选择以下项，然后选择 **"确定"** 。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  **本地适配器**： Microsoft ISCSI 发起程序。

    b.保留“数据库类型”设置，即设置为“共享”。  **发起程序 IP**：10.10.10.4。

14. 返回到 "**发现目标门户**" 窗口，选择 **"确定"** 。

15. 按照以下步骤重复此过程：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 \* * IP 地址 * *：第二个 iSCSI 目标 IP 地址。

    b.保留“数据库类型”设置，即设置为“共享”。  **本地适配器**： Microsoft ISCSI 发起程序。

    c.  **发起程序 IP**：10.10.11.4。

16. 目标门户应该如下所示，在 "**地址**" 列下提供自己的 ISCSI 目标 ip。

    ![](./media/azure-stack-network-howto-extend-datacenter/image18.png)

17. 返回 "**目标**" 选项卡，从窗口中间选择 iSCSI 目标，然后选择 "**连接**"。

18. 在 "**连接到目标**" 窗口中，选中 "**启用多路径**" 复选框，然后选择 "**高级**"。

    ![](./media/azure-stack-network-howto-extend-datacenter/image19.png)

19. 输入以下信息并选择 **"确定"** ，然后在 "**连接到目标**" 窗口中选择 **"确定"** 。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  **本地适配器**： Microsoft ISCSI 发起程序。

    b.保留“数据库类型”设置，即设置为“共享”。  **发起程序 IP**：10.10.10.4。

    c.  **目标门户 ip**： \<第一个 ISCSI 目标 ip/3260 >。

![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

1.  对第二个发起方/目标组合重复此过程。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 \* * 本地适配器 * *： Microsoft iSCSI 发起程序。

    b.保留“数据库类型”设置，即设置为“共享”。  **发起程序 IP**：10.10.11.4。

    c.  **目标门户 IP**： \<第二个 ISCSI 目标 ip/3260 >。

        ![](./media/azure-stack-network-howto-extend-datacenter/image21.png)

2.  选择 "**卷和设备**" 选项卡，然后选择 "**自动配置**" –应会看到提供的 MPIO 卷：

    ![](./media/azure-stack-network-howto-extend-datacenter/image22.png)

3.  返回 "**目标**" 选项卡，选择 "**设备**"，你应该会看到2个连接到之前创建的单个 iSCSI VHD。

    ![](./media/azure-stack-network-howto-extend-datacenter/image23.png)

4.  选择**MPIO 按钮**以查看有关负载平衡策略和路径的详细信息。

    ![](./media/azure-stack-network-howto-extend-datacenter/image24.png)

5.  选择 **"确定"** 三次以退出 Windows 和 ISCSI 发起程序。

6.  打开 "磁盘管理（diskmgmt.msc）"，系统会提示 "**初始化磁盘**" 窗口。

    ![](./media/azure-stack-network-howto-extend-datacenter/image25.png)

7.  选择 **"确定"** 接受默认值，然后向下滚动到新磁盘，右键单击，然后选择 "**新建简单卷**"

8.  遍历向导，接受默认值。 将卷标更改为**iSCSIdisk1** ，然后选择 "**完成**"。

    ![](./media/azure-stack-network-howto-extend-datacenter/image26.png)

9.  然后，应将驱动器格式化并显示驱动器号。

10. 打开**文件资源管理器**并选择 "**这台电脑**"，以查看附加到 VM001 的新驱动器。

### <a name="testing-external-storage-connectivity"></a>测试外部存储连接

若要验证通信并运行基本的文件复制测试，请首先登录到**Azure Stack 中心**系统上的**Azure Stack 集线器用户门户**，然后导航到**VM001**的 "**概述**" 边栏选项卡。

1.  选择 "**连接**"，建立到**VM001**的 RDP 连接

2.  打开**任务管理器**，选择 "**性能**" 选项卡，然后将窗口与 RDP 会话的右侧对齐。

3.  以管理员身份打开**Windows PowerShell ISE** ，并将其对齐到 RDP 会话的左侧。 在 ISE 右侧，关闭 "**命令**" 窗格，然后选择 "**脚本**" 按钮，展开 ISE 窗口顶部的 "白脚本" 窗格。

4.  在此虚拟机中，没有用于创建 VHD 的本机 PowerShell 模块，我们将使用它作为一个大文件来测试文件到 iSCSI 目标的传输。 在这种情况下，我们将运行 DiskPart 来创建 VHD 文件。 在 ISE 中运行以下操作：

    1. `Start-Process Diskpart`

    2. 随即打开一个新的 CMD 窗口，然后输入：  
        `**Create vdisk file="c:\\test.vhd" type=fixed maximum=5120**`
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image27.png)
    
    3.  这将需要几分钟时间来创建。 创建完成后，若要验证创建，请打开**文件资源管理器**并导航到 C：\\-应该会看到新的测试 .vhd，并且大小为5gb。

    ![](./media/azure-stack-network-howto-extend-datacenter/image28.png)

    4. 关闭 CMD 窗口，返回到 ISE，然后在脚本窗口中输入以下命令。 将 F：\\ 替换为之前应用的 iSCSI 目标驱动器号。

    5. `Copy-Item "C:\\test.vhd" -Destination "F:\\"`

    6. 在脚本窗口中选择该行，然后按 F8 运行。

    7. 运行该命令时，请观察两个网络适配器，查看在 VM001 中的两个网络适配器上发生的数据传输。 还应注意每个网络适配器应平均地共享负载。

    ![](./media/azure-stack-network-howto-extend-datacenter/image29.png)

此方案旨在重点介绍 Azure Stack 集线器上运行的工作负荷与外部存储阵列（在这种情况下为基于 Windows Server 的 iSCSI 目标）之间的连接。 这并不是一种性能测试，也不会反映在使用基于 iSCSI 的备用设备时需要执行的步骤，但它确实重点介绍了在 Azure Stack 集线器上部署工作负荷时所要做的一些核心注意事项，并将它们连接到 Azure Stack 中心环境以外的存储系统。

## <a name="next-steps"></a>后续步骤

[Azure Stack 集线器网络的差异和注意事项](azure-stack-network-differences.md)
