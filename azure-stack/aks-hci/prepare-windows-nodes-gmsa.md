---
title: 为组托管服务帐户支持准备 Windows 节点
description: 了解如何在 Windows 节点上配置容器的组托管服务帐户
author: v-susbo
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-susbo
ms.openlocfilehash: fd08f288d816468610dadf7e5c063c08b63c0cf2
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254698"
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

若要将 Windows 辅助角色节点加入到域中，请通过运行并记录值来登录到 Windows 辅助角色节点 `kubectl get` `EXTERNAL-IP` 。

```
kubectl get nodes -o wide
```  

随后可以使用 `ssh Administrator@ip` 通过 SSH 登录节点。 有关如何使用 SSH 登录的详细信息，请参阅 [使用 ssh 对 Windows 工作节点进行故障排除](troubleshoot.md#troubleshooting-windows-worker-nodes)。

使用 SSH 成功登录 Windows 辅助节点后，运行以下命令将节点加入到域。 然后，需要重新启动 Windows 辅助角色节点。 

```
netdom.exe join %computername% /domain:DomainName /UserD:DomainName\UserName /PasswordD:Password
```  

将所有 Windows 辅助角色节点加入到域后，请按照 [配置 gMSA](https://kubernetes.io/docs/tasks/configure-pod-container/configure-gmsa/)中的详细步骤进行操作。 这些步骤将帮助你在 Kubernetes 群集上应用 Kubernetes gMSA 自定义资源定义和 webhook。

有关具有 gMSA 的 Windows 容器的详细信息，请参阅 [windows 容器和 gMSA](/virtualization/windowscontainers/manage-containers/manage-serviceaccounts)。 

## <a name="next-steps"></a>后续步骤

* [使用 Azure Monitor 来监视群集和应用程序](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters)。
* [在 Kubernetes 群集上使用永久性卷](persistent-volume.md)。