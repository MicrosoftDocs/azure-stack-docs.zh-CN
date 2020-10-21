---
title: 使用 Windows Admin Center 设置 Azure Stack HCI 上的 Azure Kubernetes 服务的快速入门
description: 了解如何使用 Windows Admin Center 设置 Azure Stack HCI 上的 Azure Kubernetes 服务
author: davannaw-msft
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 2ac65146c78c2ed1aaf9b98ee50392a13be050c4
ms.sourcegitcommit: be445f183d003106192f039990d1fb8ee151c8d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253989"
---
# <a name="quickstart-set-up-azure-kubernetes-service-on-azure-stack-hci-using-windows-admin-center"></a>快速入门：使用 Windows Admin Center 设置 Azure Stack HCI 上的 Azure Kubernetes 服务

> 适用于：Azure Stack HCI

在本快速指南中，使用 Windows Admin Center 设置 Azure Stack HCI 上的 Azure Kubernetes 服务。 若要改为使用 PowerShell，请参阅[使用 PowerShell 进行设置](setup-powershell.md)。

设置涉及以下任务：

* Azure Stack HCI 上下载 Azure Kubernetes 服务
* 设置 Windows Admin Center（如果尚未这样做）
* 为 Windows Admin Center 安装 Azure Stack HCI 的 Azure Kubernetes 服务扩展
* 在要将 Kubernetes 群集部署到的系统上设置 Azure Kubernetes 服务主机

在开始之前，请确保已满足[系统要求](.\system-requirements.md)页上的所有先决条件。

## <a name="download-azure-kubernetes-service-on-azure-stack-hci"></a>Azure Stack HCI 上下载 Azure Kubernetes 服务

如果尚未下载预览版软件，请参阅 [在 AZURE STACK HCI 上计算 AKS](https://aka.ms/AKS-HCI-Evaluate)。 系统会要求你在 Azure Stack HCI 以及 Windows 管理中心上下载 AKS。

## <a name="setting-up-windows-admin-center"></a>设置 Windows Admin Center

如果尚未安装 Windows Admin Center，请参阅[安装 Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install)。 对于 Azure Stack HCI 上的 Azure Kubernetes 服务的公共预览版，必须在 Windows 10 计算机上下载并运行 Windows Admin Center。 目前只有 Windows Admin Center 桌面模式与 Azure Stack HCI 上的 Azure Kubernetes 服务兼容。 Azure Stack HCI 上的 Azure Kubernetes 服务功能仅适用于 Windows Admin Center 版本2009 或更高版本。

## <a name="installing-the-azure-kubernetes-service-extension"></a>安装 Azure Kubernetes 服务扩展

获取了 Azure Stack HCI 上的 Azure Kubernetes 服务公共预览版文件后，必须将 `.nupkg` 文件保存在本地或 SMB 共享上，并将文件路径添加到 Windows Admin Center 扩展管理器中的“源”列表。 `.nupkg` 文件是包含 Windows Admin Center 扩展的 NuGet 包。

若要访问现有扩展源，请打开 Windows Admin Center，并选择屏幕右上角的齿轮。 这会转到设置菜单。 可以在“扩展”菜单中的“网关”部分下找到扩展源 。 导航到“源”选项卡，然后选择“添加” 。 在此窗格中，将文件路径粘贴到 Azure Stack HCI 上的 Azure Kubernetes 服务扩展的副本，然后选择“添加”。 如果成功添加了文件路径，则你会收到成功通知。 

现在我们已添加了源，Azure Stack HCI 上的 Azure Kubernetes 服务扩展会在可用扩展列表中提供。 选择此扩展后，选择表顶部的“安装”以安装此扩展。 安装完成之后，Windows Admin Center 会重新加载。 

[ ![Windows Admin Center 扩展管理器中的可用扩展列表的视图。](.\media\setup\extension-manager.png) ](.\media\setup\extension-manager.png#lightbox)

## <a name="setting-up-an-azure-kubernetes-service-host"></a>设置 Azure Kubernetes 服务主机

创建 Kubernetes 群集之前，应完成最后一个步骤。 需要在要将 Kubernetes 群集部署到的系统上设置 Azure Kubernetes 服务主机。 此系统必须是 Azure Stack HCI 群集。 

> [!NOTE] 
> 不支持为了在 Kubernetes 群集创建过程中进行合并，而在两个独立系统上设置 Azure Kubernetes 服务主机。 

可以使用新 Azure Kubernetes 服务工具完成此设置。 

此工具会安装和下载所需包，以及创建提供核心 Kubernetes 服务并协调应用程序工作负载的管理群集。 

使用此工具之前，请打开 PowerShell，然后在每个节点上运行以下命令，以确保 Azure 登录未被任何计算机设置阻止：
```PowerShell
az login
```

现在我们已经验证了系统设置，接下来让我们开始吧： 
1. 选择“设置”以启动设置向导。
2. 查看在运行 Windows Admin Center 的计算机、所连接到的 Azure Stack HCI 群集以及网络的先决条件。 此外，请确保已登录到 Windows 管理中心上的 Azure 帐户，并且你计划使用的 Azure 订阅未过期。 完成后，选择“下一步”****。
3. 在向导的“系统检查”页上，执行所有所需操作，例如将 Windows Admin Center 网关连接到 Azure。 此步骤会检查 Windows Admin Center 以及将托管 Azure Kubernetes 服务的系统是否具有可继续进行操作的适当配置。 操作执行完成后，选择“下一步”。
4. 在“主机配置”步骤中，配置将托管 Azure Kubernetes 服务的计算机。 建议在此部分中选择自动下载更新。 完成后，选择“下一步”。 向导的此步骤要求配置以下详细信息：
    * 主机详细信息，如管理群集的名称和用于存储 VM 映像的文件夹
    * VM 网络，将应用于为运行容器和协调容器管理而所创建的所有 Linux 和 Windows VM（节点）。 
    * 负载均衡器设置，定义用于外部服务的地址池

    ![说明 Azure Kubernetes 服务主机向导的主机配置步骤。](.\media\setup\host-configuration.png)

5. 在“Azure 注册”步骤中向 Azure 注册并选择将诊断数据发送给 Microsoft。 尽管此页面要求提供 Azure 订阅和资源组，但在公共预览版中设置和使用 Azure Kubernetes 服务不会产生费用。 发送给 Microsoft 的诊断数据将用于帮助使服务保持安全和最新状态、对问题进行故障排除以及改进产品。 进行选择之后，选择“下一步”。
6. 在“审阅 + 创建”步骤中，审阅迄今为止进行的所有选择。 如果对选择满意，请选择“设置”以开始设置主机。 
7. 在“设置进度”页上，可以查看主机设置的进度。 此时，欢迎你在新选项卡中打开 Windows Admin Center 并继续执行管理任务。 
8. 如果部署成功，则会显示后续步骤，并且会启用“完成”按钮。 在“后续步骤”下选择“下载管理群集 kubeconfig”会开始下载，不会使你离开向导 。 

## <a name="next-steps"></a>后续步骤

在本快速指南中，你安装了 Windows Admin Center 和 Azure Stack HCI 的 Azure Kubernetes 服务扩展。 还在要将 Kubernetes 群集部署到的系统上配置了 Azure Kubernetes 服务主机。

现在，你已准备好继续[在 Windows Admin Center 中创建 Kubernetes 群集](create-kubernetes-cluster.md)。