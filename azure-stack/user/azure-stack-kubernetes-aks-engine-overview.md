---
title: Azure Stack Hub 上的 AKS 引擎是什么？
description: 了解如何使用 AKS 引擎命令行工具部署和管理 Azure 和 Azure Stack Hub 上的 Kubernetes 群集。
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: 1f29698d1bae0b38a41f19f3c9eb75f76bffc59e
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99246854"
---
# <a name="what-is-the-aks-engine-on-azure-stack-hub"></a>Azure Stack Hub 上的 AKS 引擎是什么？

可以使用 AKS 引擎命令行工具部署和管理 Azure 和 Azure Stack Hub 上的 Kubernetes 群集。 使用 AKS 引擎可以创建、升级和缩放 Azure 资源管理器本机群集。 可以使用该引擎在连接的和断开连接的环境中部署群集。 本文概述了 AKS 引擎、在 Azure Stack Hub 中使用该引擎时支持的方案，并介绍了部署、升级和缩放等操作。

## <a name="overview-of-the-aks-engine"></a>AKS 引擎概述

[AKS 引擎](https://github.com/Azure/aks-engine)提供了一个命令行工具来启动 Azure 和 Azure Stack Hub 上的 Kubernetes 群集。 通过使用 Azure 资源管理器，AKS 引擎可帮助你创建和维护在 Azure Stack Hub 的 VM、虚拟网络和其他基础结构即服务 (IaaS) 资源上运行的群集。

## <a name="aks-engine-on-azure-stack-hub-considerations"></a>Azure Stack Hub 上的 AKS 引擎注意事项

在使用 Azure Stack Hub 上的 AKS 引擎之前，必须了解 Azure Stack Hub 与 Azure 之间的差异。 本部分介绍将 Azure Stack Hub 与 AKS 引擎配合使用来管理 Kubernetes 群集时的不同功能和关键注意事项。

若要详细了解 Azure Stack Hub 上的 AKS 引擎的具体内容及其与 Azure 的区别，请参阅 [Azure Stack Hub 上的 AKS 引擎](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md)。

## <a name="supported-scenarios-with-the-aks-engine"></a>AKS 引擎支持的方案

Azure Stack Hub 支持团队为以下方案提供支持：

1.  AKS 引擎根据本文档中的准则使用[此模板](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack)部署所有群集项目。
2.  AKS 引擎在现有 VNET 上部署群集。 有关详细信息，请参阅 [Using a custom virtual network with AKS engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/custom-vnet.md)（通过 AKS 引擎使用自定义虚拟网络）。
3.  [升级](azure-stack-kubernetes-aks-engine-upgrade.md)和[缩放](azure-stack-kubernetes-aks-engine-scale.md)操作。

若要详细了解 AKS 引擎和 Azure Stack Hub，请参阅 [Azure Stack Hub 上 AKS 引擎的支持策略](azure-stack-kubernetes-aks-engine-support.md)。

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>安装 AKS 引擎并部署 Kubernetes 群集

若要使用 Azure Stack Hub 上的 AKS 引擎部署 Kubernetes 群集，请执行以下操作：

1. [设置 AKS 引擎的先决条件](azure-stack-kubernetes-aks-engine-set-up.md)
2. 将 AKS 引擎安装到可访问 Azure Stack Hub 环境的计算机。
     - [在 Windows 上的 Azure Stack Hub 中安装 AKS 引擎](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [在 Linux 上的 Azure Stack Hub 中安装 AKS 引擎](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [使用 Azure Stack Hub 上的 AKS 引擎部署 Kubernetes 群集](azure-stack-kubernetes-aks-engine-deploy-cluster.md)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [设置 AKS 引擎的先决条件](azure-stack-kubernetes-aks-engine-set-up.md)