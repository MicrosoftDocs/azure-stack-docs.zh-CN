---
title: 使用 Windows 管理中心在 Azure Stack HCI 上设置 Azure Kubernetes 服务的快速入门
description: 了解如何使用 Windows 管理中心在 Azure Stack HCI 上设置 Azure Kubernetes 服务
author: davannaw-msft
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: fa07b755405fced34a404dbd2b2473450a8b8e26
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948845"
---
# <a name="quickstart-set-up-azure-kubernetes-service-on-azure-stack-hci-using-windows-admin-center"></a>快速入门：使用 Windows 管理中心在 Azure Stack HCI 上设置 Azure Kubernetes 服务

> 适用于： Azure Stack HCI

在本快速入门中，使用 Windows 管理中心在 Azure Stack HCI 上设置 Azure Kubernetes 服务。 若要改为使用 PowerShell，请参阅 [使用 powershell 进行设置](setup-powershell.md)。

设置涉及以下任务：

* 设置 Windows 管理中心（如果尚未这样做）
* 安装适用于 Windows 管理中心的 Azure Stack HCI 扩展的 Azure Kubernetes 服务
* 在要将 Kubernetes 群集部署到的系统上设置 Azure Kubernetes 服务主机

在开始之前，请确保已满足 " [系统要求](.\system-requirements.md) " 页上的所有先决条件。

## <a name="setting-up-windows-admin-center"></a>设置 Windows 管理中心

如果尚未安装 Windows 管理中心，请参阅 [安装 Windows 管理中心](https://docs.microsoft.com/windows-server/manage/windows-admin-center/deploy/install)。 有关 Azure Stack HCI 上的 Azure Kubernetes 服务的公共预览版，必须在 Windows 10 计算机上下载并运行 Windows 管理中心。 目前只有 Windows 管理中心桌面模式与 Azure Stack HCI 上的 Azure Kubernetes 服务兼容。 Azure Stack HCI 功能上的 Azure Kubernetes 服务仅适用于 Windows 管理中心版本2009或更高版本。

## <a name="installing-the-azure-kubernetes-service-extension"></a>安装 Azure Kubernetes 服务扩展

获取 Azure Stack HCI 公共预览版文件上的 Azure Kubernetes 服务后，必须将该文件保存在 `.nupkg` 本地或 SMB 共享上，并将文件路径添加到 Windows 管理中心扩展管理器的 "源" 列表中。 `.nupkg`文件是包含 Windows 管理中心扩展的 NuGet 包。

若要访问现有的扩展源，请打开 Windows 管理中心，并选择屏幕右上角的 "齿轮"。 这会转到 "设置" 菜单。 可以在 "**扩展**" 菜单的 "**网关**" 部分下找到扩展源。 导航到 " **源** " 选项卡，然后选择 " **添加**"。 在此窗格中，将文件路径粘贴到 Azure Stack HCI 扩展上的 Azure Kubernetes 服务副本，然后选择 " **添加**"。 如果已成功添加文件路径，你会收到一条成功通知。 

至此，我们已添加了源，Azure Stack HCI 扩展上的 Azure Kubernetes 服务将在可用扩展列表中提供。 选择该扩展后，请在表的顶部选择 " **安装** " 以安装此扩展。 安装完成后，将重新加载 Windows 管理中心。 

[![在 Windows 管理中心的 "扩展管理器" 中查看可用扩展列表。 ](.\media\setup\extension-manager.png)](.\media\setup\extension-manager.png#lightbox)

## <a name="setting-up-an-azure-kubernetes-service-host"></a>设置 Azure Kubernetes 服务主机

在创建 Kubernetes 群集之前，应该完成最后一个步骤。 需要在要将 Kubernetes 群集部署到的系统上设置 Azure Kubernetes 服务主机。 此系统必须是 Azure Stack HCI 群集。 

> [!NOTE] 
> 不支持在两个独立的系统上设置 Azure Kubernetes 服务主机，目的是在 Kubernetes 群集创建过程中合并它们。 

可以使用新的 Azure Kubernetes 服务工具完成这一设置。 

此工具将安装和下载所需的包，并创建提供核心 Kubernetes 服务并协调应用程序工作负载的管理群集。 

让我们开始吧： 
1. 选择 " **设置** " 启动 "设置向导"。
2. 查看正在运行 Windows 管理中心的计算机、所连接的 Azure Stack HCI 群集以及网络的先决条件。 此外，请确保已登录到 Windows 管理中心上的 Azure 帐户。 完成后，选择“下一步”。
3. 在向导的 " **系统检查** " 页上，执行所需的任何操作，例如将 Windows 管理中心网关连接到 Azure。 此步骤检查 Windows 管理中心和将托管 Azure Kubernetes 服务的系统是否具有适当的配置以继续。 完成操作后，选择 " **下一步**"。
4. 在 **主机配置** 步骤中配置将承载 Azure Kubernetes 服务的计算机。 建议选择 " **自动下载** 此部分中的更新"。 完成后，选择 " **下一步** "。 此向导步骤要求你配置以下详细信息：
    * 主机详细信息，如管理群集的名称和用于存储 VM 映像的文件夹
    * VM 网络，将应用于所有 Linux 和 Windows Vm (节点) 为运行容器和安排容器管理创建。 
    * 负载均衡器设置，用于定义用于外部服务的地址池

    ![说明 Azure Kubernetes 服务主机向导的主机配置步骤。](.\media\setup\host-configuration.png)

5. 注册到 Azure 并选择将诊断数据发送到 **azure 注册** 步骤中的 Microsoft。 尽管此页面要求提供 Azure 订阅和资源组，但在公共预览版中设置和使用 Azure Kubernetes 服务不会产生费用。 你向 Microsoft 发送的诊断数据将用于帮助保持服务的安全和最新状态、排查问题并改进产品。 做出选择后，选择 " **下一步**"。
6. 在 " **评审 + 创建** " 步骤中查看目前为止所做的所有选择。 如果你对选择满意，请选择 " **设置** " 以开始设置主机。 
7. 在 " **安装进度** " 页上，可以查看主机设置的进度。 此时，欢迎您在新选项卡中打开 Windows 管理中心并继续执行管理任务。 
8. 如果部署成功，将会出现后续步骤，并且会启用 " **完成** " 按钮。 在**后续步骤**下选择 "**下载管理群集 kubeconfig** " 将开始下载，不会将你从向导中引导。 

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何为 Azure Stack HCI 扩展安装 Windows 管理中心和 Azure Kubernetes 服务。 还在要将 Kubernetes 群集部署到的系统上配置了 Azure Kubernetes 服务主机。

你现在已准备好 [在 Windows 管理中心中创建 Kubernetes 群集](create-kubernetes-cluster.md)。
