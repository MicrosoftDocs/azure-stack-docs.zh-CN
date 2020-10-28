---
title: 为 Azure Stack 集线器上的 AKS 引擎启用服务帐户令牌卷投影
description: 了解如何在 Azure Stack 中心为 AKS 引擎启用服务帐户令牌卷投影
author: mattbriggs
ms.topic: article
ms.date: 10/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/23/2020
ms.openlocfilehash: 5e712577bb01f7f084c24eadfd3931ebb67f23d9
ms.sourcegitcommit: 716ca50bd198fd51a4eec5b40d5247f6f8c16530
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/28/2020
ms.locfileid: "92906156"
---
# <a name="enabling-service-account-token-volume-projection-for-the-aks-engine-on-azure-stack-hub"></a>为 Azure Stack 集线器上的 AKS 引擎启用服务帐户令牌卷投影

Istio 是一个可配置的开源服务网格层，用于连接、监视和保护 Kubernetes 群集中的容器。 Istio 1.3 和更高版本使用名为 *服务帐户令牌卷投影* 的 Kubernetes 中的功能。 默认情况下，在由 AKS 引擎部署的 Kubernetes 群集中未启用此功能。 在本文中，可以在元素中找到 API 模型 json 属性 `apiServerConfig` ，其中显示了为群集启用服务帐户令牌卷投影所需的 KUBERNETES API 服务器标志。

有关服务帐户令牌卷投影的详细信息，请参阅 [服务帐户令牌卷投影](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection)。

## <a name="enable-service-account-token-volume-projection"></a>启用服务帐户令牌卷投影

若要启用服务帐户令牌卷投影，请将以下设置添加到 API 模型 json 文件中。 

```json
{
    "kubernetesConfig": {
        "apiServerConfig": {
            "--service-account-api-audiences": "api,istio-ca",
            "--service-account-issuer": "kubernetes.default.svc",
            "--service-account-signing-key-file": "/etc/kubernetes/certs/apiserver.key"
        }
    }
}
```

> [!Note]  
> 你可能需要调整 `--service-account-api-audiences` 和 `--service-account-issuer` 到你的特定用例。

有关完整的示例 API 模型，请参阅 [ 上的istio.js](https://github.com/Azure/aks-engine/blob/master/examples/service-mesh/istio.json)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Stack Hub 上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)
- [升级 Azure Stack Hub 上的 Kubernetes 群集](azure-stack-kubernetes-aks-engine-upgrade.md)
