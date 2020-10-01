---
title: 使用 Windows 管理中心创建 Kubernetes 群集的快速入门
description: 了解如何使用 Windows 管理中心创建 Kubernetes 群集
author: davannaw-msft
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: dawhite
ms.openlocfilehash: 7ef2f0e0532ee342e8821b362b16433f755bc072
ms.sourcegitcommit: a1e2003fb9c6dacdc76f97614ff5a26a5b197b49
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91623262"
---
# <a name="quickstart-create-a-kubernetes-cluster-on-azure-stack-hci-using-windows-admin-center"></a>快速入门：使用 Windows 管理中心在 Azure Stack HCI 上创建 Kubernetes 群集

> 适用于： Azure Stack HCI

设置 Azure Kubernetes 服务主机后，可以使用 Windows 管理中心创建 Kubernetes 群集。 若要改为使用 PowerShell，请参阅 [使用 Powershell 创建 Kubernetes 群集](create-kubernetes-cluster-powershell.md)。

让我们开始吧：

1. [设置 Azure Kubernetes 服务](setup.md) 并检查 [系统要求](system-requirements.md) （如果尚未这样做）。
1. 若要开始在 Windows 管理中心中创建 Kubernetes 群集，请在网关屏幕上按 " **添加** " 按钮。
2. 在 " **添加或创建资源** " 面板的 " **Kubernetes 群集 (预览") **下，选择 " **新建** " 以启动 Kubernetes 群集向导。 尽管 Kubernetes 群集下的 "添加" 按钮位于公共预览版中，但它不能正常工作。 在 Kubernetes 群集创建过程中的任何时候，都可以退出向导，但请注意，不会保存进度。 

    ![说明 Windows 管理中心中的 "添加或创建资源" 边栏选项卡，现在包括 Kubernetes 群集的新磁贴。](.\media\create-kubernetes-cluster\add-connection.png)

3. 查看将托管 Kubernetes 群集的系统的先决条件，以及适用于 Windows 管理中心的系统的先决条件。 完成后，选择“下一步”。 
4. 在 " **基本** 信息" 页上，配置有关群集的信息，例如 azure Arc 集成、Azure Kubernetes 服务主机信息以及主节点池大小。  Azure Kubernetes 服务主机信息和主节点池大小。 "Azure Kubernetes 服务主机" 字段需要你要将 Kubernetes 群集部署到的 Azure Stack HCI 群集的完全限定的域名。 必须通过 Azure Kubernetes 服务工具完成此系统的主机设置。 在公共预览版中，节点计数是不可编辑的，默认值为2，但可以为更大的工作负荷配置节点大小。 完成后，选择 " **下一步**"。

    ![说明了 Kubernetes 群集向导的 "基本信息" 页。](.\media\create-kubernetes-cluster\basics.png)

5. 你可以配置其他节点池，以便在 " **节点池** " 页上运行工作负荷。 公共预览期间，除了系统节点池) 之外，还可以添加多达一个 Windows 节点池和一个 Linux 节点池 (。 如果你想要稍后在此向导中启用 Azure Arc 集成，你将需要至少一个不是主节点池的 Linux 节点池。 完成后，选择“下一步”****。
6. 在 " **网络** " 页上指定网络配置。 如果选择 "高级"，则可以自定义在为群集中的节点创建虚拟网络时使用的地址范围。 如果选择 "基本"，则将使用默认地址范围创建群集中节点的虚拟网络。 无法在公共预览版中更改 (负载平衡器、网络策略和 HTTP 应用程序路由) 的网络设置。 完成后，选择 " **下一步**"。

    ![说明 Kubernetes 群集向导的 "网络" 页。](.\media\create-kubernetes-cluster\networking.png)

7. 在 " **集成** " 页上，将群集与其他服务（如 Kubernetes 仪表板、永久存储和 Azure Monitor）连接起来。 永久存储是在此页上配置所需的唯一服务。 在公共预览版中，永久存储位置将默认为在主机安装过程中选择的存储位置。 此字段当前不可编辑。 完成后，选择“下一步”****。
8. 查看 " **查看 + 创建** " 页上的选择。 如果感到满意，请选择 " **创建** " 以开始部署。 你的部署进度将显示在此页的顶部。 
9. 部署完成后，" **后续步骤** " 页详细介绍了如何管理群集。 此页面还包含 SSH 密钥和 Kubernetes 仪表板令牌。 如果在上一步中选择禁用 Kubernetes 仪表板或 Azure Arc 集成，则此页上的某些信息和说明可能不可用或无法正常使用。

> [!IMPORTANT] 
> 建议将 SSH 密钥和 Kubernetes 仪表板令牌保存在安全的、可访问的位置。

在本快速入门中，你将设置 Azure Kubernetes 服务主机并部署 Kubernetes 群集。 

若要详细了解 Azure Stack HCI 上的 Azure Kubernetes 服务，并演练如何 Azure Stack HCI 上的 Azure Kubernetes Service 上部署和管理 Linux 应用程序，请继续学习本教程。
