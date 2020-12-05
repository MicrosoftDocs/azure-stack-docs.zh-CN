---
title: 使用 Windows Admin Center 创建 Kubernetes 群集的快速入门
description: 了解如何使用 Windows Admin Center 创建 Kubernetes 群集
author: davannaw-msft
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: dawhite
ms.openlocfilehash: 7a9c1bf5d89348175b7ac25a149fe01f384964a2
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612397"
---
# <a name="quickstart-create-a-kubernetes-cluster-on-azure-stack-hci-using-windows-admin-center"></a>快速入门：使用 Windows Admin Center 在 Azure Stack HCI 上创建 Kubernetes 群集

> 适用于： Azure Stack HCI、Windows Server 2019 Datacenter

设置 Azure Kubernetes 服务主机之后，可以使用 Windows Admin Center 创建 Kubernetes 群集。 若要改为使用 PowerShell，请参阅[使用 PowerShell 创建 Kubernetes 群集](create-kubernetes-cluster-powershell.md)。

在继续执行 Kubernetes 群集创建向导之前，请确保已 [设置 Azure Kubernetes 服务](setup.md) 并检查 [系统要求](system-requirements.md) （如果尚未安装）。 可以通过 " [所有连接](#creating-a-kubernetes-cluster-from-the-all-connections-page) " 页或 [Azure Kubernetes 服务主机仪表板](#creating-a-kubernetes-cluster-from-the-azure-kubernetes-service-host-dashboard)访问 Kubernetes 群集创建向导。

## <a name="creating-a-kubernetes-cluster-from-the-all-connections-page"></a>从 "所有连接" 页创建 Kubernetes 群集 

可以通过两种方法在 Windows 管理中心中创建 Kubernetes 群集。 其中一种方法是在 " **所有连接** " 页中执行此操作。 请按照以下步骤操作，然后转到 [Kubernetes 群集创建向导](#the-kubernetes-cluster-create-wizard) 部分： 

1. 若要开始在 Windows Admin Center 中创建 Kubernetes 群集，请按网关屏幕上的“添加”按钮。 
2. 在“添加或创建资源”面板中的“Kubernetes 群集(预览)”下，选择“新建”以启动 Kubernetes 群集向导  。 尽管 Kubernetes 群集下的 "添加" 按钮位于公共预览版中，但它不能正常工作。 在 Kubernetes 群集创建过程中的任何时候，你都可以退出向导，但请注意，不会保存你的进度。 


    ![说明 Windows 管理中心中的 "添加或创建资源" 边栏选项卡，现在包括 Kubernetes 群集的新磁贴。](.\media\create-kubernetes-cluster\add-connection.png)
  
## <a name="creating-a-kubernetes-cluster-from-the-azure-kubernetes-service-host-dashboard"></a>从 Azure Kubernetes Service 主机仪表板创建 Kubernetes 群集  

还可以通过 Azure Kubernetes Service 主机仪表板创建 Kubernetes 群集。 当你连接到使用 Azure Kubernetes 服务主机的系统时，可在 Azure Kubernetes 服务工具中找到此仪表板。 请按照以下步骤操作，然后转到 [Kubernetes 群集创建向导](#the-kubernetes-cluster-create-wizard) 部分： 

1. 连接到要在其中创建 Kubernetes 群集的系统，并导航到 **Azure Kubernetes 服务** 工具。 此系统应已设置 Azure Kubernetes 服务主机。
2. 在 **Kubernetes 群集** 标题下选择 "**添加群集**" 按钮。

![说明在设置 Azure Kubernetes 服务主机后显示的 Azure Kubernetes 服务工具仪表板。](.\media\setup\dashboard.png)
  
## <a name="the-kubernetes-cluster-create-wizard"></a>Kubernetes 群集创建向导
已通过 " **所有连接** " 页或 Azure Kubernetes 服务工具访问了 Kubernetes 群集创建向导。 让我们开始吧：  

1. 查看将托管 Kubernetes 群集的系统的先决条件，以及 Windows Admin Center 的先决条件。 完成后，选择“下一步”。 
2. 在 " **基本** 信息" 页上，配置有关群集的信息，例如 azure Arc 集成、Azure Kubernetes 服务主机信息以及主节点池大小。 "Azure Kubernetes 服务主机" 字段需要在浏览 [安装程序](setup.md) 页面时使用的 Azure Stack HCI 或 Windows Server 2019 Datacenter 群集的完全限定的域名。 必须通过 Azure Kubernetes 服务工具为此系统完成主机设置。 在公共预览版中，节点计数不可编辑，默认值为2，但可以为更大的工作负荷配置节点大小。 完成后，选择“下一步”。

 [![说明了 Kubernetes 群集向导的 "基本信息" 页。 ](.\media\create-kubernetes-cluster\basics.png)](.\media\create-kubernetes-cluster\basics.png#lightbox)
 
3. 可以在“节点池”页上配置其他节点池来运行工作负载。 在公共预览版期间，可以最多添加一个 Windows 节点池和一个 Linux 节点池（除了系统节点池之外）。 如果你想要稍后在此向导中启用 Azure Arc 集成，你将需要至少一个不是主节点池的 Linux 节点池。 完成后，选择“下一步”。
4. 在“网络”页上指定网络配置。 如果选择“高级”，则可以自定义在为群集中的节点创建虚拟网络时使用的地址范围。 如果选择 "基本"，则将使用默认地址范围创建群集中节点的虚拟网络。 无法在公共预览版中更改网络设置（负载均衡器、网络策略和 HTTP 应用程序路由）。 完成后，选择“下一步”。

    [![说明 Kubernetes 群集向导的 "网络" 页。 ](.\media\create-kubernetes-cluster\networking.png)](\media\create-kubernetes-cluster\networking.png#lightbox)

5. 在 " **集成** " 页上，将群集与其他服务（如永久存储）进行连接。 需要在此页上配置永久存储。 在公共预览版中，永久存储位置将默认为主机安装过程中选择的存储位置。 此字段当前不可编辑。 完成后，选择“下一步”。
6. 在“审阅 + 创建”页上审阅进行的选择。 如果感到满意，请选择“创建”以开始部署。 部署进度会显示在此页的顶部。 
7. 部署完成之后，“后续步骤”页会详细说明如何管理群集。 此页面还包含 SSH 密钥。 如果在上一步中选择了禁用 Azure Arc 集成，则此页上的某些信息和说明可能不可用或无法正常使用。

> [!IMPORTANT] 
> 建议将 SSH 密钥保存在安全的、可访问的位置。

## <a name="next-steps"></a>后续步骤

本快速入门介绍了如何部署 Kubernetes 群集。 若要详细了解 Azure Stack HCI 上的 Azure Kubernetes 服务并演练如何在 Azure Stack HCI 上的 AKS 上部署和管理 Linux 应用程序，请继续学习以下教程：

- [教程：在 Azure Stack HCI 上的 Azure Kubernetes 服务中部署 Linux 应用程序](deploy-linux-application.md)
