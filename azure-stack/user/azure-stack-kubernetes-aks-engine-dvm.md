---
title: 将 Marketplace 项群集移到 Azure Stack 集线器上的 AKS 引擎
description: 了解如何将 Marketplace 项群集移到 Azure Stack 集线器上的 AKS 引擎。
author: mattbriggs
ms.topic: article
ms.date: 3/19/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/19/2020
ms.openlocfilehash: f15c870a1b256ffa546672a3abde2fc68f9baa4f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80069007"
---
# <a name="move-your-marketplace-item-cluster-to-the-aks-engine-on-azure-stack-hub"></a>将 Marketplace 项群集移到 Azure Stack 集线器上的 AKS 引擎

Kubernetes Azure Stack 中心市场项使用 Azure 资源管理器模板来部署部署虚拟机（VM），以下载和安装 AKS 引擎，并生成用于描述群集的输入 API 模型，然后在 VM 和部署的群集中运行 AKS 引擎。 本文介绍如何访问 AKS 引擎和相应的文件，以便你可以使用它来对 Kubernetes 群集执行更新和缩放操作。

## <a name="access-aks-engine-in-the-dvm"></a>访问 DVM 中的 AKS 引擎

Kubernetes Azure Stack 中心市场项启动的部署成功完成后，可以在为群集指定的资源组中找到用于部署群集的 AKS 引擎，此 VM 不属于 Kubernetes 群集，它是在它自己的 VNet 中创建的。 下面是查找 VM 并在其中找到 AKS 引擎的步骤：

1.  打开 Azure Stack 集线器用户门户，找到为 Kubernetes 群集指定的资源组。
2.  在资源组中，找到 "部署 VM"。 名称以前缀： **vmd-** 开头。
3.  选择部署 VM。 在 "概述" 中，请查看公共 IP 地址。 使用此地址和控制台应用（如 Putty）与 VM 建立 SSH 会话。
4.  在部署虚拟机上的会话中，可在以下路径找到 AKS 引擎：`./var/lib/waagent/custom-script/download/0/bin/aks-engine`
5.  找到描述`.json`用作 aks 的输入的分类的文件。 以为形式的`/var/lib/waagent/custom-script/download/0/bin/azurestack.json`文件。 请注意，该文件具有用于部署群集的服务主体凭据。 如果决定保留该文件，请注意将文件传输到受保护的存储区。
6.  在处`/var/lib/waagent/custom-script/download/0/_output/<resource group name>`找到由 AKS 引擎生成的输出目录。 在此目录中，查找路径`apimodel.json` `/var/lib/waagent/custom-script/download/0/bin/apimodel.json`中的输出。 目录和`apimodel.json`文件包含部署 Kubernetes 群集所需的所有生成的证书、密钥和凭据。 将这些资源存储在一个安全的位置。
7.  在路径`$HOME/<output dir>/kubeconfig/kubeconfing.<location>.json`中找到 Kubernetes 配置文件（通常称为**kubeconfig**文件，其中** \<location>** 对应于 Azure Stack 中心位置标识符）。 如果计划设置**kubectl**来访问 Kubernetes 群集，此文件将很有用。

## <a name="use-the-aks-engine-with-your-newly-created-cluster"></a>将 AKS 引擎用于新创建的群集

找到 aks、输入 apimodel 文件、输出目录和输出 apimodel 文件后，将其存储在安全的位置，即可在任何 Linux VM 上使用 AKS 引擎二进制文件和输出`apimodel.json` 。

1.  若要继续使用 AKS 引擎，若要执行**升级**和**缩放**等操作，请将**AKS**二进制文件复制到目标计算机。 如果将同一**vmd**计算机用于目录。

2.  使用群集名称或引用新群集的其他 mnemotechnic 名称创建目录，并将输出 apimodel 文件保存在其中。 请确保它是受保护的位置，因为此文件包含凭据。 之后，你可以运行 aks，以运行[扩展](azure-stack-kubernetes-aks-engine-scale.md)或[升级](azure-stack-kubernetes-aks-engine-upgrade.md)等操作

## <a name="next-steps"></a>后续步骤

- 了解[Azure Stack 集线器上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)  
- [对 Azure Stack 集线器上的 AKS 引擎进行故障排除](azure-stack-kubernetes-aks-engine-troubleshoot.md)  

