---
title: 将 Linux VM 部署到 Azure Stack Hub
description: 使用 Ubuntu 映像部署 Linux 虚拟机，以便在 Azure Stack Hub 中托管 Web 应用。
author: mattbriggs
ms.topic: overview
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 3847d321c924d06aaacfadc39feac572404ff113
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525381"
---
# <a name="deploy-a-linux-vm-to-host-a-web-app-in-azure-stack-hub"></a>在 Azure Stack Hub 中部署用于托管 Web 应用的 Linux VM

可以使用 Azure 市场中的 Ubunutu 映像创建和部署基本 Linux 虚拟机 (VM)，以托管使用 Web 框架创建的 Web 应用。 

此 VM 可以托管使用以下语言编写的 Web 应用：

- **Python**：常见的 Python Web 框架包括 Flask、Bottle 和 Django。
- **Go**：常见的 Go 框架包括 Revel、Martini、Gocraft/Web 和 Gorilla。 
- **Ruby**：将 Ruby on Rails 设置为交付 Ruby Web 应用的框架。 
- **Java**：使用 Java 开发要发布到 Apache Tomcat 服务器的 Web 应用。 可将 Tomcat 安装在 Linux 上，然后将 Java WAR 文件直接部署到服务器。 

根据本文中的说明，通过任何 Web 应用、框架和使用 Linux OS 的后端技术来启动并运行。 然后可以使用 Azure Stack Hub 来管理基础结构，并使用技术内部的管理工具来处理应用的维护任务。

## <a name="deploy-a-linux-vm-for-a-web-app"></a>部署适用于 Web 应用的 Linux VM

在此过程中，你将创建机密密钥，使用 Linux VM 的基础映像，指定 VM 的特定属性，然后创建 VM。 创建 VM 后，打开运行该 VM 所需的端口，并在该 VM 上托管你的应用。 接下来，创建 DNS 名称。 最后，连接到该 VM 并使用 apt-get 实用工具更新计算机。 完成此过程后，Azure Stack Hub 实例中已有一个准备好托管 Web 应用的 VM。

在开始之前，请确保一切已准备就绪。

## <a name="prerequisites"></a>先决条件

- 一个有权访问 Ubuntu Server 16.04 LTS 映像的 Azure Stack Hub 订阅。 可以使用更高版本的映像，但请注意，本文中的说明是根据 16.04 LTS 编写的。 如果没有此映像，请联系云运营商，以将映像放入 Azure Stack Hub 市场。

## <a name="deploy-the-vm-by-using-the-portal"></a>使用门户部署 VM

若要部署 VM，请遵循后续几个部分中的说明操作。

### <a name="create-your-vm"></a>创建 VM

1. 为服务器创建安全外壳 (SSH) 公钥。 有关详细信息，请参阅[如何使用 SSH 公钥](azure-stack-dev-start-howto-ssh-public-key.md)。
1. 在 Azure Stack Hub 门户中，选择“创建资源” > “计算” > “Ubuntu Server 16.04 LTS”。   

    ![将 Web 应用部署到 Azure Stack Hub VM](media/azure-stack-dev-start-howto-deploy-linux/001-portal-computea.png)

4. 在“创建虚拟机”窗格中，对于“1.   配置基本设置”：

    a. 输入 **VM 的名称**。

    b. 选择“高级 SSD”（适用于高级磁盘 [SSD]）或“标准 HDD”（适用于标准磁盘 [HDD]）作为“VM 磁盘类型”。   

    c. 输入你的 **用户名**。

    d. 选择“SSH 公钥”作为“身份验证类型”。  

    e. 检索创建的 SSH 公钥。 在文本编辑器中打开此公钥，复制密钥并将其贴到“SSH 公钥”框中。  包含从 `---- BEGIN SSH2 PUBLIC KEY ----` 到 `---- END SSH2 PUBLIC KEY ----` 的文本。 将整个文本块贴到密钥框中：

    ```text  
    ---- BEGIN SSH2 PUBLIC KEY ----
    Comment: "rsa-key-20190207"
    <Your key block>
    ---- END SSH2 PUBLIC KEY ----
    ```

    f. 选择 Azure Stack Hub 实例的订阅。

    g. 根据要为应用组织资源的方式，创建新的资源组或使用现有的资源组。

    h.如果该值不存在，请单击“添加行”。 选择你的位置。 Azure Stack 开发工具包 (ASDK) 通常位于本地区域。  具体的位置取决于 Azure Stack Hub 实例。
1. 对于“2.  大小”，请键入：
    - 针对可在 Azure Stack Hub 实例中使用的 VM，选择数据大小和 RAM。
    - 可以浏览列表，或根据“计算类型”、“CPU”和“存储空间”筛选 VM 的大小。   
    
    > [!NOTE]
    > - 显示的价格是以当地货币计价的估算值。 它们仅包含 Azure 基础结构费用以及订阅和位置的所有折扣。 这些价格不包括任何适用的软件费用。 
    > - 建议的大小基于硬件和软件要求，由所选映像的发布者确定。
    > - 使用标准磁盘 (HDD) 而不是高级磁盘 (SSD) 可能会影响操作系统性能。

1. 在“3.  配置可选功能”中，键入：

    a. 对于“高可用性”，请选择一个可用性集。  若要为应用程序提供冗余，请将两个或更多个虚拟机组合到一个可用性集中。 这种配置可以确保在发生计划内或计划外维护事件时，至少有一个虚拟机可用，并满足 99.95% 的 Azure 服务级别协议 (SLA) 要求。 创建虚拟机后无法更改虚拟机的可用性集。

    b. 对于“存储”，请选择“高级磁盘(SSD)”或“标准磁盘(HDD)”。    高级磁盘 (SSD) 基于固态硬盘，提供一致的低延迟性能。 高级磁盘可在价格与性能之间实现最佳平衡，非常适合用于 I/O 密集型应用程序和生产工作负荷。 标准磁盘基于磁驱动器，适用于不经常访问数据的应用程序。 区域冗余磁盘由区域冗余存储 (ZRS) ，它将数据复制到多个区域，即使单个区域出现故障，也是如此。 

    c. 选择“使用托管磁盘”。  启用此功能时，Azure 会自动管理磁盘的可用性。 你可以受益于数据冗余和容错能力，而无需自行创建和管理存储帐户。 托管磁盘并非在所有区域中均可用。 有关详细信息，请参阅 [Azure 托管磁盘简介](/azure/virtual-machines/windows/managed-disks-overview)。

    d. 若要配置网络，请选择“虚拟网络”。  虚拟网络以逻辑方式在 Azure 中相互隔离。 虚拟网络与数据中心内的传统网络非常类似，可以配置其 IP 地址范围、子网、路由表、网关和安全设置。 默认情况下，同一虚拟网络中的虚拟机可以相互访问。 

    e. 若要配置子网，请选择“子网”。  子网是虚拟网络中某个范围内的 IP 地址。 可以使用子网来相互隔离虚拟机，或者将虚拟机与 Internet 相隔离。 

    f. 若要配置对 VM 或 VM 上运行的服务的访问，请选择“公共 IP 地址”。  使用公共 IP 地址可与虚拟网络外部的虚拟机通信。 

    g. 选择“基本”或“高级”**网络安全组**。   设置允许或拒绝发往 VM 的网络流量的规则。 

    h.如果该值不存在，请单击“添加行”。 若要设置使用常用或自定义协议对 VM 设置的访问，请选择“公共入站端口”。  该服务会指定此规则的目标协议和端口范围。 可以选择预先定义的服务（例如远程桌面协议 (RDP) 或 SSH），或提供自定义端口范围。 
        对于 Web 服务器，请使用打开的 HTTP (80)、HTTPS (443) 和 SSH (22)。 如果你打算使用 RDP 连接来管理计算机，请打开端口 3389。

    i. 若要将扩展添加到 VM，请选择“扩展”。  扩展可为虚拟机添加新功能，例如配置管理或防病毒保护。 

    j. 禁用或启用“监视”。  若要帮助诊断启动问题，可以使用监视功能来捕获串行控制台输出，以及主机上运行的虚拟机的屏幕截图。 

    k. 若要指定用于保存指标的存储帐户，请选择“诊断存储帐户”。  指标将写入存储帐户，你可以使用自己的工具对其进行分析。 

    l. 选择“确定”  。

1. 复查“4.  摘要”：
    - 门户将验证你的设置。
    - 若要将设置重复用于 Azure 资源管理器工作流，可以下载适用于你的 VM 的 Azure 资源管理器模板。
    - 通过验证后，选择“确定”。  VM 部署需要花费几分钟时间。

### <a name="specify-the-open-ports-and-dns-name"></a>指定打开的端口和 DNS 名称

若要使网络中的用户能够访问你的 Web 应用，请打开用于连接到计算机的端口，并添加可让用户在其 Web 浏览器中使用的 DNS 友好名称（例如 *mywebapp.local.cloudapp.azurestack.external*）。

#### <a name="open-inbound-ports"></a>打开入站端口

可以修改预定义服务（例如 RDP 或 SSH）的目标协议和端口范围，或提供自定义端口范围。 例如，你可能想要使用 Web 框架的端口范围。 GO，例如在端口 3000 上通信。

1. 打开租户的 Azure Stack Hub 门户。

1. 搜索你的 VM。 你可能已将 VM 固定到仪表板；或者，可以在“搜索资源”框中搜索该 VM。 

1. 在 VM 窗格中选择“网络”。 

1. 选择“添加入站端口”规则以打开一个端口。 

1. 对于“源”，请保留默认选项“任何”。  

1. 对于“源端口范围”，请保留通配符 (*)。 

1. 对于“目标端口范围”，请输入要打开的端口，例如 **3000**。 

1. 对于“协议”，请保留默认选项“任何”。  

1. 对于“操作”，请选择“允许”。  

1. 对于“优先级”，请保留默认选项。 

1. 输入 **名称** 和 **说明**，以帮助记住打开端口的原因。

1. 选择“添加”   。

#### <a name="add-a-dns-name-for-your-server"></a>添加服务器的 DNS 名称

此外，可以创建服务器的 DNS 名称，使用户能够使用 URL 连接到你的网站。

1. 打开租户的 Azure Stack Hub 门户。

1. 搜索你的 VM。 你可能已将 VM 固定到仪表板；或者，可以在“搜索资源”框中搜索该 VM。 

1. 选择“概述”。 

1. 在“VM”下选择“配置”。  

1. 对于“分配”，请选择“动态”。  

1. 输入 DNS 名称标签（例如 **mywebapp**），因此，完整的 URL 将是 *mywebapp.local.cloudapp.azurestack.external*（适用于 ASDK 应用）。

### <a name="connect-via-ssh-to-update-your-vm"></a>通过 SSH 进行连接以更新 VM

1. 在 Azure Stack Hub 实例所在的同一网络中打开 SSH 客户端。 有关详细信息，请参阅[使用 SSH 公钥](azure-stack-dev-start-howto-ssh-public-key.md)。

1. 输入以下命令：

    ```bash  
        sudo apt-get update
        sudo apt-get -y upgrade
    ```

## <a name="next-steps"></a>后续步骤

了解如何[在 Azure Stack Hub 中设置开发环境](azure-stack-dev-start.md)。
