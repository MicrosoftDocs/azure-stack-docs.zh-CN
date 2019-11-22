---
title: Azure Stack 上的 AKS 引擎的支持策略 |Microsoft Docs
description: 本主题包含 Azure Stack 上的 AKS 引擎的支持策略。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 1aa8aa65f0b19efcbafc85887e644c1c2b773bde
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310172"
---
# <a name="support-policies-for-aks-engine-on-azure-stack"></a>Azure Stack 上的 AKS 引擎的支持策略

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本文提供有关 Azure Stack 上的 AKS 引擎的技术支持策略和限制的详细信息。 本文还详细介绍了 Kubernetes Marketplace 项、第三方开源组件以及安全性或修补程序管理。 

## <a name="self-managed-kubernetes-clusters-on-azure-stack-with-aks-engine"></a>具有 AKS 引擎 Azure Stack 上的自托管 Kubernetes 群集

基础结构即服务（IaaS）云组件，如计算或网络组件，使用户能够访问低级别的控件和自定义选项。 AKS 引擎允许用户以透明方式 laydown Kubernetes 群集利用这些 IaaS 组件，用户可以访问和影响部署的所有方面。

创建群集时，客户定义 AKS engine 创建的 Kubernetes 主机和辅助角色节点。 客户工作负荷将在这些节点上执行。 客户拥有，可以查看或修改主节点和工作节点。 漫不经心修改的节点可能会导致数据和工作负荷丢失，并可能导致群集无法正常运行。 此外，AKS 引擎操作（如升级或缩放）将覆盖任何超出界限的更改。 例如，如果群集有静态盒，则 AKS 引擎升级操作后不会保留这些。

由于客户群集节点执行专用代码并存储敏感数据，因此 Microsoft 支持部门只能以有限的方式访问这些数据。 Microsoft 支持部门不能登录到，不能在中执行命令，也不能查看这些节点的日志，无需快速获得客户权限或协助。

## <a name="aks-engine-supported-areas"></a>AKS 引擎支持的区域

Microsoft 为以下各项提供技术支持：

-  AKS 引擎命令的问题：部署、生成、升级和缩放。 该工具应与其在 Azure 上的行为一致。
-  遵循[AKS 引擎概述](azure-stack-kubernetes-aks-engine-overview.md)部署的 Kubernetes 群集问题。
-  与其他 Azure Stack 服务的连接问题 
-  Kubernetes API 连接问题
-  与 Azure 资源管理器 Azure Stack Kubernetes 提供程序功能和连接问题
-  AKS 引擎生成的 Azure Stack 本机项目配置的问题（例如负载均衡器、网络安全组、Vnet、子网、网络接口、路由表、可用性集、公共 IP 地址、存储帐户和 VM 计算机） 
-  网络性能和延迟问题
-  断开连接的部署中的 AKS 引擎所使用的 AKS 基本映像出现问题。 

## <a name="aks-engine-areas-not-supported"></a>不支持 AKS 引擎区域

Microsoft 不为以下各项提供技术支持：

-  使用 Azure 上的 AKS 引擎。
-  Azure Stack Kubernetes Marketplace 项。
-  使用以下 AKS 引擎群集定义选项和加载项。
    -  不支持的加载项：  
            -AAD Pod 标识  
            -ACI 连接器  
            -Blobfuse Flex Volume  
            -群集自动缩放程序  
            -容器监视  
            -KeyVault Flex Volume  
            -NVIDIA 设备插件  
            - Rescheduler  
            -SMB 弹性卷  
        
    -  不支持的群集定义选项：  
            -在 KubernetesConfig 下：  
                    - cloudControllerManagerConfig  
                    - enableDataEncryptionAtRest  
                    - enableEncryptionWithExternalKms  
                    - enablePodSecurityPolicy  
                    - etcdEncryptionKey  
                    - useInstanceMetadata  
                    - useManagedIdentity  
                    - azureCNIURLLinux  
                    - azureCNIURLWindows  
            -在发现 masterprofile 下：  
                    - availabilityZones  
            -在 agentPoolProfiles 下：  
                    - availabilityZones  
                    -singlePlacementGroup  
                    - scaleSetPriority  
                    - scaleSetEvictionPolicy  
                    - acceleratedNetworkingEnabled  
                    - acceleratedNetworkingEnabledWindows

-  Kubernetes 配置更改在 Kubernetes 配置存储 etcd 外保存。 例如，在群集节点中运行的静态 pod。
-  有关 Kubernetes 用法的问题。 例如，Microsoft 支持部门不会提供有关如何创建自定义入口控制器、使用应用程序工作负载或应用第三方或开源软件包或工具的建议。
-  不是由 AKS 引擎部署的 Kubernetes 群集的一部分提供的第三方开源项目。 这些项目可能包括 Kubeadm、Kubespray、Native、Istio、Helm、Envoy 或其他项目。
-  在用例方案中使用[AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md#supported-scenarios-with-the-aks-engine)所指定的方案以外的 AKS 引擎。
-  第三方软件。 此类软件可能包括安全扫描工具以及网络设备或软件。
-  有关多云或多供应商扩建的问题。 例如，Microsoft 不支持运行联合 multipublic 云供应商解决方案相关的问题。
-  除 " [AKS 引擎支持区域](#aks-engine-supported-areas)" 部分中列出的其他网络自定义。

##  <a name="security-issues-and-patching"></a>安全问题和修补

如果在 Azure Stack 的一个或多个组件或 Kubernetes 提供程序的一个或多个组件中发现了安全漏洞，Microsoft 将为客户提供修补受影响的群集的修补程序，以解决此问题。 或者，AKS 团队将为用户提供升级指导。 请注意，修补程序可能需要群集的停机时间。 需要重新启动时，Microsoft 会通知客户此要求。 如果用户未按照 Microsoft 指导应用修补程序，则其群集将继续容易受到安全问题的影响。

## <a name="kubernetes-marketplace-item"></a>Kubernetes marketplace 项

用户可以下载 Kubernetes Marketplace 项，这允许用户通过 Azure Stack 用户门户中的模板间接使用 AKS 引擎部署 Kubernetes 群集，这使得它比直接使用 AKS 引擎更简单。 这是一个有用的工具，可用于快速设置用于演示、测试和开发的群集。 它不用于生产，因为它不包含在 Microsoft 支持的一组项目中。

## <a name="preview-features"></a>预览功能

对于需要扩展测试和用户反馈的功能，Microsoft 发布了功能标志后的新预览版功能或功能。 请将这些功能视为预发行版或 beta 功能。 预览功能或功能标志功能不适用于生产环境。 正在进行的功能更改和行为、bug 修复和其他更改可能会导致不稳定的群集和停机时间。 Microsoft 不支持这些功能。

## <a name="next-steps"></a>后续步骤

- 了解[Azure Stack 上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)