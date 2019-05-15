---
title: 将 Linux VM 部署到 Azure Stack |Microsoft Docs
description: 将应用部署到 Azure Stack。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 61d9f21f35edf1a0e8ebf61c81580c4d53218970
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617676"
---
# <a name="deploy-a-linux-vm-to-host-a-web-app-in-azure-stack"></a>部署 Linux VM 托管在 Azure Stack 中的 web 应用

可以创建和部署通过使用 Azure Marketplace 中的 Ubuntu 映像托管 web 应用与 web 框架创建基本的 Linux 虚拟机 (VM)。 

此 VM 可以通过使用托管的 web 应用：

- **Python**：常见的 Python web 框架包括 Flask、 Bottle 和 Django。
- **转**:常见转框架包括聚会、 Martini、 Gocraft/web 和 Gorilla。 
- **Ruby**:将设置的 Ruby on Rails 为一个框架来交付 Ruby web 应用。 
- **Java**：使用 Java 进行开发 web 应用程序发布到 Apache Tomcat 服务器。 可以在 Linux 上安装 Tomcat，然后 Java WAR 文件将直接部署到服务器。 

使用本文中的说明以了解启动并运行任何 web 应用程序、 框架和使用 Linux OS 的后端技术。 然后可以使用 Azure Stack 管理基础结构并使用你的技术中的管理工具来处理您的应用程序的维护任务。

## <a name="deploy-a-linux-vm-for-a-web-app"></a>部署 web 应用的 Linux VM

在此过程中，您将创建密钥、 使用 Linux VM 的基本映像，指定 VM 的特定属性，然后创建 VM。 创建 VM 后，打开所必需的使用 VM 的 vm 来托管应用的端口。 接下来，您创建的 DNS 名称。 最后，连接到 VM，并使用 apt get 实用程序更新的计算机。 完成该过程后，您必须 VM 已准备好 web 应用托管在 Azure Stack 实例中。

在开始之前，请确保已在位置所需的一切。

## <a name="prerequisites"></a>必备组件

- Azure Stack 订阅，有权访问 Ubuntu Server 16.04 LTS 映像。 可以使用更高版本的映像，但这些说明编写与记住 16.04 LTS。 如果没有此映像，请联系云操作员到 Azure Stack marketplace 中获取图像。

## <a name="deploy-the-vm-by-using-the-portal"></a>使用门户部署 VM

若要部署 VM，请执行以下各部分中的说明。

### <a name="create-your-vm"></a>创建 VM

1. 创建你的服务器的安全外壳 (SSH) 公钥。 有关详细信息，请参阅[如何使用 SSH 公钥](azure-stack-dev-start-howto-ssh-public-key.md)。
1. 在 Azure Stack 门户中，选择**创建资源** > **计算** > **Ubuntu Server 16.04 LTS**。

    ![将 web 应用部署到 Azure Stack VM](media/azure-stack-dev-start-howto-deploy-linux/001-portal-compute.png)

4. 在中**创建虚拟机**窗格中，为**1。配置基本设置**:

    a. 输入**VM 的名称**。

    b. 选择**VM 磁盘类型**、 任一**高级 SSD** （适用于高级磁盘 [SSD]） 或**标准 HDD** （适用于标准磁盘 [HDD]）。

    c. 输入你**用户名**。

    d. 选择**身份验证类型**作为**的 SSH 公钥**。

    e. 检索您创建的 SSH 公钥。 在文本编辑器中打开它，复制密钥，并将其粘贴到**SSH 公钥**框。 包括中的文本`---- BEGIN SSH2 PUBLIC KEY ----`到`---- END SSH2 PUBLIC KEY ----`。 将整个文本块粘贴到密钥框中：

    ```text  
    ---- BEGIN SSH2 PUBLIC KEY ----
    Comment: "rsa-key-20190207"
    <Your key block>
    ---- END SSH2 PUBLIC KEY ----
    ```

    f. 选择 Azure Stack 实例的订阅。

    g. 创建新的资源组或使用现有帐户，具体取决于你想要组织您的应用程序的资源。

    h. 选择你的位置。 Azure Stack 开发工具包 (ASDK) 是通常在*本地*区域。 位置取决于你的 Azure Stack 实例。
1. 有关**2。大小**，类型：
    - 选择 VM，可在 Azure Stack 实例中的数据和 RAM 的大小。
    - 您可以浏览列表或筛选器的 VM 的大小**计算类型**， **Cpu**，并**存储空间**。
    
    > [!NOTE]
    > - 下列价格是你当地货币的估计值。 它们只包含 Azure 基础结构成本以及任何针对订阅和位置的折扣。 它们不包括任何适用的软件成本。 
    > - 建议的大小由所选映像的发布者，并且基于硬件和软件要求。
    > - 使用标准磁盘 (HDD) 而不高级磁盘 (SSD) 可能会影响操作系统的性能。

1. 在**3。配置可选**功能中，键入：

    a. 有关**高可用性，** 选择可用性集。 若要向你的应用程序提供冗余，组在可用性集中的两个或多个虚拟机。 此配置可以确保在计划内或计划外维护事件期间至少一个虚拟机将可用，并满足 99.95 %azure 服务级别协议 (SLA)。 在创建后无法更改虚拟机的可用性集。

    b. 有关**存储**，选择**高级磁盘 (SSD)** 或**标准磁盘 (HDD)**。 高级磁盘 (SSD) 基于固态硬盘，提供一致的低延迟性能。 它们提供价格和性能之间的最佳平衡，并且是 O 能力的应用程序和生产工作负荷的理想选择。 标准磁盘由磁驱动器支持，是数据不常访问的应用程序。 区域冗余存储 (ZRS) 将数据复制跨多个区域，由支持区域冗余的磁盘，它们是可用的即使单个区域出现故障。 

    c. 选择**使用托管磁盘**。 当启用此功能时，Azure 自动管理磁盘的可用性。 您受益于数据冗余和容错功能，而无需创建和管理自己的存储帐户。 托管的磁盘在所有区域中可能不可用。 有关详细信息，请参阅[简介 Azure 托管磁盘](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview)。

    d. 若要配置你的网络，请选择**虚拟网络**。 虚拟网络是逻辑上相互隔离的 Azure 中。 可以配置他们的 IP 地址范围、 子网、 路由表、 网关和安全设置，类似于你的数据中心中的传统网络。 在同一虚拟网络中的虚拟机可以相互访问默认情况下。 

    e. 若要配置你的子网，请选择**子网**。 子网是虚拟网络中的一系列 IP 地址。 可以使用子网来隔离各个或来自 internet 的虚拟机。 

    f. 若要配置到 VM 或 VM 上运行的服务的访问，请选择**公共 IP 地址**。 使用公共 IP 地址与虚拟机从虚拟网络外部进行通信。 

    g. 选择**网络安全组**，**基本**，或**高级**。 设置规则，用于允许或拒绝对 VM 的网络流量。 

    h. 若要设置为你的 VM 的常见或自定义协议的访问权限，请选择**公共入站的端口**。 该服务指定此规则的目标协议和端口范围。 您可以选择预定义的服务，例如远程桌面协议 (RDP) 或 ssh 连接，或提供自定义端口范围。 
        对于 web 服务器，使用 HTTP (80)，HTTPS (443) 和 SSH (22) 打开。 如果你打算使用 RDP 连接来管理计算机，打开端口 3389。

    i. 若要将扩展添加到你的 VM，请选择**扩展**。 扩展将新功能，如配置管理或防病毒保护添加到你的虚拟机。 

    j. 禁用或启用**监视**。 为了帮助诊断启动问题，可用于监视捕获串行控制台输出和在主机运行的虚拟机的屏幕截图。 

    k. 若要指定保存你的度量值的存储帐户，请选择**诊断存储帐户**。 度量值将写入存储帐户，以便可以使用你自己的工具对其进行分析。 

    l. 选择“确定”。

1. 查看**4。摘要**:
    - 在门户中验证你的设置。
    - 若要重复使用你的 Azure 资源管理器的工作流使用的设置，可以为 VM 下载 Azure 资源管理器模板。
    - 在经过验证，则选择**确定**。 部署 VM 需要几分钟时间。

### <a name="specify-the-open-ports-and-dns-name"></a>指定打开的端口和 DNS 名称

若要使用户可以访问网络上的 web 应用，打开用于连接到计算机并添加一个友好的 DNS 名称，例如端口*mywebapp.local.cloudapp.azurestack.external*，用户可以使用在 web 浏览器.

#### <a name="open-inbound-ports"></a>打开入站的端口

可以修改预定义的服务，如 RDP 或 SSH，目标协议和端口范围，或提供自定义端口范围。 例如，你可能想要使用的 web 框架的端口范围。 转到，例如，在端口 3000 上通信。

1. 打开你的租户 Azure Stack 门户。

1. 搜索你的 VM。 你可能具有固定 VM 到你的仪表板，或者，可以搜索中为其**搜索资源**框。

1. 选择**网络**VM 窗格中。

1. 选择**添加入站的端口**规则以打开一个端口。

1. 有关**源**，保留默认选择**任何**。

1. 有关**源端口范围**，保留通配符 （*）。

1. 有关**目标端口范围**，输入你想要打开，如端口**3000**。

1. 有关**协议**，保留默认选择**任何**。

1. 对于“操作”，请选择“允许”。

1. 有关**优先级**，保留默认选择。

1. 输入**名称**并**说明**以帮助您记起为什么的端口已打开。

1. 选择 **添加** 。

#### <a name="add-a-dns-name-for-your-server"></a>添加你的服务器的 DNS 名称

此外，可以创建您的服务器的 DNS 名称，以便用户可以通过使用 URL 连接到你的网站。

1. 打开你的租户 Azure Stack 门户。

1. 搜索你的 VM。 你可能具有固定 VM 到你的仪表板，或者，可以搜索中为其**搜索资源**框。

1. 选择“概述”。

1. 下**VM**，选择**配置**。

1. 有关**赋值**，选择**动态**。

1. 输入 DNS 名称标签，如**mywebapp**，以便将完整 URL 将变为*mywebapp.local.cloudapp.azurestack.external* （对于 ASDK 应用）。

### <a name="connect-via-ssh-to-update-your-vm"></a>通过以更新 VM 的 SSH 连接

1. 在同一网络中与 Azure Stack 实例，打开 SSH 客户端。 有关详细信息，请参阅[使用 SSH 公钥](azure-stack-dev-start-howto-ssh-public-key.md)。

1. 输入以下命令：

    ```bash  
        sudo apt-get update
        sudo apt-get -y upgrade
    ```

## <a name="next-steps"></a>后续步骤

了解如何[设置 Azure Stack 中的开发环境](azure-stack-dev-start.md)。
