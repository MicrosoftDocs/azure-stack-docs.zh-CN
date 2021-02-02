---
title: Azure Stack Hub 上 AKS 引擎的支持策略
description: 本主题包含 Azure Stack Hub 上 AKS 引擎的支持策略。
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/0102020
ms.openlocfilehash: 0322d72f3cd28a8e499b2a900d57e4e9d2e9bce4
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99246718"
---
# <a name="support-policies-for-aks-engine-on-azure-stack-hub"></a>Azure Stack Hub 上 AKS 引擎的支持策略

本文提供有关 Azure Stack Hub 上 AKS 引擎的支持策略和限制的详细信息。 本文还详细介绍了 Kubernetes 市场项、第三方开源组件以及安全性或修补程序管理。 

## <a name="self-managed-kubernetes-clusters-on-azure-stack-hub-with-aks-engine"></a>具有 AKS 引擎的 Azure Stack Hub 上的自托管 Kubernetes 群集

基础结构即服务 (IaaS) 云组件（例如计算或网络组件）可让用户访问低级别的控件机制和自定义选项。 AKS 引擎允许用户以透明方式利用这些 IaaS 组件放置 Kubernetes 群集，因此用户可以访问和影响部署的所有方面。

创建群集时，客户需定义 AKS 引擎创建的 Kubernetes 主节点和工作器节点。 客户工作负荷将在这些节点上执行。 客户拥有并可以查看或修改主节点和工作器节点。 不小心修改的节点可能会导致数据和工作负荷丢失，并导致群集无法正常工作。 此外，AKS 引擎操作（如升级或缩放）将覆盖任何超出有效范围的更改。 例如，如果群集有静态 Pod，在执行了 AKS 引擎升级操作后不会保留这些 Pod。

由于客户群集节点执行私有代码并存储敏感数据，因此 Microsoft 支持部门只能以受限的方式访问这些节点。 没有直接的客户许可或帮助，Microsoft 支持人员无法登录这些节点、无法在其中执行命令，也无法查看其日志。

## <a name="version-support"></a>版本支持

AKS 引擎版本支持遵循其余 Azure Stack Hub 支持策略所建立的模式，即 Azure Stack Hub 上对 AKS 引擎版本的支持基于 n-2 公式。 例如，如果最新的 AKS 引擎版本为 v0.55.0，则支持的版本集为：0.48.0、0.51.0、0.55.0。 同样重要的是要遵循 Azure Stack Hub 更新版本和相应的对 AKS 引擎支持版本的映射（保留在 [AKS 引擎发行说明](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping)中）。

## <a name="aks-engine-supported-areas"></a>AKS 引擎支持的区域

Microsoft 为以下各项提供技术支持：

-  AKS 引擎命令部署、生成、升级和缩放的相关问题。 工具应与其在 Azure 上的行为一致。
-  在 [AKS 引擎概述](azure-stack-kubernetes-aks-engine-overview.md)之后部署的 Kubernetes 群集的相关问题。
-  与其他 Azure Stack Hub 服务之间的连接问题。 
-  Kubernetes API 连接问题。
-  Azure Stack Hub Kubernetes 提供程序功能问题和与 Azure 资源管理器的连接问题。
-  AKS 引擎生成的 Azure Stack Hub 本机工件（如负载均衡器、网络安全组、VNET、子网、网络接口、路由表、可用性集、公共 IP 地址、存储帐户和 VM 计算机）配置的相关问题。 
-  网络性能和延迟问题。
-  已断开连接的部署中的 AKS 引擎所使用的 AKS 基本映像的相关问题。 

## <a name="aks-engine-areas-not-supported"></a>不支持 AKS 引擎区域

Microsoft 不为以下各项提供技术支持：

-  在 Azure 上使用 AKS 引擎。
-  Azure Stack Hub Kubernetes 市场项。
-  使用以下 AKS 引擎群集定义选项和加载项。
    -  不支持的加载项：  
            - Azure AD Pod Identity  
            - ACI 连接器  
            - Blobfuse Flex Volume  
            - 群集自动缩放程序  
            - 容器监视  
            - KeyVault Flex Volume  
            - NVIDIA 设备插件  
            - Rescheduler  
            - SMB Flex Volume  
        
    -  不支持的群集定义选项：  
            - KubernetesConfig 下：  
                    - cloudControllerManagerConfig  
                    - enableDataEncryptionAtRest  
                    - enableEncryptionWithExternalKms  
                    - enablePodSecurityPolicy  
                    - etcdEncryptionKey  
                    - useInstanceMetadata  
                    - useManagedIdentity  
                    - azureCNIURLLinux  
                    - azureCNIURLWindows  
            - masterProfile 下：  
                    - availabilityZones  
            - agentPoolProfiles 下：  
                    - availabilityZones  
                    - singlePlacementGroup  
                    - scaleSetPriority  
                    - scaleSetEvictionPolicy  
                    - acceleratedNetworkingEnabled  
                    - acceleratedNetworkingEnabledWindows

-  Kubernetes 配置更改保存在 Kubernetes 配置存储 etcd 以外的位置。 例如，在群集节点中运行的静态 Pod。
-  有关 Kubernetes 用法的问题。 例如，对于如何创建自定义入口控制器、如何使用应用程序工作负荷，或者如何应用第三方的或开源的软件包或工具，Microsoft 支持部门不提供建议。
-  不是作为 AKS 引擎部署的 Kubernetes 群集的一部分提供的第三方开源项目。 这些项目可能包括 Kubeadm、Kubespray、Native、Istio、Helm、Envoy 等。
-  在[支持使用 AKS 引擎的方案](azure-stack-kubernetes-aks-engine-overview.md#supported-scenarios-with-the-aks-engine)中指定的用例方案之外的其他用例中使用 AKS 引擎。
-  第三方软件。 此类软件可能包括安全扫描工具以及网络设备或软件。
-  有关多云或多供应商扩建的问题。 例如，Microsoft 不为与运行联合多公有云供应商解决方案相关的问题提供支持。
-  [AKS 引擎支持的区域](#aks-engine-supported-areas)部分中未列出的网络自定义。
-  生产环境应仅使用高度可用的 Kubernetes 群集，即使用至少三个主节点和三个代理节点部署的群集。 生产部署中不支持少于这个数的群集。

##  <a name="security-issues-and-patching"></a>安全问题和修补

如果在 Azure Stack 中心的一个或多个 AKS 引擎或 Kubernetes 提供程序组件中发现安全漏洞，Microsoft 将为客户提供修补受影响的群集的修补程序，以缓解此问题。 或者，AKS 团队将为用户提供升级指导。 请注意，可能需要让群集停机才能使用修补程序。 需要重新启动时，Microsoft 会通知客户此要求。 如果用户未按照 Microsoft 指导应用修补程序，则其群集将继续容易受到安全问题的影响。

## <a name="kubernetes-marketplace-item"></a>Kubernetes 市场项

用户可以下载 Kubernetes 市场项，它使用户能够通过 Azure Stack Hub 用户门户中的模板间接使用 AKS 引擎来部署 Kubernetes 群集。 这比直接使用 AKS 引擎部署更简单。 Kubernetes 市场项是一个有用的工具，可用于快速设置用于演示、测试和开发的群集。 它不用于生产，因此不包含在 Microsoft 支持的一组项目中。

## <a name="preview-features"></a>预览功能

对于需要扩展测试和用户反馈的功能，Microsoft 发布了功能标志后的新预览版功能或功能。 请将这些功能视为预发行版或 beta 功能。 预览功能或功能标志功能不适用于生产环境。 正在进行的功能更改和行为、bug 修复及其他更改可能导致群集不稳定和停机。 Microsoft 不支持这些功能。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Stack Hub 上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)