---
title: 部署 Azure Stack HCI 操作系统
description: 了解如何部署 Azure Stack HCI 操作系统，然后使用 Windows Admin Center 来连接到服务器。 了解如何创建服务器群集，并了解如何为服务器获取最新的 Windows 更新和固件。
author: JohnCobb1
ms.author: v-johcob
ms.topic: tutorial
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/7/2020
ms.openlocfilehash: 51a4411e95207d2f7b544fdf507fe8bd8fc98f2e
ms.sourcegitcommit: 61556b7b6e029e3a26a4b7ef97f0b13fbe7cd5a5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/07/2020
ms.locfileid: "96761705"
---
# <a name="deploy-the-azure-stack-hci-operating-system"></a>部署 Azure Stack HCI 操作系统

> 适用于：Azure Stack HCI 版本 20H2

部署 Azure Stack HCI 的第一步是在要加入群集的每个服务器上[下载 Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) 并安装该操作系统。 本文介绍了部署该操作系统的各种方法，以及如何使用 Windows Admin Center 来连接到服务器。

> [!NOTE]
> 如果已从 [Azure Stack HCI 目录](https://aka.ms/azurestackhcicatalog)通过你喜欢的 Microsoft 硬件合作伙伴购买 Azure Stack HCI 集成系统解决方案硬件，则 Azure Stack HCI 操作系统应已预安装。 在这种情况下，可以跳过此步骤，并继续[创建 Azure Stack HCI 群集](create-cluster.md)。

## <a name="prerequisites"></a>先决条件

在部署 Azure Stack HCI 操作系统之前，需要：

- 确定硬件是否符合 Azure Stack HCI 群集的要求
- 收集成功部署所需的信息
- 在管理 PC 或服务器上安装 Windows Admin Center·

关于 Azure Stack HCI 上 Azure Kubernetes 服务的要求，请参阅 [Azure Stack HCI 上的 AKS 要求](../../aks-hci/overview.md#what-you-need-to-get-started)。

### <a name="determine-hardware-requirements"></a>确定硬件要求

Microsoft 建议从我们的合作伙伴购买经验证的 Azure Stack HCI 硬件/软件解决方案。 这些解决方案是依据我们的参考体系结构设计和汇编的，并且经过了验证，能够确保兼容性和可靠性，因此你可以快速起步和运行。 检查所用的系统、组件、设备和驱动程序是否是通过 Windows Server 目录认证的 Windows Server 2019。 请访问 [Azure Stack HCI 解决方案](https://azure.microsoft.com/overview/azure-stack/hci)网站获取经验证的解决方案。

至少需要两台服务器、服务器之间可靠的高带宽、低延迟的网络连接，以及物理连接到每个服务器的 SATA、SAS、NVMe 或持久化内存驱动器。

但是，硬件要求可能会因要部署的群集的大小和配置而异。 若要确保部署成功，请查看 Azure Stack HCI [系统要求](../concepts/system-requirements.md)。

### <a name="gather-information"></a>收集信息

若要为部署做准备，请收集有关你的环境的以下详细信息：

- **服务器名称：** 熟悉你的组织的计算机、文件、路径和其他资源的命名策略。 你需要预配多台服务器，每台都有唯一的名称。
- **域名：** 熟悉你的组织用于域命名和域加入的策略。 你需要将服务器加入域，且需指定域名。
- **静态 IP 地址：** Azure Stack HCI 要求为存储和工作负荷 (VM) 流量使用静态 IP 地址，不支持通过 DHCP 为此高速网络分配动态 IP 地址。 你可以为管理网络适配器使用 DHCP，除非你在一个团队中使用两个适配器，在这种情况下，你仍需使用静态 IP。 有关群集中每台服务器应该使用的 IP 地址，请询问网络管理员。
- **RDMA 网络：** 有两种 RDMA 协议：iWarp 和 RoCE。 请记下你的网络适配器使用哪一种。如果是 RoCE，还请记下版本（v1 或 v2）。 对于 RoCE，还请记下架顶式交换机的型号。
- **VLAN ID：** 请记下用于服务器上的网络适配器的 VLAN ID（如果有）。 应该能够从网络管理员处获取此信息。
- **站点名称：** 对于延伸群集，将使用两个站点以便进行灾难恢复。 可以使用 [Active Directory 域服务](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)设置站点，也可以让创建群集向导自动为你设置站点。 有关站点设置的问题，可以咨询域管理员。

### <a name="install-windows-admin-center"></a>安装 Windows Admin Center

Windows Admin Center 是一个本地部署的基于浏览器的应用，用于管理 Azure Stack HCI。 若要[安装 Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install)，最简单的方法是在本地管理 PC 上安装（桌面模式），但也可以在服务器上安装（服务模式）。

如果在服务器上安装 Windows Admin Center，则那些需要 CredSSP 的任务（例如创建群集以及安装更新和扩展）使用的帐户必须是 Windows Admin Center 服务器上的“网关管理员”组的成员。 有关详细信息，请参阅[配置用户访问控制和权限](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions)的前两个部分。

## <a name="prepare-hardware-for-deployment"></a>准备好硬件用于部署

为 Azure Stack HCI 解决方案获取服务器硬件后，就可以将其连接到机架并为其布线了。 请使用以下步骤为操作系统的部署准备服务器硬件。

1. 将你想要在服务器群集中使用的所有服务器节点放到机架中。
1. 将服务器节点连接到网络交换机。
1. 根据 Azure Stack HCI 硬件供应商的建议配置服务器的 BIOS 或统一可扩展固件接口 (UEFI)，以最大程度地提高性能和可靠性。

## <a name="operating-system-deployment-options"></a>操作系统部署选项

你可以使用与部署其他 Microsoft 操作系统相同的方式来部署 Azure Stack HCI 操作系统：

- 服务器制造商预安装。
- 使用应答文件进行无外设部署。
- System Center Virtual Machine Manager (VMM)。
- 网络部署。
- 手动部署，方法是将键盘和监视器直接连接到数据中心的服务器硬件，或将 KVM 硬件设备连接到服务器硬件。

### <a name="server-manufacturer-pre-installation"></a>服务器制造商预安装

对于 Azure Stack HCI 操作系统的企业部署，建议使用首选硬件合作伙伴提供的 Azure Stack HCI 集成系统解决方案硬件。 该解决方案硬件预先安装了操作系统，并支持使用 Windows Admin Center 来部署和更新硬件制造商提供的驱动程序和固件。

解决方案硬件范围为2到16个节点，由 Microsoft 和合作伙伴供应商进行测试和验证。 若要查找首选硬件合作伙伴提供的 Azure Stack HCI 解决方案硬件，请参阅 [Azure Stack HCI 目录](https://www.microsoft.com/cloud-platform/azure-stack-hci-catalog)。

### <a name="headless-deployment"></a>无外设部署

你可以使用应答文件来执行操作系统的无外设部署。 应答文件使用 XML 格式来定义在操作系统的无人参与安装过程中使用的配置设置和值。

对于此部署选项，你可以使用 Windows 系统映像管理器创建一个 unattend.xml 应答文件，以便在服务器上部署操作系统。 Windows 系统映像管理器使用包含组件部分的图形工具创建无人参与应答文件，以定义配置问题的“答案”并确保文件中的格式和语法正确。
Windows 系统映像管理器工具在 Windows 评估和部署工具包 (Windows ADK) 中提供。 开始操作：[下载并安装 Windows ADK](/windows-hardware/get-started/adk-install)。

### <a name="system-center-virtual-machine-manager-vmm-deployment"></a>System Center Virtual Machine Manager (VMM) 部署

可以使用 System Center Virtual Machine Manager 在裸机硬件上部署 Azure Stack HCI 操作系统，并将服务器加入群集。 若要了解 VMM，请参阅 [System Center Virtual Machine Manager 的系统要求](/system-center/vmm/system-requirements)。

若要详细了解如何使用 VMM 来执行操作系统裸机部署，请参阅[从裸机计算机预配 Hyper-V 主机或群集](/system-center/vmm/hyper-v-bare-metal)。

### <a name="network-deployment"></a>网络部署

另一种方法是使用 [Windows 部署服务](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831764(v=ws.11))通过网络安装 Azure Stack HCI 操作系统。

### <a name="manual-deployment"></a>手动部署

若要在要加入群集的每台服务器的系统驱动器上手动部署 Azure Stack HCI 操作系统，请通过你的首选方法（如从 DVD 或 USB 驱动器启动）安装操作系统。 使用服务器配置工具 (Sconfig) 完成安装过程，以准备用于创建群集的服务器。 若要详细了解该工具，请参阅[使用 Sconfig 配置服务器核心安装](/windows-server/get-started/sconfig-on-ws2016)。

若要手动安装 Azure Stack HCI 操作系统，请执行以下操作：
1. 在要安装操作系统的服务器的系统驱动器上，启动“安装 Azure Stack HCI”向导。
1. 选择要安装的语言或接受默认的语言设置，选择“下一步”，然后在向导的下一页上选择“立即安装”。

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-language.png" alt-text="“安装 Azure Stack HCI”向导的语言页。":::

1. 在“适用的声明和许可条款”页上查看许可条款，选中“我接受许可条款”复选框，然后选择“下一步”。
1. 在“你想执行哪种类型的安装?”页面上，选择“自定义:仅安装较新版本的 Azure Stack HCI (高级)”。

    > [!NOTE]
    > 此版本的操作系统不支持升级安装。

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-which-type.png" alt-text="“安装 Azure Stack HCI”向导的安装类型选项页。":::

1. 在“你想将 Azure Stack HCI 安装在哪里?”页面上，确认你想要安装操作系统的驱动器位置或更新该位置，然后选择“下一步”。

    :::image type="content" source="../media/operating-system/azure-stack-hci-install-where.png" alt-text="“安装 Azure Stack HCI”向导的驱动器位置页。":::

1. 此时会出现“正在安装 Azure Stack HCI”页，以显示过程状态。

    :::image type="content" source="../media/operating-system/azure-stack-hci-installing.png" alt-text="“安装 Azure Stack HCI”向导的状态页。":::

    > [!NOTE]
    > 安装过程会两次重启操作系统来完成该过程，并在打开管理员命令提示之前显示有关启动服务的通知。

1. 在管理员命令提示符下，选择 **"确定"** 以在登录到操作系统之前更改用户的密码，然后按 enter。

    :::image type="content" source="../media/operating-system/azure-stack-hci-change-admin-password.png" alt-text="更改密码提示。":::

1. 在“为管理员输入新凭据”提示下输入一个新密码，再次输入该密码进行确认，然后按 Enter。
1. 在“你的密码已更改”确认提示下，按 Enter。

    :::image type="content" source="../media/operating-system/azure-stack-hci-admin-password-changed.png" alt-text="“已更改密码”确认提示":::

现在，你可以使用服务器配置工具 (Sconfig) 来执行重要任务了。 若要使用 Sconfig，请登录到运行 Azure Stack HCI 操作系统的服务器。 这可以通过键盘和监视器在本地进行，也可以使用远程管理（无外设或 BMC）控制器或远程桌面来进行。 当你登录到服务器时，Sconfig 工具会自动打开。

:::image type="content" source="../media/operating-system/azure-stack-hci-sconfig-screen.png" alt-text="服务器配置工具界面。" lightbox="../media/operating-system/azure-stack-hci-sconfig-screen.png":::

在 Sconfig 工具的主页中，你可以执行以下初始配置任务：
- 配置网络，或确认已使用动态主机配置协议 (DHCP) 自动配置了网络。
- 如果默认的自动生成的服务器名称不能满足你的需要，请重命名服务器。
- 将服务器加入到 Active Directory 域。
- 将你的域用户帐户或指定的域组添加到本地管理员组。
- 如果计划从本地子网外部管理服务器并决定目前不加入域，请启用对 Windows 远程管理 (WinRM) 的访问权限。 （默认防火墙规则同时允许从本地子网和 Active Directory 域服务中的任何子网进行管理。）

在每台服务器上使用 Sconfig 根据需要配置操作系统后，可以使用 Windows Admin Center 中的“群集创建”向导将服务器加入群集。

## <a name="next-steps"></a>后续步骤

若要执行与本文相关的下一个管理任务，请参阅：
> [!div class="nextstepaction"]
> [创建 Azure Stack HCI 群集](../deploy/create-cluster.md)
