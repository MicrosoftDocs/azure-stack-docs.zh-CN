---
title: 部署 Azure Stack HCI 操作系统
description: 了解如何部署 Azure Stack HCI OS，并使用 Windows 管理中心来连接到服务器。 了解如何创建服务器群集，并了解如何为服务器获取最新的 Windows 更新和固件。
author: JohnCobb1
ms.author: v-johcob
ms.topic: tutorial
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/24/2020
ms.openlocfilehash: 657064792f27a3649335c30a3390a7efddf0bb2a
ms.sourcegitcommit: 034e61836038ca75199a0180337257189601cd12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91230526"
---
# <a name="deploy-the-azure-stack-hci-operating-system"></a>部署 Azure Stack HCI 操作系统

> 适用于：Azure Stack HCI 版本 20H2

在 [部署 AZURE STACK Hci 之前](before-you-start.md#install-windows-admin-center)完成中的步骤后，部署 Azure Stack hci 的第一步是 [下载 Azure Stack HCI](https://azure.microsoft.com/en-us/products/azure-stack/hci/hci-download/) 并在要进行群集的每个服务器上安装操作系统。 本文介绍了部署该操作系统的各种方法，以及如何使用 Windows Admin Center 来连接到服务器。

在部署操作系统之后，可以使用关于如何创建服务器群集的相关指南，并根据[创建 Azure Stack HCI 群集](create-cluster.md)中所述，为服务器获取最新的 Windows 更新和固件更新。

## <a name="prerequisites"></a>必备条件
- 已在某个系统上安装了 Windows Admin Center 且该系统可以访问要加入群集的服务器，如[部署 Azure Stack HCI 之前的准备工作](before-you-start.md#install-windows-admin-center)中所述。
- 一种 Azure Stack HCI 解决方案，该解决方案根据你首选的硬件供应商、Azure Stack HCI 操作系统和 Azure 服务提供 Microsoft 验证的硬件，如 [Azure Stack HCI 解决方案](https://azure.microsoft.com/products/azure-stack/hci/)中所述。

## <a name="deployment-preparation"></a>部署准备
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

### <a name="server-manufacturer-preinstallation"></a>服务器制造商预安装
对于 Azure Stack HCI 操作系统的企业部署，建议使用首选硬件合作伙伴提供的 Azure Stack HCI 集成系统解决方案硬件。 该解决方案硬件预先安装了操作系统，并支持使用 Windows Admin Center 来部署和更新硬件制造商提供的驱动程序和固件。

解决方案硬件范围为2到16个节点，由 Microsoft 和合作伙伴供应商进行测试和验证。 若要查找首选硬件合作伙伴提供的 Azure Stack HCI 解决方案硬件，请参阅 [Azure Stack HCI 目录](https://www.microsoft.com/cloud-platform/azure-stack-hci-catalog)。

### <a name="headless-deployment"></a>无外设部署
你可以使用应答文件来执行操作系统的无外设部署。 应答文件使用 XML 格式来定义在操作系统的无人参与安装过程中使用的配置设置和值。

对于此部署选项，你可以使用 Windows 系统映像管理器创建一个 unattend.xml 应答文件，以便在服务器上部署操作系统。 Windows 系统映像管理器使用包含组件部分的图形工具创建无人参与应答文件，以定义配置问题的“答案”并确保文件中的格式和语法正确。
Windows 系统映像管理器工具在 Windows 评估和部署工具包 (Windows ADK) 中提供。 开始操作：[下载并安装 Windows ADK](/windows-hardware/get-started/adk-install)。

### <a name="system-center-virtual-machine-manager-vmm-deployment"></a>System Center Virtual Machine Manager (VMM) 部署
System Center Virtual Machine Manager (VMM) 是 System Center 套件的一部分。 你可以使用 VMM 在裸机硬件上部署 Azure Stack HCI 操作系统，并将服务器加入群集。 若要了解 VMM，请参阅 [System Center Virtual Machine Manager 的系统要求](/system-center/vmm/system-requirements)。

若要详细了解如何使用 VMM 来执行操作系统裸机部署，请参阅[从裸机计算机预配 Hyper-V 主机或群集](/system-center/vmm/hyper-v-bare-metal?view=sc-vmm-2019)。

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
- 如果默认自动生成的服务器名称不能满足您的需要，请重命名服务器。
- 将服务器加入到 Active Directory 域。
- 将你的域用户帐户或指定的域组添加到本地管理员组。
- 如果计划从本地子网外部管理服务器并决定目前不加入域，请启用对 Windows 远程管理 (WinRM) 的访问权限。 （默认防火墙规则同时允许从本地子网和 Active Directory 域服务中的任何子网进行管理。）

在每台服务器上使用 Sconfig 根据需要配置操作系统后，可以使用 Windows Admin Center 中的“群集创建”向导将服务器加入群集。

## <a name="next-steps"></a>后续步骤
若要执行与本文相关的下一个管理任务，请参阅：
> [!div class="nextstepaction"]
> [创建 Azure Stack HCI 群集](../deploy/create-cluster.md)
