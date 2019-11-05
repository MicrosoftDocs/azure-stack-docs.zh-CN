---
title: Azure Stack 上的 AKS 引擎是什么？ | Microsoft 文档
description: 了解如何使用 AKS 引擎命令行工具在 Azure 上部署和管理 Kubernetes 群集，并 Azure Stack。
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
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: 22779072b2dfed018a2ff6d5eac5bf2c294ccd31
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595123"
---
# <a name="what-is-the-aks-engine-on-azure-stack"></a>Azure Stack 上的 AKS 引擎是什么？

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 AKS 引擎命令行工具在 Azure 上部署和管理 Kubernetes 群集，并 Azure Stack。 使用 AKS 引擎创建、升级和缩放 Azure 资源管理器本地群集。 可以使用引擎在连接和断开连接的环境中部署群集。 本文概述了 AKS 引擎，并提供了有关将引擎与 Azure Stack 配合使用的支持方案，以及部署、升级和缩放等操作的简介。

> [!IMPORTANT]
> AKS 引擎目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="overview-of-the-aks-engine"></a>AKS 引擎概述

[AKS 引擎](https://github.com/Azure/aks-engine)提供了一个命令行工具，用于在 Azure 上启动 Kubernetes 群集并 Azure Stack。 通过使用 Azure 资源管理器，AKS 引擎可帮助你创建和维护在 Azure Stack 中的 Vm、虚拟网络和其他基础结构即服务（IaaS）资源上运行的群集。

## <a name="aks-engine-on-azure-stack-considerations"></a>Azure Stack 注意事项上的 AKS 引擎

在 Azure Stack 上使用 AKS 引擎之前，请务必了解 Azure Stack 与 Azure 之间的差异。 本部分列出了在将 Azure Stack 与 AKS 引擎结合使用来管理 Kubernetes 群集时的不同功能和关键注意事项。

有关 Azure Stack 上的 AKS 引擎的详细信息及其与 Azure 的区别，请参阅[Azure Stack 上的 AKS 引擎](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md)。

## <a name="supported-scenarios-with-the-aks-engine"></a>AKS 引擎支持的方案

Azure Stack 支持团队支持以下方案：

1.  AKS 引擎按照本文档中的指导原则部署所有群集项目，并使用[以下模板](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack)。
2.  AKS 引擎在现有 VNET 上部署群集。 有关详细信息，请参阅[将自定义虚拟网络与 AKS 引擎配合使用](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/custom-vnet.md)。
3.  [升级](azure-stack-kubernetes-aks-engine-upgrade.md)和[缩放](azure-stack-kubernetes-aks-engine-scale.md)操作。

有关 AKS 引擎和 Azure Stack 的详细信息，请参阅[Azure Stack 上的 AKS engine 的支持策略](azure-stack-kubernetes-aks-engine-support.md)。

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>安装 AKS 引擎并部署 Kubernetes 群集

在 Azure Stack 上使用 AKS 引擎部署 Kubernetes 群集：

1. [设置 AKS 引擎的先决条件](azure-stack-kubernetes-aks-engine-set-up.md)
2. 将 AKS 引擎安装到可访问 Azure Stack 环境的计算机。
     - [在 Azure Stack 的 Windows 上安装 AKS 引擎](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [在 Azure Stack 中的 Linux 上安装 AKS 引擎](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [在 Azure Stack 上使用 AKS 引擎部署 Kubernetes 群集](azure-stack-kubernetes-aks-engine-deploy-cluster.md)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [设置 AKS 引擎的先决条件](azure-stack-kubernetes-aks-engine-set-up.md)