---
title: 使用 Windows Admin Center 设置 Azure Stack HCI 上的 Azure Kubernetes 服务的快速入门
description: 了解如何使用 Windows Admin Center 设置 Azure Stack HCI 上的 Azure Kubernetes 服务
author: davannaw-msft
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: dawhite
ms.openlocfilehash: 3d8be2b6b1e3251875ca014e221c9bf4de35671e
ms.sourcegitcommit: 0e2c814cf2c154ea530a4e51d71aaf0835fb2b5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918732"
---
# <a name="quickstart-set-up-azure-kubernetes-service-on-azure-stack-hci-using-windows-admin-center"></a>快速入门：使用 Windows Admin Center 设置 Azure Stack HCI 上的 Azure Kubernetes 服务

> 适用于： Azure Stack HCI、Windows Server 2019 Datacenter

在本快速指南中，使用 Windows Admin Center 设置 Azure Stack HCI 上的 Azure Kubernetes 服务。 若要改为使用 PowerShell，请参阅[使用 PowerShell 进行设置](setup-powershell.md)。

设置涉及以下任务：

* 下载 Azure Stack HCI 上的 Azure Kubernetes 服务
* 设置 Windows Admin Center（如果尚未这样做）
* 安装适用于 Windows 管理中心的 Azure Kubernetes 服务扩展
* 在要将 Kubernetes 群集部署到的系统上设置 Azure Kubernetes 服务主机

在开始之前，请确保已满足[系统要求](.\system-requirements.md)页上的所有先决条件。

## <a name="download-azure-kubernetes-service-on-azure-stack-hci"></a>下载 Azure Stack HCI 上的 Azure Kubernetes 服务

如果尚未下载预览版软件，请参阅[评估 Azure Stack HCI 上的 AKS](https://aka.ms/AKS-HCI-Evaluate)。 系统会要求你在 Azure Stack HCI 和 Windows 管理中心上下载 AKS。

## <a name="setting-up-windows-admin-center"></a>设置 Windows Admin Center

如果尚未安装 Windows Admin Center，请参阅[安装 Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install)。 对于 Azure Stack HCI 上的 Azure Kubernetes 服务的公共预览版，必须在 Windows 10 计算机上下载并运行 Windows Admin Center。 Azure Stack HCI 上的 Azure Kubernetes 服务功能仅适用于 Windows Admin Center 版本2009 或更高版本。

## <a name="installing-the-azure-kubernetes-service-extension"></a>安装 Azure Kubernetes 服务扩展

获取了 Azure Stack HCI 上的 Azure Kubernetes 服务公共预览版文件后，必须将 `.nupkg` 文件保存在本地或 SMB 共享上，并将文件路径添加到 Windows Admin Center 扩展管理器中的“源”列表。 `.nupkg` 文件是包含 Windows Admin Center 扩展的 NuGet 包。

若要访问现有扩展源，请打开 Windows Admin Center，并选择屏幕右上角的齿轮。 这会转到设置菜单。 可以在“扩展”菜单中的“网关”部分下找到扩展源 。 导航到“源”选项卡，然后选择“添加” 。 在此窗格中，将文件路径粘贴到 Azure Kubernetes 服务扩展的副本，然后选择 " **添加**"。 如果已成功添加文件路径，你会收到一条成功通知。 

现在我们已添加该源，Azure Kubernetes 服务扩展将在可用扩展列表中提供。 选择此扩展后，选择表顶部的“安装”以安装此扩展。 安装完成之后，Windows Admin Center 会重新加载。 

[ ![Windows Admin Center 扩展管理器中的可用扩展列表的视图。](.\media\setup\extension-manager.png) ](.\media\setup\extension-manager.png#lightbox)

## <a name="setting-up-an-azure-kubernetes-service-host"></a>设置 Azure Kubernetes 服务主机

创建 Kubernetes 群集之前，应完成最后一个步骤。 需要在要将 Kubernetes 群集部署到的系统上设置 Azure Kubernetes 服务主机。 此系统可以是 Windows Server 2019 Datacenter 群集、单节点 Windows Server 2019 Datacenter 或2-4 节点 Azure Stack HCI 群集。 

> [!NOTE] 
> 不支持为了在 Kubernetes 群集创建过程中进行合并，而在两个独立系统上设置 Azure Kubernetes 服务主机。 

可以使用新 Azure Kubernetes 服务工具完成此设置。 

此工具会安装和下载所需包，以及创建提供核心 Kubernetes 服务并协调应用程序工作负载的管理群集。 


现在我们已验证了系统设置，接下来让我们开始使用吧： 
1. 选择“设置”以启动设置向导。
2. 查看正在运行 Windows 管理中心的计算机、所连接到的群集以及网络的先决条件。 此外，请确保已登录到 Windows Admin Center 上的 Azure 帐户，并且你计划使用的 Azure 订阅未过期。 完成后，选择“下一步”。
3. 在向导的“系统检查”页上，执行所有所需操作，例如将 Windows Admin Center 网关连接到 Azure。 此步骤检查 Windows 管理中心和将托管 Azure Kubernetes 服务的系统是否具有适当的配置以继续。 操作执行完成后，选择“下一步”。
4. 在“主机配置”步骤中，配置将托管 Azure Kubernetes 服务的计算机。 建议在此部分中选择自动下载更新。 完成后，选择“下一步”。 向导的此步骤要求配置以下详细信息：
    * 主机详细信息，如管理群集的名称和用于存储 VM 映像的文件夹
    * VM 网络，将应用于为运行容器和协调容器管理而所创建的所有 Linux 和 Windows VM（节点）。 
    * 负载均衡器设置，定义用于外部服务的地址池

    ![说明 Azure Kubernetes 服务主机向导的主机配置步骤。](.\media\setup\host-configuration.png)

5. 查看 " **查看 + 创建** " 步骤中的所有选择。 如果你对选择满意，请选择 " **下一步** " 以开始安装主机。 
6. 在“设置进度”页上，可以查看主机设置的进度。 此时，欢迎你在新选项卡中打开 Windows Admin Center 并继续执行管理任务。 
7. 如果部署成功，请选择 " *完成*"，此时会显示一个管理仪表板，可在其中创建和管理 Kubernetes 群集。 此仪表板（如 Azure Stack HCI 上的 Azure Kubernetes 服务的其余部分）处于预览版本中，并将在将来的版本中使用其他功能进行更新。


1. 如果部署成功，你将看到一个管理仪表板，你可以在其中创建和管理你的 Kubernetes 群集。 此仪表板（如 Azure Stack HCI 上的 Azure Kubernetes 服务的其余部分）处于预览版本中，并将在将来的版本中使用其他功能进行更新。 
 
  ![演示 Azure Stack HCI 管理仪表板上的 Azure Kubernetes 服务。](.\media\setup\dashboard.png)
 
## <a name="next-steps"></a>后续步骤

在本快速入门中，已安装 Windows 管理中心和 Azure Kubernetes 服务扩展。 还在要将 Kubernetes 群集部署到的系统上配置了 Azure Kubernetes 服务主机。

现在，你已准备好继续[在 Windows Admin Center 中创建 Kubernetes 群集](create-kubernetes-cluster.md)。
