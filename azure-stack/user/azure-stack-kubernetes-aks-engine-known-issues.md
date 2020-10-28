---
title: 在 Azure Stack Hub 上使用 AKS 引擎的已知问题
description: 了解在 Azure Stack Hub 上使用 AKS 引擎的已知问题。
author: mattbriggs
ms.topic: article
ms.date: 09/11/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/11/2020
ms.openlocfilehash: f12895e82cbe6e4c2370eec6fb33eb90383bb669
ms.sourcegitcommit: 716ca50bd198fd51a4eec5b40d5247f6f8c16530
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92898597"
---
# <a name="known-issues-with-the-aks-engine-on-azure-stack-hub"></a>在 Azure Stack Hub 上使用 AKS 引擎的已知问题

本主题介绍 Azure Stack Hub 上的 AKS 引擎的已知问题。

## <a name="unable-to-resize-cluster-vms-with-the-compute-service"></a>无法将群集 Vm 重设为计算服务

- **适用于** ： Azure Stack HUB、AKS engine (所有) 
- **描述** ：通过计算服务调整群集 vm 的大小不能与 AKS 引擎一起使用。 AKS 引擎在 API 模型 json 文件中维护群集的状态。 若要确保所需的 VM 大小反映在通过 AKS 引擎完成的任何创建、更新或缩放操作中，您必须在执行任何这些操作之前更新 API 模型。 例如，如果使用计算服务将已部署的群集上的 VM 大小更改为不同的大小，则在执行时，状态将丢失 `aks-engine update` 。
- **修正** ：若要执行此操作，请找到群集的 API 模型，将大小更改为，然后运行 `aks-engine update` 。
- **发生次数** ：尝试使用计算服务调整大小。

## <a name="disk-detach-operation-fails-in-aks-engine-0550"></a>在 AKS 引擎 0.55.0 中进行磁盘分离操作时失败

- 适用于：Azure Stack Hub（更新 2005）、AKS 引擎 0.55.0
- 说明：在尝试删除包含持久性卷的部署时，删除操作会触发一系列附加/分离错误。 这是由于 AKS 引擎 v0.55.0 云提供程序中的 bug 所致。 云提供程序使用了版本高于 Azure 资源管理器目前在 Azure Stack Hub（更新 2005）中支持的 API 版本的 API 来调用 Azure 资源管理器。
- **补救措施** ：可以在 [AKS 引擎 GitHub 存储库（问题 3817）](https://github.com/Azure/aks-engine/issues/3817#issuecomment-691329443)中找到详细信息和缓解步骤。 在 AKS 引擎的新版本和相应映像可用时立即升级。
- **发生率** ：删除包含持久性卷的部署时。



## <a name="upgrade-issues-in-aks-engine-0510"></a>AKS 引擎 0.51.0 中的升级问题

* 在将 Kubernetes 群集从 1.15.x 版升级到 1.16.x 版（aks 引擎升级）期间，以下 Kubernetes 组件的升级需要额外的手动步骤：kube-proxy、azure-cni-networkmonitor、csi-secrets-store、kubernetes-dashboard。 下面描述了你可能观察到的情况以及如何解决这些问题。

  * 在已连接的环境中，由于群集中没有迹象表明受影响的组件未升级，因此不会明显注意到此问题。 一切似乎都按预期进行。
  <!-- * In disconnected environments, you can see this problem when you run a query for the system pods status and see that the pods for the components mentioned below are not in "Ready" state: -->

    ```bash  
    kubectl get pods -n kube-system
    ```

  * 若要为上述每个组件解决此问题，请运行下表的“解决方法”列中的命令。

    |组件名称 |解决方法 |受影响方案|
    |---------------|-----------|------------------|
    |kube-proxy     | `kubectl delete ds kube-proxy -n kube-system` |已连接、离线 |
    |azure-cni-networkmonitor   | `kubectl delete ds azure-cni-networkmonitor -n kube-system`   | 已连接、离线 |
    |csi-secrets-store  |`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/secrets-store-csi-driver.yaml`<br>`kubectl delete ds csi-secrets-store -n kube-system` | 已断开连接 |
    |kubernetes-dashboard |在每个主节点上运行以下命令：<br>`sudo sed -i s/Always/IfNotPresent/g /etc/kubernetes/addons/kubernetes-dashboard.yaml` |已断开连接 |

* 此版本不支持 Kubernetes 1.17。 尽管有 GitHub 拉取请求 (PR) 引用 1.17，但它不受支持。

## <a name="aks-engine-get-versions-command-limitations"></a>aks-engine get-versions 命令限制

aks-engine `get-versions` 命令的输出仅与全局 Azure 相关，与 Azure Stack Hub 不相关。 有关不同升级路径的详细信息，请参阅[升级到更新 Kubernetes 版本的步骤](azure-stack-kubernetes-aks-engine-upgrade.md#steps-to-upgrade-to-a-newer-kubernetes-version)。

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 上的 AKS 引擎概述](azure-stack-kubernetes-aks-engine-overview.md)
