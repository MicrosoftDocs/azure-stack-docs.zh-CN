---
title: 什么是 Azure Stack HCI 上的 Azure Kubernetes 服务？
description: Azure Stack HCI 上的 azure Kubernetes 服务是 Azure Kubernetes Service (AKS) 的本地实现，它可自动运行容器化应用程序。
ms.topic: overview
author: jasongerend
ms.author: jgerend
ms.date: 09/22/2020
ms.openlocfilehash: fad630d143fee31c4b63b4008c02bef697475155
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948862"
---
# <a name="what-is-azure-kubernetes-service-on-azure-stack-hci"></a>什么是 Azure Stack HCI 上的 Azure Kubernetes 服务？

Azure Stack HCI 上的 azure Kubernetes 服务是 Azure Kubernetes Service (AKS) 的本地实现，它可自动运行容器化应用程序。 Azure Kubernetes 服务现在 Azure Stack HCI 提供预览版，使你可以更快速地开始在你的数据中心内托管 Linux 和 Windows 容器。

若要开始在本地完成 Azure Kubernetes 服务，请 [注册预览版](https://aka.ms/AKS-HCI-Evaluate) (在预览) 期间没有额外的成本，请参阅 [在 Azure Stack HCI 上设置 Azure Kubernetes 服务](setup.md)。 若要改为使用 Azure Kubernetes 服务来协调基于云的容器，请参阅 azure [中的 Azure Kubernetes 服务](/azure/aks/intro-kubernetes)。

以下部分介绍了在 Azure Stack HCI 上使用 Azure Kubernetes 服务的一些原因，并回答了有关该服务的一些常见问题以及如何开始使用。 有关容器的背景，请参阅 [Windows 和容器](/virtualization/windowscontainers/about/)。

## <a name="automate-management-of-containerized-applications"></a>自动管理容器化应用程序

尽管可以使用 Docker 和 Windows 手动管理一些容器，但应用通常使用5、10甚至几百个容器，即 Kubernetes orchestrator 的位置。

Kubernetes 是一个开源协调器，用于大规模自动化容器管理。 Azure Kubernetes 服务通过提供用于在 Azure Stack HCI 上设置 Kubernetes 和必要加载项以及创建 Kubernetes 群集来托管工作负荷的向导，简化了本地 Kubernetes 部署。

下面是 Azure Kubernetes 服务提供的一些功能，在 Azure Stack HCI 上进行预览时：

- 大规模部署容器化应用 (称为 Kubernetes 群集的 Vm 群集) 跨 Azure Stack HCI 群集运行的虚拟机
- 当 Kubernetes 群集中的节点发生故障时进行故障转移
- 部署和管理基于 Linux 和 Windows 的容器化应用
- 计划工作负荷
- 监视运行状况
- 通过在 Kubernetes 群集中添加或删除节点，增加或减少
- 管理网络
- 发现服务
- 协调应用升级
- 通过群集节点关联将 pod 分配到群集节点

有关 Kubernetes 的详细信息，请参阅 [Kubernetes.io](https://kubernetes.io)。

## <a name="simplify-setting-up-kubernetes"></a>简化 Kubernetes 的设置

Azure Kubernetes 服务简化了在 Azure Stack HCI 上设置 Kubernetes 的过程，其中包括以下功能：

- 用于设置 Kubernetes 及其依赖项的 Windows 管理中心向导 (例如 kubeadm、kubelet、kubectl 和 Pod 网络外接程序) 
- 用于创建 Kubernetes 群集以运行容器化应用程序的 Windows 管理中心向导
- PowerShell cmdlet，用于设置 Kubernetes 和创建 Kubernetes 群集，以防为主机安装和 Kubernetes 群集创建编写脚本

## <a name="view-and-manage-kubernetes-using-on-premises-tools-or-azure-arc"></a>使用本地工具或 Azure Arc 查看和管理 Kubernetes

在 Azure Stack HCI 群集上设置 Azure Kubernetes 服务并创建 Kubernetes 群集后，我们提供了几种方式来管理和监视 Kubernetes 基础结构：

- **本地使用常用工具（如 Kubectl 和 Kubernetes 仪表板** ）-使用基于 web 的开源界面将应用程序部署到 Kubernetes 群集，管理群集资源，排除故障，以及查看正在运行的应用程序。
- **在使用 Azure Arc Azure 门户中** ，使用 azure 服务管理在云和本地环境中部署的 Azure Kubernetes 服务和 Kubernetes 群集。 可以使用 Azure Arc 来添加和删除 Kubernetes 群集，以及将节点添加到 Kubernetes 群集、更改网络设置以及安装加载项。
<br>利用 azure Arc，还可以通过其他 Azure 服务管理 Kubernetes 群集，其中包括：

  - Azure Monitor
  - Azure Policy
  - 基于角色的访问控制

## <a name="run-linux-and-windows-containers"></a>运行 Linux 和 Windows 容器

Azure Kubernetes 服务完全支持基于 Linux 和基于 Windows 的容器。 在 Azure Stack HCI 上创建 Kubernetes 群集时，可以选择是创建 (组相同 Vm 的组，) 运行 Linux 容器和/或 Windows 容器。 

Azure Kubernetes 服务创建 Linux 和 Windows Vm，因此你无需直接管理 Linux 或 Windows 操作系统。

## <a name="secure-your-container-infrastructure"></a>保护容器基础结构

Azure Kubernetes 服务提供了许多功能来帮助保护容器基础结构：

- **辅助角色节点的基于虚拟机监控程序的隔离** -每个 Kubernetes 群集都运行在其自己的专用和隔离的虚拟机集上，因此租户可以共享同一物理基础结构。
- **Microsoft 维护的辅助角色节点的 linux 和 windows 映像** -辅助角色节点运行 Microsoft 创建的 Linux 和 windows 虚拟机映像，以遵循安全最佳做法。 Microsoft 还会每月刷新一次这些映像，其中包含最新的安全更新。

安全是 Azure Stack HCI 上的 Azure Kubernetes 服务预览版的持续投资领域，因此请继续关注。

## <a name="where-can-i-run-azure-kubernetes-service"></a>在哪里可以运行 Azure Kubernetes 服务？

Azure Kubernetes 服务在以下平台上提供：

- 在 azure 云中通过 azure [中的 Azure Kubernetes 服务](/azure/aks/intro-kubernetes)
- Azure Stack HCI 上通过 Azure Kubernetes 服务的本地 (本文介绍的内容) 
- 使用 [Azure Stack 集线器上的 AKS 引擎](../user/azure-stack-kubernetes-aks-engine-overview.md)在 Azure Stack 中心环境中进行本地部署。

## <a name="how-does-kubernetes-work-on-azure-stack-hci"></a>Kubernetes 在 Azure Stack HCI 上如何工作？

当在 Azure Stack HCI 上运行时，azure Kubernetes 服务的工作方式稍有不同，而不是在 Azure 云中使用它：

- Azure 中的 Kubernetes 服务是一项托管服务，其中的许多 Kubernetes 管理基础结构 (控制面) 。 控制平面和容器化应用程序都在 Azure 虚拟机中运行。
- 通过 Azure Stack HCI 上的 Azure Kubernetes 服务，你可以直接在 Azure Stack HCI 群集上设置服务，使你能够控制控制平面，以便进行对话。 控制平面、容器化应用程序和 Azure Kubernetes 服务本身都在超聚合群集托管的虚拟机中运行。

在 Azure Stack HCI 群集上设置 Azure Kubernetes 服务后，其工作方式类似于托管的 Azure Kubernetes 服务：你可以使用该服务创建运行容器化应用程序的 Kubernetes 群集。 这些 Kubernetes 群集是作为工作节点运行应用程序容器的 Vm 组。 Kubernetes 群集还包含一个控制平面，其中包含用于协调应用程序容器的 Kubernetes 系统服务。

下面是几个简化的关系图，显示 Azure Kubernetes 服务的体系结构在 Azure 中运行时和 Azure Stack HCI 中的比较。

:::image type="content" source="media\overview\aks-azure-architecture.png" alt-text="Azure 中托管的 Azure Kubernetes 服务的体系结构，其中显示了平台服务和大部分控制面由 Azure 管理，而运行容器化应用程序的 Kubernetes 群集由客户管理。" lightbox="media\overview\aks-azure-architecture.png":::

:::image type="content" source="media\overview\aks-hci-architecture.png" alt-text="Azure Stack HCI 上的 Azure Kubernetes 服务的体系结构，其中显示了所有内容在 Azure Stack HCI 群集之上运行，包括 Azure Kubernetes Service 平台、控制平面和运行容器化应用程序的 Kubernetes 群集。" lightbox="media\overview\aks-hci-architecture.png":::

## <a name="what-you-need-to-get-started"></a>入门所需操作

以下部分概述了在 Azure Stack HCI 上运行 Azure Kubernetes 服务所需的内容。 有关完整的详细信息，请参阅在 [AZURE STACK HCI 上安装 Azure Kubernetes 服务之前](system-requirements.md)。

### <a name="on-your-windows-admin-center-system"></a>在 Windows 管理中心系统上

Windows 管理中心管理系统具有以下要求：

- Windows 10 (当前不支持 Windows 管理中心服务器) 
- 60 GB 可用空间
- 注册到 Azure
- 与 Azure Stack HCI 群集位于同一个域中

### <a name="on-the-azure-stack-hci-cluster-that-hosts-azure-kubernetes-service"></a>在托管 Azure Kubernetes 服务的 Azure Stack HCI 群集上

运行 Azure Stack HCI、版本20H2 或更高版本的群集具有以下要求：

- 此预览版的群集中最多有四个服务器
- 用于 Azure Kubernetes 服务的存储池中有 1 TB 的可用容量
- 至少 30 GB 的可用内存用于运行 Azure Kubernetes 服务 Vm
- 群集中的所有服务器都必须使用 EN-US 区域和此预览版本的语言选择

有关一般 Azure Stack HCI 要求，请参阅 [部署 AZURE STACK Hci 之前](../hci/deploy/before-you-start.md)。

### <a name="the-network-configuration-for-azure-stack-hci"></a>Azure Stack HCI 的网络配置

连接到 Azure Stack HCI 群集上的 Vm 的网络需要专用于 Azure Kubernetes 服务的 DHCP IPv4 地址范围，并可通过 Azure Stack HCI 群集上的 Vm 访问

在 Azure Stack HCI 上，不能对 Azure Kubernetes 服务使用网络上的 VLAN 标记。 使用访问 (网络交换机上的未标记) 端口 Azure Stack HCI 和 Azure Kubernetes 服务 Vm 使用的网络。

## <a name="next-steps"></a>后续步骤

若要开始在 Azure Stack HCI 上完成 Azure Kubernetes 服务，请参阅以下文章：

- [审查要求](system-requirements.md)
- [Azure Stack HCI 上设置 Azure Kubernetes 服务](create-kubernetes-cluster.md)
