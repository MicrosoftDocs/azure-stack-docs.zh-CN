---
title: Azure Stack 集线器上的 Azure Kubernetes Service （AKS）引擎的发行说明
description: 了解 Azure Stack 集线器上的 AKS engine 更新所需执行的步骤。
author: mattbriggs
ms.topic: article
ms.date: 4/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 4/23/2020
ms.openlocfilehash: 1e58943e4ff315d2b5c2a9370f623846e920443d
ms.sourcegitcommit: c51e7787e36c49d34ee86cabf9f823fb98b61026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82220722"
---
# <a name="release-notes-for-the-aks-engine-on-azure-stack-hub"></a>Azure Stack 集线器上的 AKS 引擎的发行说明

本文介绍 Azure Stack 中心更新上的 Azure Kubernetes Service （AKS）引擎的内容。 更新包括针对 Azure Stack 中心平台的最新版本 AKS 引擎的改进和修补程序。 请注意，这并不是为了记录适用于全球 Azure 的 AKS 引擎的版本信息。

## <a name="update-planning"></a>更新规划

AKS 引擎升级命令可完全自动执行群集的升级过程，它负责处理虚拟机（Vm）、网络、存储、Kubernetes 和业务流程任务。 在应用更新之前，请确保查看本文中的信息。

### <a name="upgrade-considerations"></a>升级注意事项

-   你是否正在使用正确的 marketplace 项 AKS 基本 Ubuntu 16.04-LTS Image 发行版 for the AKS engine？ 可在[下载新的 AKS 基本 Ubuntu 映像和 AKS 引擎版本](#download-new-image-and-aks-engine)部分中找到相应版本。

-   是否对目标群集使用正确的群集规范（apimodel）和资源组？ 最初部署群集时，此文件是在输出目录中生成的。 请参阅[部署 Kubernetes 群集](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-cluster#deploy-a-kubernetes-cluster)中的 "部署" 命令参数。

-   你是否正在使用可靠的计算机来运行 AKS 引擎以及要从其执行升级操作的计算机？

-   如果要使用活动工作负荷更新操作群集，则可以在不影响它们的情况下应用升级，假设群集处于正常负载。 但是，如果需要将用户重定向到该群集，则应具有备份群集。

-   如果可能，请从 Azure Stack 中心环境中的 VM 运行命令，以减少网络跃点和可能的连接故障。

-   确保你的订阅具有足够的配额来完成整个过程。 进程将在此过程中分配新的 Vm。 生成的 Vm 数与原始 Vm 数相同，但会为在此过程中创建的更多 Vm 作计划。

-   未计划系统更新或计划任务。

-   在配置了与生产群集的值相同的群集上设置过渡升级，并在生产群集中进行升级之前测试升级。

### <a name="use-the-upgrade-command"></a>使用升级命令

你将需要使用 AKS 引擎 "upgrade" 命令，如以下文章[升级 Azure Stack 集线器上的 Kubernetes 群集](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-upgrade)中所述。

### <a name="upgrade-interruptions"></a>升级中断

有时，意外的因素会中断群集的升级。 当 AKS 引擎报告错误或发生 AKS 引擎执行过程时，可能会发生中断。 检查中断的原因，解决该问题，然后再次提交相同的升级命令以继续升级过程。 **升级**命令是幂等的，应在重新提交命令后恢复群集的升级。 通常，中断会增加完成更新所需的时间，但不会影响它的完成时间。

### <a name="estimated-upgrade-time"></a>估计升级时间

群集中每个 VM 的估计时间为12到15分钟。 例如，若要升级，20个节点的群集可能需要大约5小时。

## <a name="download-new-image-and-aks-engine"></a>下载新图像和 AKS 引擎

下载 AKS 基本 Ubuntu 映像和 AKS 引擎的新版本。

如 Azure Stack 中心的 AKS 引擎文档中所述，部署 Kubernetes 群集需要两个主要组件： 
- Aks 二进制文件
- AKS 基本 Ubuntu 16.04-LTS Image 发行版

此更新提供了这些版本的新版本：

-   Azure Stack 中心操作员需要下载新的 AKS 基本 Ubuntu 映像：

    -   名称：`AKS Base Ubuntu 16.04-LTS Image Distro, March 2020`
    -   版本：`2020.03.19`
    -   按照以下文章中的说明，[将 Azure Kubernetes Services （AKS）引擎的先决条件添加到 Azure Stack 中心市场](https://docs.microsoft.com/azure-stack/operator/azure-stack-aks-engine)

-   Kubernetes 群集管理器将需要下载新的 aks 版本0.48.0。 请参阅以下文章中的说明，在[Azure Stack 集线器中的 Linux 上安装 AKS 引擎](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-linux)。 你可以遵循用于首次安装群集的相同过程。 更新将覆盖以前的二进制文件。 例如，如果使用`get-akse.sh`脚本，请按照在[连接环境中安装一文中](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-linux#install-in-a-connected-environment)所述的相同步骤进行操作。 如果你在 Windows 系统上安装，则这一过程同样适用，这篇文章在[Azure Stack Hub 中的 Windows 上安装 AKS 引擎](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-deploy-windows)。

## <a name="kubernetes-version-upgrade-path"></a>Kubernetes 版本升级路径

可以在下表中找到 Azure Stack 集线器的当前版本和升级版本。 不要跟随 aks `get-versions`命令，因为该命令还包括全局 Azure 中支持的版本。 以下版本和升级表适用于 Azure Stack 集线器中的 AKS 引擎群集。

| **当前版本** | **可用升级** |
| --------------------| ----------------------|
| 1.14.7 | 1.15.10 |
| 1.14.8 | 1.15.10 |
| 1.15.4 | 1.15.10 |
| 1.15.5 | 1.15.10 |

## <a name="whats-new"></a>新增功能 

-   支持 Kubernetes 版本1.15.10 （[\#2834](https://github.com/Azure/aks-engine/issues/2834)）。 部署新群集时，请确保在 api 模型 json 文件中指定（a.k.s。 群集定义文件）发布版本号和次版本号。 你可以找到一个示例： [kubernetes-test-azurestack](https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json)：

    - `"orchestratorRelease": "1.15`,

    - `"orchestratorVersion": "1.15.10"`

    > [!Note]  
    > 如果 API 模型 json 文件中未显式提供 Kubernetes 版本，则将使用版本`1.15` （[\#2932](https://github.com/Azure/aks-engine/issues/2932)），并且 orchestratorVersion 将默认为` 1.15.11`，这将导致在群集部署过程中出现错误。

-   使用 aks v 0.43.1 时，云提供程序执行其控制循环和其他任务的默认频率设置不适用于 Azure Stack 集线器的传入请求资源管理器阈值限制。 此更新将更改 Azure Stack 集线器的默认值，以将重试负载减少到 Azure Stack 中心资源管理器（[\#2861](https://github.com/Azure/aks-engine/issues/2861)）。

-   如果 api 模型 json 文件包含 Azure Stack Hub 不支持的属性（[\#2717](https://github.com/Azure/aks-engine/issues/2717)），则 aks 中的新验证步骤将导致执行停止或显示警告。

-   使用新的验证签入，aks 将验证执行的 aks （[\#2342](https://github.com/Azure/aks-engine/issues/2342)）版本所需的 aks 基础映像版本的可用性。 这会在分析 api 模型文件之后、调用 Azure Stack 中心资源管理器之前发生。

-   "升级" 命令中的 New aks 选项 "--仅限控制平面" 允许用户升级操作，使其仅面向主虚拟机（[\#2635](https://github.com/Azure/aks-engine/issues/2635)）。

-   对 Linux 核心版本 4.15.0-1071 的更新-azure for Ubuntu 16.04-LTS。 有关详细信息，请参阅 "[包： 4.15.0-1071-azure （4.15.0 \[）安全性\]](https://packages.ubuntu.com/xenial/linux-image-4.15.0-1071-azure)"。

-   新的 hyperkube 更新，支持 Kubernetes 版本1.14.8 和1.15.10。

-   更新 kubectl 以匹配群集的 Kubernetes 版本。 此组件在 Kubernetes 群集主节点中提供，可以通过 SSH 将它运行到主节点。

-   最新[2020 版](https://github.com/microsoft/Docker-Provider/blob/ci_feature_prod/README.md)（[\#2850](https://github.com/Azure/aks-engine/issues/2850)）的 Azure 容器监视器外接程序的更新。

-   升级`coredns`到版本 v 1.6.6 （[\#2555](https://github.com/Azure/aks-engine/issues/2555)）。

-   升级`etcd`到版本3.3.18 （[\#2462](https://github.com/Azure/aks-engine/issues/2462)）。

-   升级`moby`到版本3.0.11 （[\#2887](https://github.com/Azure/aks-engine/issues/2887)）。

-   使用此版本时，AKS 引擎会`k8s.gcr.io`从中剪切依赖项`Kubernetes MCR registry @ mcr.microsoft.com` ，以便在生成映像时使用官方（[\#2722](https://github.com/Azure/aks-engine/issues/2722)）。

## <a name="known-issues"></a>已知问题

-  在单个群集内并行部署多个 Kubernetes 服务可能会导致基本负载均衡器配置错误。 尽可能部署一个服务。

-  运行 aks 将生成适用于 Azure 和 Azure Stack 集线器的信息，然而，没有明确的方法来辨别与 Azure Stack 中心对应的内容。 不要使用此命令确定可升级的版本区域。 使用上述升级参考表。

-  由于 aks 工具是 Azure 和 Azure Stack 集线器上的共享源代码存储库。 检查很多发行说明和拉取请求将会导致你认为该工具支持其他版本的 Kubernetes 和操作系统平台，超过以上列出的版本，请将其忽略，并使用上面的版本表作为此更新的官方指南。

## <a name="reference"></a>参考

下面列出了一些修复的 bug 以及从0.44.0 到0.48.0 的完整发行说明集，请注意后一列表将包括 Azure 和 Azure Stack 集线器项

### <a name="bug-fixes"></a>Bug 修复

-   `userAssignedIdentityId`在 windows `azure.json`中缺少引号（[\#2327](https://github.com/Azure/aks-engine/issues/2327)）

-   加载项`update config`仅限升级（[\#2282](https://github.com/Azure/aks-engine/issues/2282)）

-   在 windows 节点上获取管理 IP 的碰撞超时（[\#2284](https://github.com/Azure/aks-engine/issues/2284)）

-   将 1.0.28 Azure CNI zip 文件添加到 windows VHD （[\#2268](https://github.com/Azure/aks-engine/issues/2268)）

-   设置 IPAddressCount 的正确默认顺序（[\#2358](https://github.com/Azure/aks-engine/issues/2358)）

-   更新以对所有 k8s 版本使用单个 omsagent yaml，以避免任何手动错误和易维护性（[\#2692](https://github.com/Azure/aks-engine/issues/2692)）

### <a name="release-notes"></a>发行说明

这是适用于 Azure 和 Azure Stack 中心组合的完整发行说明集

-   https://github.com/Azure/aks-engine/releases/tag/v0.44.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.44.1
-   https://github.com/Azure/aks-engine/releases/tag/v0.44.2
-   https://github.com/Azure/aks-engine/releases/tag/v0.45.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.1
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.2
-   https://github.com/Azure/aks-engine/releases/tag/v0.46.3
-   https://github.com/Azure/aks-engine/releases/tag/v0.47.0
-   https://github.com/Azure/aks-engine/releases/tag/v0.48.0

## <a name="next-steps"></a>后续步骤

- 了解[Azure Stack 集线器上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)
