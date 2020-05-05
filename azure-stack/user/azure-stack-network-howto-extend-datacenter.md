---
title: 如何在 Azure Stack Hub 上扩展数据中心
description: 了解如何在 Azure Stack Hub 上扩展数据中心。
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/13/2019
ms.openlocfilehash: 99a8425901213d50c17175ab946aeff78a5aa81d
ms.sourcegitcommit: 278aaeca069213a98b90751253f6b15423634849
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82742601"
---
# <a name="extending-storage-to-azure-stack-hub"></a>将存储扩展到 Azure Stack Hub

本文提供 Azure Stack Hub 存储基础架构信息，可帮助你确定如何将 Azure Stack Hub 集成到现有的网络环境。 在大致介绍如何扩展数据中心后，本文将演示两种不同的方案。 可以连接到 Windows 文件存储服务器。 也可以连接到 Windows iSCSI 服务器。

## <a name="overview-of-extending-storage-to-azure-stack-hub"></a>将存储扩展到 Azure Stack Hub 概述

在某些情况下，你的数据位于公有云中是不够的。 也许你拥有的是计算密集型虚拟化数据库工作负载，它对延迟敏感，而往返公有云的时间可能会影响数据库工作负载的性能。 也许本地数据存储在文件服务器、NAS 或 iSCSI 存储阵列上，需要通过本地工作负载才能访问，并且需要驻留在本地以满足法规或合规性目标。 将本地数据驻留对许多组织而言都很重要，以上只是其中两种情况。

为什么不直接在 Azure Stack Hub 上的存储帐户中或在 Azure Stack Hub 系统上运行的虚拟化文件服务器中承载该数据？ 与 Azure 中不同，Azure Stack Hub 存储是有限的。 可供使用的容量完全取决于所选购的每个节点的容量，以及所拥有的节点数。 此外，由于 Azure Stack Hub 是一种超融合解决方案，因而如果希望增加存储容量以满足使用需求，还需通过增加节点来增加计算占用。 这可能会导致成本过高，尤其是在额外需要的容量是用于可为获得 Azure Stack Hub 系统外的低成本而添加的冷存档存储的情况下。

以上是了解以下方案的原因。 如何将 Azure Stack Hub 系统以及 Azure Stack Hub 上运行的虚拟化工作负载简单高效地连接到 Azure Stack Hub 之外可通过网络访问的存储系统。

### <a name="design-for-extending-storage"></a>专为扩展存储设计

下图描绘了一种方案，其中，运行工作负荷的单个虚拟机连接并利用外部（在 VM 和 Azure Stack Hub 本身的外部）存储来读取/写入数据。本文重点说明简单的文件检索，但你也可以扩展本示例，使其适用于更复杂的方案，例如远程存储数据库文件。

![](./media/azure-stack-network-howto-extend-datacenter/azure-stack-network-howto-extend-datacenter-image1.svg)

在该图中，你会看到 Azure Stack 集线器系统上的 VM 已部署了多个 Nic。 从这两种冗余，以及存储最佳做法，在目标和目标之间有多个路径，这一点很重要。 让情况变得更为复杂的是，Azure Stack Hub 中的 VM 同时有公共和专用 IP，就像在 Azure 中的情况一样。 如果外部存储必须连接到该 VM，就只能通过公共 IP 进行连接，因为专用 IP 主要用于 Azure Stack Hub 系统内部的 vNet 和子网。 外部存储无法与 VM 的专用 IP 空间通信，除非它能通过站点到站点 VPN 连接到 vNet 本身。 因此，在本示例中，我们将重点介绍通过公共 IP 空间进行的通信。 请注意，在图中的公共 IP 空间内，有 2 个不同的公共 IP 池子网。 默认情况下，Azure Stack Hub 只需要一个用于公共 IP 地址的池即可，但考虑到冗余路由，可以添加另一个池。 但由于无法选择特定池中的 IP 地址，实际上 VM 最后可能具有来自同一池、但跨多个虚拟网络卡的公共 IP。

为了便于讨论，假设边界设备与外部存储之间的路由受到管理，流量可以正常遍历网络。 在本示例中，主干是 1 GbE、10 GbE、25 GbE 还是更快的速度并不重要，但在规划集成时必须考虑到这一点，使所有应用程序获得所需的性能来访问此外部存储。

## <a name="connect-to-a-windows-server-iscsi-target"></a>连接到 Windows Server iSCSI 目标

在此方案中，我们将在 Azure Stack Hub 上部署并配置 Windows Server 2019 虚拟机，并让其准备好连接到外部 iSCSI 目标（也运行 Windows Server 2019）。 在适当的情况下，我们将启用 MPIO 等重要功能，以优化性能以及 VM 与外部存储之间的连接。

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack-hub"></a>在 Azure Stack Hub 上部署 Windows Server 2019 VM

1.  在**Azure Stack 集线器管理门户**中，如果此系统已正确注册并连接到 marketplace，请选择 " **marketplace 管理"** ，然后，如果你还没有 Windows server 2019 映像，请选择 "**从 Azure 添加**"，然后搜索**windows Server 2019**，添加**windows server 2019 Datacenter**映像。

    ![](./media/azure-stack-network-howto-extend-datacenter/image2.png)

    下载 Windows Server 2019 映像可能需要一段时间。

2.  如果在 Azure Stack Hub 环境中拥有 Windows Server 2019 映像，即可登录到 Azure Stack Hub 用户门户  。

3.  登录到 Azure Stack Hub 用户门户后，确保自己拥有[某个套餐的订阅](https://docs.microsoft.com/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-1908)，可用于预配 IaaS 资源（计算、存储和网络）。

4.  获得可用的订阅后，回到 Azure Stack Hub 用户门户中的“仪表板”，并依次选择“创建资源”、“计算”、“Windows Server 2019 Datacenter 库项”     。

5.  在“基本信息”边栏选项卡上，填写如下信息  ：

    a.  **名称**：VM001

    b.  **用户名**：localadmin

    c.  **密码**和**确认密码**：\<所选的密码>

    d.  **订阅**：\<包含计算/存储/网络资源的所选订阅>。

    e.  **资源组**：storagetesting（新建）

    f.  选择“确定” 

6.  在“选择大小”边栏选项卡上，依次选择“Standard_F8s_v2”和“选择”    。

7.  在“设置”边栏选项卡上选择“虚拟网络”，在“创建虚拟网络”边栏选项卡上，将地址空间调整为 **10.10.10.0/23** 并将子网地址范围更新为 **10.10.10.0/24**，然后选择“确定”。    

8.  选择“公共 IP 地址”，然后在“创建公共 IP 地址”边栏选项卡中选择“静态”单选按钮    。

9.  在“选择公共入站端口”下拉列表中，选择“RDP (3389)”   。

10. 保留其他默认值，然后选择“确定”。 

    ![](./media/azure-stack-network-howto-extend-datacenter/image3.png)

11. 阅读摘要、等待验证，然后选择“确定”开始部署。  部署应该可在大约 10 分钟内完成。

12. 完成部署后，在“资源”下选择虚拟机名称“VM001”以打开“概述”    。

    ![](./media/azure-stack-network-howto-extend-datacenter/image4.png)

13. 在“DNS 名称”下，选择“配置”并提供 DNS 名称标签 vm001，选择“保存”，然后选择“VM001”     。

14. 在“概述”边栏选项卡的右侧，选择“虚拟网络/子网”文本下的“storagetesting-vnet/default”。 

15. 在“storagetesting-vnet”边栏选项卡中，依次选择“子网”、“+子网”，然后在新的“添加子网”边栏选项卡中输入以下信息，然后选择“确定”    ：

    a.  **名称**：subnet2

    b.  **地址范围(CIDR 块)** ：10.10.11.0/24

    c.  **网络安全组**：无

    d.  **路由表**：无

16. 保存后，选择“VM001”  。

17. 从“概述”边栏选项卡的左侧，选择“网络”  。

18. 依次选择“链接网络接口”、“创建网络接口”。  

19. 在“创建网络接口”边栏选项卡中，输入以下信息  。

    a.  **名称**：vm001nic2

    b.  **子网**：确保子网为 10.10.11.0/24

    c.  **网络安全组**：VM001-nsg

    d. **资源组**：storagetesting

20. 成功附加后，选择“VM001”，然后选择“停止”以关闭 VM   。

21. 停止（解除分配）VM 后，从“概述”边栏选项卡左侧依次选择“网络”、“附加网络接口”、“vm001nic2”和“确定”     。 片刻之后，系统将额外的 NIC 添加到 VM。

22. 在“网络”边栏选项卡上选择“vm001nic2”选项卡，然后选择“网络接口: vm001nic2”    。

23. 在“vm001nic 接口”边栏选项卡上选择“IP 设置”，然后在边栏选项卡的中央选择“ipconfig1”。  

24. 在“ipconfig1 设置”边栏选项卡上，为“公共 IP 地址”选择“已启用”，选择“配置所需的设置”、“新建”，输入 vm001nic2pip 作为名称，然后依次选择“静态”、“确定”、“保存”。      

25. 成功保存后，返回 VM001 概述边栏选项卡，然后选择“启动”以启动配置的 Windows Server 2019 VM。 

26. 启动后，在 VM001 中建立 RDP 会话  。

27. 在 VM 建立连接后，打开 CMD（以管理员身份），然后输入主机名以检索 OS 的计算机名   。 它应与 VM001 匹配  。 记下此名称供以后使用。

### <a name="configure-second-network-adapter-on-windows-server-2019-vm-on-azure-stack-hub"></a>在 Azure Stack Hub 上配置 Windows Server 2019 VM 上的第二个网络适配器

默认情况下，Azure Stack Hub 会将默认网关分配给附加到虚拟机的第一个（主）网络接口。 Azure Stack Hub 不会将默认网关分配给附加到虚拟机的其他（辅助）网络接口。 因此，默认情况下无法与辅助网络接口所在子网的外部资源进行通信。 但是，辅助网络接口可以与子网外部的资源进行通信，尽管对不同操作系统而言，启用通信的步骤有所不同。

1.  如果尚未打开连接，请在 VM001 中建立 RDP 连接  。

2.  以管理员身份打开 CMD，然后运行“route print”，这应返回此 VM 内的两个接口（Hyper-V 网络适配器）   。

    ![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

3.  现在，运行 ipconfig 以查看分配给辅助网络接口的 IP 地址  。 在本例中，10.10.11.4 被分配到接口 6。 辅助网络接口没有返回任何默认网关地址。

    ![](./media/azure-stack-network-howto-extend-datacenter/image6.png)

4.  要将发往辅助网络接口子网外部地址的所有流量路由到子网网关，请从“CMD:”运行以下命令  。

    ```CMD  
    route add -p 0.0.0.0 MASK 0.0.0.0 <ipaddress> METRIC 5015 IF <interface>
    ```

    `<ipaddress>` 是当前子网的 .1 地址，`<interface>` 是接口编号。

    ![](./media/azure-stack-network-howto-extend-datacenter/image7.png)

5.  若要确认添加的路由是否在路由表中，请输入“route print”命令  。

    ![](./media/azure-stack-network-howto-extend-datacenter/image8.png)

6.  还可以通过运行 ping 命令来验证出站通信：  
    `ping 8.8.8.8 -S 10.10.11.4`  
    使用 `-S` 标记可以指定源地址，本例中，10.10.11.4 是目前具有默认网关的 NIC 的 IP 地址。

7.  关闭 CMD  。

### <a name="configure-the-windows-server-2019-iscsi-target"></a>配置 Windows Server 2019 iSCSI 目标

在此方案中，你将验证配置，其中，Windows Server 2019 iSCSI 目标是在 Hyper-v 上运行的虚拟机，在 Azure Stack 中心环境之外。 将为此虚拟机配置 8 个虚拟处理器、1 个 VHDX 文件，最重要的是，还要配置 2 个虚拟网络适配器。 在理想情况下，这两个网络适配器将有不同的可路由子网，但在此验证中，它们位于同一子网上。

![](./media/azure-stack-network-howto-extend-datacenter/image9.png)

iSCSI 目标服务器可以是在 Hyper-V、VMware 或所选替代设备（专用的物理 iSCSI SAN 等）上运行的 Windows Server 2016 或 2019 物理机或虚拟机。 此处的重点是与 Azure Stack Hub 系统建立入站和出站连接，但源与目标之间最好有多个路径，这样可以提供额外的冗余，并可以使用更高级的功能（例如 MPIO）来提升性能。

我建议您在继续配置文件共享之前，用最新的累积更新和修补程序更新 Windows Server 2019 iSCSI 目标，并在必要时重新启动。

更新并重启后，可将此服务器配置为 iSCSI 目标。

1.  打开“服务器管理器”，依次选择“管理”、“添加角色和功能”。   

2.  打开后，依次选择“下一步”、“基于角色或基于功能的安装”，然后继续完成各项选择，直到转到“选择服务器角色”页    。

3.  展开“文件和存储服务”，然后展开“文件和 iSCSI 服务”并勾选“iSCSI 目标服务器”框，接受添加新功能的任何弹出提示，然后继续完成操作    。

    ![](./media/azure-stack-network-howto-extend-datacenter/image10.png)

    完成后，关闭“服务器管理器”  。

4.  打开“文件资源管理器”，导航到 C:\\，然后新建文件夹，命名为 iSCSI    。

5.  重新打开“服务器管理器”，然后从左侧菜单选择“文件和存储服务”   。

6.  选择 iSCSI，然后在右窗格中选择“启动新的 iSCSI 虚拟磁盘向导以创建 iSCSI 虚拟磁盘”链接   。 选择它。 此时将弹出一个向导。

7.  在“选择 iSCSI 虚拟磁盘位置”页上，选择“键入自定义路径”的单选按钮，然后浏览到 C:\\iSCSI 并选择“下一步”     。

8.  将 iSCSI 虚拟磁盘命名为 iSCSIdisk1，根据需要提供说明，然后选择“下一步”   。

9.  将虚拟磁盘的大小设置为 10GB，然后依次选择“固定大小”和“下一步”    。

    ![](./media/azure-stack-network-howto-extend-datacenter/image11.png)

10) 这是新的目标，因此请依次选择“新 iSCSI 目标”和“下一步”   。

11) 在“指定目标名称”页上，输入 TARGET1，然后选择“下一步”    。

12) 在“指定访问服务器”页上，选择“添加”   。 这将打开一个对话框，用于输入将获得授权连接 iSCSI 目标的特定发起程序  。

13) 在“添加发起程序 ID 窗口”中，选择“输入所选类型的值”，然后确保已在“类型”下选中下拉菜单中的 IQN    。 输入 iqn.1991-05.com.microsoft:\<computername>，其中 \<computername> 是 VM001 的计算机名，然后选择“下一步”     。

    ![](./media/azure-stack-network-howto-extend-datacenter/image12.png)

14) 在“启用身份验证”页上，将框留空，然后选择“下一步”   。

15) 确认选择，并选择“创建”，然后关闭  。 此时服务器管理器中应显示创建的 iSCSI 虚拟磁盘。

    ![](./media/azure-stack-network-howto-extend-datacenter/image13.png)

### <a name="configure-the-windows-server-2019-iscsi-initiator-and-mpio"></a>配置 Windows Server 2019 iSCSI 发起程序和 MPIO

若要设置 iSCSI 发起程序，请首先在 Azure Stack Hub 系统上重新登录到 Azure Stack Hub 用户门户，然后导航到 VM001 的“概述”边栏选项卡     。

1.  建立与 VM001 的 RDP 连接。 连接后，打开“服务器管理器”  。

2.  选择“添加角色和功能”，并接受默认值，直到出现“功能”页   。

3.  在“功能”页上，添加“多路径 I/O”，然后选择“下一步”    。

    ![](./media/azure-stack-network-howto-extend-datacenter/image14.png)

4.  勾选“必要时自动重启目标服务器”框并选择“安装”，然后选择“关闭”    。 很可能需要重启，完成重启后，请重新连接到 VM001。

5.  返回“服务器管理器”，等待 MPIO 安装完成，选择“关闭”，然后依次选择“工具”和“MPIO”      。

6.  选择“发现多路径”选项卡，勾选“添加对 iSCSI 设备的支持”框并选择“添加”，然后选择“确定”以重启 VM001      。 如果未弹出窗口，请选择“确定”，然后手动重启  。

    ![](./media/azure-stack-network-howto-extend-datacenter/image15.png)

7.  重启后，新建与 VM001 的 RDP 连接  。

8.  连接后，打开“服务器管理器”，然后依次选择“工具”和“iSCSI 发起程序”    。

9.  Microsoft iSCSI 窗口弹出时，选择“确定”以允许默认运行 iSCSI 服务  。

    ![](./media/azure-stack-network-howto-extend-datacenter/image16.png)

10. 在“iSCSI 发起程序属性”窗口中，选择“发现”选项卡  。

11. 现在要添加 2 个目标，首先选择“发现门户”按钮  。

12. 输入 iSCSI 目标服务器的第一个 IP 地址，然后选择“高级”  。

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

13. 在“高级设置”窗口中，选择以下内容，然后选择“确定”   。

    a.  **本地适配器**：Microsoft iSCSI 发起程序。

    b.  **发起程序 IP**：10.10.10.4。

14. 返回“发现目标门户”窗口，选择“确定”   。

15. 按以下步骤重复此过程：

    a. **IP 地址**：第二个 iSCSI 目标 IP 地址。

    b.  **本地适配器**：Microsoft iSCSI 发起程序。

    c.  **发起程序 IP**：10.10.11.4。

16. 目标门户应如下所示，你自己的 iSCSI 目标 IP 位于“地址”列下  。

    ![](./media/azure-stack-network-howto-extend-datacenter/image18.png)

17. 返回“目标”选项卡，选择窗口中间的 iSCSI 目标，然后选择“连接”   。

18. 在“连接到目标”窗口中，勾选“启用多路径”框，然后选择“高级”    。

    ![](./media/azure-stack-network-howto-extend-datacenter/image19.png)

19. 输入以下信息并选择“确定”，然后在“连接到目标”窗口中，选择“确定”    。

    a.  **本地适配器**：Microsoft iSCSI 发起程序。

    b.  **发起程序 IP**：10.10.10.4。

    c.  **目标门户 IP**：\<你的第一个 iSCSI 目标 IP/3260>。

![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

1.  对第二个发起程序/目标组合重复此过程。

    a. **本地适配器**：Microsoft iSCSI 发起程序。

    b.  **发起程序 IP**：10.10.11.4。

    c.  **目标门户 IP**：\<你的第二个 iSCSI 目标 IP/3260>。

        ![](./media/azure-stack-network-howto-extend-datacenter/image21.png)

2.  选择“卷和设备”选项卡，然后选择“自动配置”，此时系统应显示 MPIO 卷   ：

    ![](./media/azure-stack-network-howto-extend-datacenter/image22.png)

3.  返回“目标”选项卡，选择“设备”，此时系统应显示与之前创建的单个 iSCSI VHD 相连的 2 个连接   。

    ![](./media/azure-stack-network-howto-extend-datacenter/image23.png)

4.  选择“MPIO 按钮”，以查看有关负载均衡策略和路径的详细信息  。

    ![](./media/azure-stack-network-howto-extend-datacenter/image24.png)

5.  选择“确定”三次，以退出 Windows 和 iSCSI 发起程序  。

6.  打开磁盘管理 (diskmgmt.msc)，此时系统应显示“初始化磁盘”窗口  。

    ![](./media/azure-stack-network-howto-extend-datacenter/image25.png)

7.  选择“确定”接受默认值，向下滚动到新磁盘，右键单击，然后选择“新建简单卷”  

8.  接受默认值，逐步完成向导。 将卷标签更改为 iSCSIdisk1，然后选择“完成”   。

    ![](./media/azure-stack-network-howto-extend-datacenter/image26.png)

9.  接下来，系统格式化驱动器并显示驱动器号。

10. 打开“文件资源管理器”，然后选择“此电脑”以查看附加到 VM001 的新驱动器   。

### <a name="testing-external-storage-connectivity"></a>测试外部存储连接

要验证通信并运行基本的文件复制测试，首先在 Azure Stack Hub 系统上重新登录到 Azure Stack Hub 用户门户，然后导航到 VM001 的“概述”边栏选项卡    

1.  选择“连接”以便与 VM001 建立 RDP 连接  

2.  打开“任务管理器”，选择“性能”选项卡，然后将窗口与 RDP 会话的右侧对齐   。

3.  以管理员身份打开 Windows PowerShell ISE，然后将其与 RDP 会话的左侧对齐  。 关闭 ISE 右侧的“命令”窗格，然后选择“脚本”按钮，以展开 ISE 窗口顶部的白色脚本窗格   。

4.  此 VM 中没有用于创建 VHD 的本机 PowerShell 模块，我们将其用作大文件（用于测试向 iSCSI 目标的文件转移）。 在这种情况下，我们将运行 DiskPart 来创建 VHD 文件。 在 ISE 中运行以下命名：

    1. `Start-Process Diskpart`

    2. 新的 CMD 窗口打开，输入：  
        `**Create vdisk file="c:\\test.vhd" type=fixed maximum=5120**`
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image27.png)
    
    3.  创建需要一些时间。 创建后，若要验证创建的内容，请打开“文件资源管理器”，然后导航到 C:\\ - 此时应看到新的 test.vhd，大小为 5GB  。

    ![](./media/azure-stack-network-howto-extend-datacenter/image28.png)

    4. 关闭 CMD 窗口，返回到 ISE，然后在脚本窗口中输入以下命令。 将 F:\\ 替换为之前应用的 iSCSI 目标驱动器号。

    5. `Copy-Item "C:\\test.vhd" -Destination "F:\\"`

    6. 在脚本窗口中选择该行，然后按 F8 运行。

    7. 运行命令时，请观察两个网络适配器，查看 VM001 中两个网络适配器上发生的数据转移。 还应注意到，各网络适配器均匀地共享负载。

    ![](./media/azure-stack-network-howto-extend-datacenter/image29.png)

此方案旨在强调 Azure Stack Hub 上运行的工作负荷与外部存储阵列（在本例中为基于 Windows Server 的 iSCSI 目标）之间的连接性。 这并不是一种性能测试，也不能反映在使用备用的基于 iSCSI 的设备时需要执行的步骤，但它确实突出了在 Azure Stack 集线器上部署工作负荷并将其连接到 Azure Stack 中心环境以外的存储系统时需要执行的一些核心注意事项。

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 网络的差异和注意事项](azure-stack-network-differences.md)
