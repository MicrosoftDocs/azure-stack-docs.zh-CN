---
title: 为组托管服务帐户支持准备 Windows 节点
description: 了解如何在 Windows 节点上配置容器的组托管服务帐户
author: v-susbo
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-susbo
ms.openlocfilehash: 6710bb9c6a9e53dea60d14be686a4ea953e1bf19
ms.sourcegitcommit: 3534ff416d40518eaba87eac8eca6d3082fc1d3f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96563552"
---
# <a name="prepare-windows-nodes-for-group-managed-service-account-support"></a>为组托管服务帐户支持准备 Windows 节点

> 适用于 Azure Stack HCI 上的 AKS、Windows Server 2019 Datacenter 上的 AKS 运行时

组托管服务帐户是一种特定类型的 Active Directory 帐户，提供：
- 自动密码管理
- 简化的服务主体名称 (SPN) 管理
- 跨多个服务器将管理委派给其他管理员的能力 

若要为在 Windows 节点上运行的 Pod 和容器配置组托管服务帐户 (gMSA)，必须首先将 Windows 节点加入 Active Directory 域。

若要启用组托管服务帐户支持，Kubernetes 群集名称必须少于四个字符。 它需要少于四个字符，因为已加入域的服务器名称的最大支持长度为15个字符。 辅助角色节点 Azure Stack HCI Kubernetes 群集名称约定中的 AKS 将向节点名称添加一些预定义的字符。

## <a name="join-the-worker-nodes-to-a-domain"></a>将辅助角色节点加入到域中

若要将 Windows 辅助角色节点加入到域中，请通过运行 get 并记下值来登录到 Windows 辅助角色节点 `kubectl` `EXTERNAL-IP` 。

```
   kubectl get nodes -o wide
```  

随后可以使用 `ssh Administrator@ip` 通过 SSH 登录节点。

成功登录 Windows 工作器节点之后，运行以下 PowerShell 命令以将节点加入域。 系统将提示输入 **域管理员帐户** 凭据。 还可以使用已授权将计算机加入给定域的提升的用户凭据。 然后，需要重启 Windows 辅助角色节点。 

```powershell
add-computer --domainame "YourDomainName" -restart
```

将所有 Windows 辅助角色节点加入到域后，请按照 [配置 gMSA](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa/)中的详细步骤进行操作。 这些步骤将帮助你在 Kubernetes 群集上应用 Kubernetes gMSA 自定义资源定义和 webhook。

有关具有 gMSA 的 Windows 容器的详细信息，请参阅 [windows 容器和 gMSA](https://docs.microsoft.com/virtualization/windowscontainers/manage-containers/manage-serviceaccounts)。 有关疑难解答信息，请参阅 [故障排除](troubleshoot.md) 页。 

## <a name="next-steps"></a>后续步骤

* [使用 Azure Monitor 来监视群集和应用程序](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters)。
* [在 Kubernetes 群集上使用永久性卷](persistent-volume.md)。
