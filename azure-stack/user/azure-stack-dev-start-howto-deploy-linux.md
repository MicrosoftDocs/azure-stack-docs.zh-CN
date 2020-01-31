---
title: 将 Linux VM 部署到 Azure Stack 集线器
description: 将应用部署到 Azure Stack 中心。
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: b887e4e3eba0a3f109d8f338d4de3bcca4908512
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884932"
---
# <a name="deploy-a-linux-vm-to-host-a-web-app-in-azure-stack-hub"></a>部署 Linux VM 以在 Azure Stack 中心内托管 web 应用

可以使用 Azure Marketplace 中的 Ubuntu 映像创建和部署基本 Linux 虚拟机（VM），以托管使用 web 框架创建的 web 应用。 

此 VM 可通过以下方式托管 web 应用：

- **Python**：常见的 python web 框架包括 Flask、瓶和 Django。
- **开始**：常见的走向框架包括得意、Martini、Gocraft/Web 和 Gorilla。 
- **Ruby**：将 Ruby on Rails 设置为框架，以交付 ruby web 应用。 
- **Java**：使用 java 开发您发布到 Apache Tomcat 服务器的 web 应用程序。 你可以在 Linux 上安装 Tomcat，然后将 Java WAR 文件直接部署到服务器。 

使用本文中的说明来启动并运行使用 Linux 操作系统的任何 web 应用、框架和后端技术。 然后，你可以使用 Azure Stack 中心来管理你的基础结构，并使用技术内的管理工具来处理应用的维护任务。

## <a name="deploy-a-linux-vm-for-a-web-app"></a>为 web 应用部署 Linux VM

在此过程中，你将创建一个密钥，使用 Linux VM 的基本映像，指定 VM 的特定属性，然后创建 VM。 创建 VM 后，请打开与 VM 配合使用所需的端口，并打开 VM 来托管应用程序。 接下来，创建 DNS 名称。 最后，使用 apt 实用工具连接到 VM 并更新计算机。 完成该过程后，可以在 Azure Stack 中心实例中创建一个可供托管 web 应用的 VM。

在开始之前，请确保已准备好所需的一切。

## <a name="prerequisites"></a>必备组件

- Azure Stack 中心订阅，可访问 Ubuntu Server 16.04 LTS 映像。 你可以使用更高版本的映像，但这些说明是使用 16.04 LTS 编写的。 如果没有此映像，请联系你的云操作员，使其进入 Azure Stack 中心市场。

## <a name="deploy-the-vm-by-using-the-portal"></a>使用门户部署 VM

若要部署 VM，请按照以下几节中的说明进行操作。

### <a name="create-your-vm"></a>创建 VM

1. 为服务器创建安全外壳（SSH）公钥。 有关详细信息，请参阅[如何使用 SSH 公钥](azure-stack-dev-start-howto-ssh-public-key.md)。
1. 在 Azure Stack 中心门户中，选择 "**创建资源** > **计算** > **Ubuntu Server 16.04 LTS**"。

    ![将 web 应用部署到 Azure Stack 中心 VM](media/azure-stack-dev-start-howto-deploy-linux/001-portal-compute.png)

4. 在 "**创建虚拟机**" 窗格中，为**1。配置基本设置**：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 输入**VM 的名称**。

    b.保留“数据库类型”设置，即设置为“共享”。 选择**VM 磁盘类型**，**高级 SSD** （适用于高级磁盘 [SSD]）或**标准 HDD** （适用于标准磁盘 [HDD]）。

    c. 输入**用户名**。

    d.单击“下一步”。 选择 "**身份验证类型**" 作为 " **SSH 公钥**"。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 检索所创建的 SSH 公钥。 在文本编辑器中打开它，复制该密钥，然后将其粘贴到 " **SSH 公钥**" 框中。 将 `---- BEGIN SSH2 PUBLIC KEY ----` 中的文本添加到 `---- END SSH2 PUBLIC KEY ----`中。 将整个文本块粘贴到 "密钥" 框中：

    ```text  
    ---- BEGIN SSH2 PUBLIC KEY ----
    Comment: "rsa-key-20190207"
    <Your key block>
    ---- END SSH2 PUBLIC KEY ----
    ```

    f. 为 Azure Stack 中心实例选择订阅。

    g. 创建新的资源组或使用现有资源组，具体取决于你想要如何组织应用的资源。

    h. 选择你的位置。 Azure Stack 开发工具包（ASDK）通常位于*本地*区域。 位置取决于 Azure Stack 中心实例。
1. **2。大小**，请键入：
    - 为 VM 选择可用于 Azure Stack 中心实例的数据和 RAM 大小。
    - 可以按**计算类型**、 **cpu**和**存储空间**浏览列表或筛选 VM 大小。
    
    > [!NOTE]
    > - 显示的价格是以当地货币为估算值。 它们仅包含 Azure 基础结构成本，以及订阅和位置的任何折扣。 它们不包括任何适用的软件成本。 
    > - 建议的大小由所选映像的发布者决定，并且基于硬件和软件要求。
    > - 使用标准磁盘（HDD）而不是高级磁盘（SSD）可能会影响操作系统的性能。

1. 在**3 中。配置可选**功能，键入：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 为实现**高可用性，请**选择可用性集。 若要为应用程序提供冗余，请将两个或更多虚拟机组合到一个可用性集中。 此配置可确保在计划内或计划外维护事件期间，至少有一个虚拟机可用，并满足99.95% 的 Azure 服务级别协议（SLA）的要求。 虚拟机的可用性集在创建后将无法更改。

    b.保留“数据库类型”设置，即设置为“共享”。 对于 "**存储**"，请选择**高级磁盘（SSD）** 或**标准磁盘（HDD）** 。 高级磁盘（SSD）由固态硬盘支持，并提供一致的低延迟性能。 它们在价格与性能之间提供最佳平衡，适用于 i/o 密集型应用程序和生产工作负荷。 标准磁盘由磁盘驱动，并且更适用于不经常访问数据的应用程序。 区域冗余磁盘由区域冗余存储（ZRS）支持，该存储将数据复制到多个区域，即使单个区域关闭，它们仍可用。 

    c. 选择 "**使用托管磁盘**"。 当你启用此功能时，Azure 会自动管理磁盘的可用性。 你可以利用数据冗余和容错能力，而无需自行创建和管理存储帐户。 托管磁盘可能在所有区域中都不可用。 有关详细信息，请参阅[Azure 托管磁盘简介](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)。

    d.单击“下一步”。 若要配置网络，请选择 "**虚拟网络**"。 虚拟网络在 Azure 中逻辑上相互隔离。 你可以配置其 IP 地址范围、子网、路由表、网关和安全设置，这与数据中心中的传统网络非常类似。 默认情况下，同一虚拟网络中的虚拟机可以相互访问。 

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 若要配置子网，请选择 "**子网**"。 子网是虚拟网络中的一系列 IP 地址。 你可以使用子网将虚拟机彼此隔离，或将虚拟机与 internet 隔离。 

    f. 若要配置对 VM 或 VM 上运行的服务的访问，请选择 "**公共 IP 地址**"。 使用公共 IP 地址从虚拟网络外部与虚拟机进行通信。 

    g. 选择 "**网络安全组**"、"**基本**" 或 "**高级**"。 设置允许或拒绝到 VM 的网络流量的规则。 

    h. 若要将常用或自定义协议的访问权限设置到 VM，请选择 "**公用入站端口**"。 服务为此规则指定目标协议和端口范围。 你可以选择预定义的服务，如远程桌面协议（RDP）或 SSH，或提供自定义端口范围。 
        对于 web 服务器，请使用 HTTP （80）、HTTPS （443）和 SSH （22）打开。 如果计划使用 RDP 连接管理计算机，请打开端口3389。

    i. 若要将扩展添加到 VM，请选择 "**扩展**"。 扩展会向虚拟机添加新功能，如配置管理或防病毒保护。 

    j. 禁用或启用**监视**。 为了帮助诊断启动问题，你可以使用监视来捕获主机上运行的虚拟机的串行控制台输出和屏幕截图。 

    k. 若要指定保存指标的存储帐户，请选择 "**诊断存储帐户**"。 指标会写入存储帐户，以便你可以使用自己的工具进行分析。 

    l. 选择“确定”。

1. 查看**4。摘要**：
    - 门户将验证你的设置。
    - 若要在 Azure 资源管理器工作流中重复使用设置，可以下载 VM 的 Azure 资源管理器模板。
    - 验证已通过后，选择 **"确定"** 。 VM 部署花费几分钟时间。

### <a name="specify-the-open-ports-and-dns-name"></a>指定开放端口和 DNS 名称

若要使你的 web 应用可供网络上的用户访问，请打开用于连接到计算机的端口并添加友好 DNS 名称（例如*mywebapp*），用户可以在其 web 浏览器中使用该名称。

#### <a name="open-inbound-ports"></a>打开入站端口

可以修改预定义服务（如 RDP 或 SSH）的目标协议和端口范围，或提供自定义端口范围。 例如，你可能想要使用 web 框架的端口范围。 例如，在端口3000上进行通信。

1. 为租户打开 Azure Stack 中心门户。

1. 搜索你的 VM。 你可能已将 VM 固定到仪表板，或者可以在 "**搜索资源**" 框中搜索它。

1. 在 "VM" 窗格中选择 "**网络**"。

1. 选择 "**添加入站端口**规则" 以打开端口。

1. 对于 "**源**"，保留默认选择 "**任意**"。

1. 对于 "**源端口范围**"，保留通配符（*）。

1. 对于 "**目标端口范围**"，请输入要打开的端口，例如**3000**。

1. 对于 "**协议**"，保留默认选择 "**所有**"。

1. 对于“操作”，请选择“允许”。

1. 对于 "**优先级**"，保留默认选择。

1. 输入 "**名称**" 和 "**说明**"，以帮助你记住此端口处于打开状态的原因。

1. 选择 **添加** 。

#### <a name="add-a-dns-name-for-your-server"></a>为服务器添加 DNS 名称

此外，你可以为服务器创建 DNS 名称，以便用户可以通过使用 URL 连接到你的网站。

1. 为租户打开 Azure Stack 中心门户。

1. 搜索你的 VM。 你可能已将 VM 固定到仪表板，或者可以在 "**搜索资源**" 框中搜索它。

1. 选择“概述”。

1. 在 " **VM**" 下，选择 "**配置**"。

1. 对于 "**分配**"，选择 "**动态**"。

1. 输入 DNS 名称标签（如**mywebapp**），以使完整 URL 变为*mywebapp* （适用于 ASDK 应用）。

### <a name="connect-via-ssh-to-update-your-vm"></a>通过 SSH 进行连接以更新 VM

1. 在与 Azure Stack 中心实例相同的网络上，打开 SSH 客户端。 有关详细信息，请参阅[使用 SSH 公钥](azure-stack-dev-start-howto-ssh-public-key.md)。

1. 输入以下命令：

    ```bash  
        sudo apt-get update
        sudo apt-get -y upgrade
    ```

## <a name="next-steps"></a>后续步骤

了解如何[在 Azure Stack Hub 中设置开发环境](azure-stack-dev-start.md)。
