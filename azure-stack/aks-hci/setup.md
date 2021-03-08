---
title: 使用 Windows Admin Center 设置 Azure Stack HCI 上的 Azure Kubernetes 服务的快速入门
description: 了解如何使用 Windows Admin Center 设置 Azure Stack HCI 上的 Azure Kubernetes 服务
author: davannaw-msft
ms.topic: quickstart
ms.date: 01/22/2021
ms.author: dawhite
ms.openlocfilehash: 4f4b06c671df668f3dd99b15a95dc29aae446c55
ms.sourcegitcommit: 192701cd691d0f13ae65f19627bb8ae7fc81bbc9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2021
ms.locfileid: "102451296"
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

如果尚未下载预览版软件，请参阅[评估 Azure Stack HCI 上的 AKS](https://aka.ms/AKS-HCI-Evaluate)。 系统会要求你在 Azure Stack HCI 上下载 AKS。 请确保不要在此页上安装 Windows 管理中心版本，因为它指向2103版本，如果为你的部署选择了 Windows 管理中心版本2103，则某些 AKS 的功能可能无法按预期方式工作。 

## <a name="setting-up-windows-admin-center"></a>设置 Windows Admin Center

如果尚未安装 Windows Admin Center，请参阅[安装 Windows Admin Center](http://aka.ms/aks-hci-wac-2009)。 您可以在 Windows 10 计算机或服务器上运行 Windows 管理中心。 **Azure Stack HCI 功能上的 Azure Kubernetes 服务最适用于 Windows 管理中心版本2009。请确保下载并安装版本2009，以便在 Azure Stack HCI 上部署 AKS 群集。** 我们正在致力于将 AKS-HCI 与 Windows 管理中心版本2103集成，如果你选择对部署使用版本2103，则某些功能可能无法按预期工作。

## <a name="installing-the-azure-kubernetes-service-extension"></a>安装 Azure Kubernetes 服务扩展

获取了 Azure Stack HCI 上的 Azure Kubernetes 服务公共预览版文件后，必须将 `.nupkg` 文件保存在本地或 SMB 共享上，并将文件路径添加到 Windows Admin Center 扩展管理器中的“源”列表。 `.nupkg` 文件是包含 Windows Admin Center 扩展的 NuGet 包。

若要访问现有扩展源，请打开 Windows Admin Center，并选择屏幕右上角的齿轮。 这会转到设置菜单。 可以在“扩展”菜单中的“网关”部分下找到扩展源 。 导航到“源”选项卡，然后选择“添加” 。 在此窗格中，将文件路径粘贴到 Azure Kubernetes 服务扩展的副本，然后选择 " **添加**"。 如果已成功添加文件路径，你会收到一条成功通知。 

现在我们已添加该源，Azure Kubernetes 服务扩展将在可用扩展列表中提供。 选择此扩展后，选择表顶部的“安装”以安装此扩展。 安装完成之后，Windows Admin Center 会重新加载。 

[ ![Windows Admin Center 扩展管理器中的可用扩展列表的视图。](.\media\setup\extension-manager.png) ](.\media\setup\extension-manager.png#lightbox)

## <a name="setting-up-an-azure-kubernetes-service-host"></a>设置 Azure Kubernetes 服务主机

创建 Kubernetes 群集之前，应完成最后一个步骤。 需要在要将 Kubernetes 群集部署到的系统上设置 Azure Kubernetes 服务主机。 这也称为 "设置平台服务或管理群集"。  

[![突出显示平台服务部分的体系结构关系图的图片。](.\media\setup\aks-hci-architecture-focused.png)](.\media\setup\aks-hci-architecture-focused.png) 

此系统可以是 Windows Server 2019 Datacenter 群集、单节点 Windows Server 2019 Datacenter 或2-4 节点 Azure Stack HCI 群集。 

> [!NOTE] 
> 不支持为了在 Kubernetes 群集创建过程中进行合并，而在两个独立系统上设置 Azure Kubernetes 服务主机。 

可以使用新 Azure Kubernetes 服务工具完成此设置。 

此工具将安装和下载所需的包，并创建提供 core Kubernetes 服务并协调应用程序工作负载的 AKS 主机群集。 

现在我们已验证了系统设置，接下来让我们开始使用吧： 
1. 选择“设置”以启动设置向导。
2. 查看正在运行 Windows 管理中心的计算机、所连接到的群集以及网络的先决条件。 此外，请确保已登录到 Windows Admin Center 上的 Azure 帐户，并且你计划使用的 Azure 订阅未过期。 完成后，选择“下一步”。

> [!WARNING]
> 如果使用2-4 节点 Azure Stack HCI 群集，请确保至少已配置一个外部虚拟交换机，然后再继续执行此步骤，否则将无法成功设置 Azure Kubernetes 服务主机。

3. 在向导的 " **系统检查** " 页上，执行所需的任何操作，例如将 [Windows 管理中心网关连接到 Azure](/windows-server/manage/windows-admin-center/azure/azure-integration)。 此步骤检查 Windows 管理中心和将托管 Azure Kubernetes 服务的系统是否具有适当的配置以继续。 操作执行完成后，选择“下一步”。
4. 在“主机配置”步骤中，配置将托管 Azure Kubernetes 服务的计算机。 建议在此部分中选择自动下载更新。 向导的此步骤要求配置以下详细信息：
    * **主机详细信息**，如 AKS 主机群集的名称，以及将存储 VM 映像的映像文件。 映像目录必须指向共享存储路径或主机可访问的 SMB 共享。
    * **VM 网络**，将应用于所有 Linux 和 Windows vm (节点) 为运行容器和安排容器管理创建。 这包括连接到 internet 的虚拟交换机、虚拟 LAN 标识启用、IP 地址分配方法和 Cloudagent IP 的字段。 Cloudagent IP 可用于向 CloudAgent 服务提供静态 IP 地址。 无论选择哪个 IP 地址，都适用这种情况。 如果选择了静态 IP 地址分配方法，则必须指定其他一些字段：
      - **子网前缀**，不与其他地址冲突的 IP 地址范围
      - **网关**，用于在计算机之外路由数据包的网关
      - **Dns 服务器**，网络中 dns 服务器的 IP 地址
      - **Kubernetes 节点 ip 池开始**，Kubernetes 群集使用的 IP 地址的池开始范围
      - **Kubernetes 节点 ip 池结束**，Kubernetes 群集使用的 IP 地址的池结束范围
    * **负载均衡器设置**，用于定义用于外部服务的地址池。 如果在 "VM 网络" 部分中选择了静态配置，则地址池的开始和结束必须在该部分中指定的子网范围内。 

    ![说明 Azure Kubernetes 服务主机向导的主机配置步骤。](.\media\setup\host-configuration.png)
    
    完成后，选择“下一步”。

5. 在向导的 " **Azure 注册** " 页上，提供有关要用于此服务的订阅和资源组的详细信息。 虽然 Azure Kubernetes 服务处于预览阶段，但不会向你收费。 完成后，选择“下一步”。
6. 查看 " **查看 + 创建** " 步骤中的所有选择。 如果你对选择满意，请选择 " **下一步** " 以开始安装主机。 
7. 在“设置进度”页上，可以查看主机设置的进度。 此时，欢迎你在新选项卡中打开 Windows Admin Center 并继续执行管理任务。 
8. 如果部署成功，请选择 " *完成*"，此时会显示一个管理仪表板，可在其中创建和管理 Kubernetes 群集。 此仪表板（如 Azure Stack HCI 上的 Azure Kubernetes 服务的其余部分）处于预览版本中，并将在将来的版本中使用其他功能进行更新。
 
  ![演示 Azure Stack HCI 管理仪表板上的 Azure Kubernetes 服务。](.\media\setup\dashboard.png)
 
## <a name="next-steps"></a>后续步骤

在本快速入门中，已安装 Windows 管理中心和 Azure Kubernetes 服务扩展。 还在要将 Kubernetes 群集部署到的系统上配置了 Azure Kubernetes 服务主机。

现在，你已准备好继续[在 Windows Admin Center 中创建 Kubernetes 群集](create-kubernetes-cluster.md)。
