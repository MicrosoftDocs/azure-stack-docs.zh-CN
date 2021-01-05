---
title: 将 Marketplace 项群集移到 Azure Stack 集线器上的 AKS 引擎
description: 了解如何在 Azure Stack Hub 上将市场项群集移动到 AKS 引擎。
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: 112862f5e73806d2e73d809b70e4bd7fd2e315b5
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874177"
---
# <a name="move-your-marketplace-item-cluster-to-the-aks-engine-on-azure-stack-hub"></a>在 Azure Stack Hub 上将市场项群集移动到 AKS 引擎

Kubernetes Azure Stack Hub 市场项使用 Azure 资源管理器模板来部署一个部署虚拟机 (VM)，以下载和安装 AKS 引擎，并生成用于描述群集的输入 API 模型，之后，AKS 引擎会在部署的 VM 和群集中运行。 本文介绍如何访问 AKS 引擎和相应的文件，以便之后可以使用它在 Kubernetes 群集上执行更新和缩放操作。

## <a name="access-aks-engine-in-the-dvm"></a>在 DVM 中访问 AKS 引擎

Kubernetes Azure Stack Hub 市场项启动的部署成功完成后，可以发现，用于部署群集的 AKS 引擎已安装在你为群集指定的资源组中创建的部署 VM 上，此 VM 不是 Kubernetes 群集的一部分，而是在其自己的 VNet 中创建的。 以下是用于查找该 VM 并在其中定位 AKS 引擎的步骤：

1.  打开 Azure Stack Hub 用户门户，找到为 Kubernetes 群集指定的资源组。
2.  在该资源组中，找到部署 VM。 其名称以前缀“vmd-”开头。
3.  选择该部署 VM。 在“概述”中，找到公共 IP 地址。 使用此地址和控制台应用（如 Putty）建立与该 VM 的 SSH 会话。
4.  在部署 VM 上的会话中，从以下路径找到 AKS 引擎：`./var/lib/waagent/custom-script/download/0/bin/aks-engine`
5.  找到 `.json` 文件，该文件描述用作 aks-engine 的输入的群集。 该文件位于 `/var/lib/waagent/custom-script/download/0/bin/azurestack.json`。 请注意，该文件具有用于部署群集的服务主体凭据。 如果决定保留该文件，请注意将该文件传输到受保护的存储。
6.  找到 AKS 引擎生成的输出目录 `/var/lib/waagent/custom-script/download/0/_output/<resource group name>`。 在此目录下的路径 `/var/lib/waagent/custom-script/download/0/bin/apimodel.json` 中找到输出 `apimodel.json`。 该目录和 `apimodel.json` 文件包含部署 Kubernetes 群集所需的所有已生成证书、密钥和凭据。 请将这些资源存储在安全位置。
7.  在路径 `/var/lib/waagent/custom-script/download/0/_output/k8smpi00/kubeconfig/kubeconfig.<location>.json` 处找到 Kubernetes 配置文件（通常称为 kubeconfig 文件），其中“\<location>”对应于 Azure Stack Hub 位置标识符 。 如果计划设置 kubectl 来访问 Kubernetes 群集，此文件会很有用。


## <a name="use-the-aks-engine-with-your-newly-created-cluster"></a>将 AKS 引擎与新创建的群集配合使用

找到 aks-engine、输入 apimodel.json 文件、输出目录和输出 apimodel.json 文件后，请立即将其存储在受保护的位置，你可以在任意 Linux VM 上使用 AKS 引擎二进制文件和输出 `apimodel.json`。

1.  若要继续使用 AKS 引擎以执行“升级”和“缩放”等操作，请将“aks-engine”二进制文件复制到目标计算机  。 如果使用的是同一台“vmd-”计算机，则复制到一个目录中。

2.  使用群集名称或其他用于指代新群集的易记名称创建目录，并将输出 apimodel.json 文件保存在该目录中。 请确保该目录位置是受保护的，因为此文件中包含凭据。 之后，即可运行 aks-engine 来执行“[缩放](azure-stack-kubernetes-aks-engine-scale.md)”或“[升级](azure-stack-kubernetes-aks-engine-upgrade.md)”等操作

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Stack Hub 上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)  
- [在 Azure Stack Hub 上排除 AKS 引擎故障](azure-stack-kubernetes-aks-engine-troubleshoot.md)  
