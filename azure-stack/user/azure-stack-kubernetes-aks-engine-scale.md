---
title: 缩放 Azure Stack Hub 上的 Kubernetes 群集
description: 了解如何缩放 Azure Stack Hub 上的 Kubernetes 群集。
author: mattbriggs
ms.topic: article
ms.date: 09/02/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: cfa531c42ebcc191461d4a801d0c17ce9526b912
ms.sourcegitcommit: b80d529ff47b15b8b612d8a787340c7b0f68165b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89473140"
---
# <a name="scale-a-kubernetes-cluster-on-azure-stack-hub"></a>缩放 Azure Stack Hub 上的 Kubernetes 群集

可以使用 scale 命令通过 AKS 引擎缩放群集。 scale 命令在输出目录中重用群集配置文件 (`apimodel.json`) 作为新的 Azure 资源管理器部署的输入。 引擎会对指定的代理池执行缩放操作。 缩放操作完成后，引擎会更新该 `apimodel.json` 文件中的群集定义，以反映新的节点计数，从而反映更新的当前群集配置。

## <a name="scale-a-cluster"></a>缩放群集

`aks-engine scale` 命令可以增加或减少 `aks-engine` Kubernetes 群集的现有代理池中的节点数。 将始终在代理池的末尾添加或删除节点。 删除之前，将封锁并排出节点。

### <a name="values-for-the-scale-command"></a>scale 命令的值

scale 命令使用以下参数查找群集定义文件并更新群集。

| 参数 | 示例 | 说明 |
| --- | --- | --- | 
| azure-env | AzureStackCloud | 使用 Azure Stack Hub 时，环境名称需要设置为 `AzureStackCloud`。 | 
| location | local | 这是 Azure Stack Hub 实例的区域。 对于 ASDK，此区域设置为 `local`。  | 
| resource-group | kube-rg | 包含群集的资源组的名称。 | 
| subscription-id |  | 订阅的 GUID，该订阅包含群集使用的资源。 请确保订阅上有足够的配额来进行扩展。 | 
| client-id |  | 通过 AKS 引擎创建群集时使用的服务主体的客户端 ID。 | 
| client-secret |  | 创建群集时使用的服务主体机密。 | 
| api-model | kube-rg/apimodel.json | 群集定义文件 (apimodel.json) 的路径。 此路径可能为：_output/\<dnsPrefix>/apimodel.json | 
| -new-node-count | 9 | 所需节点计数。 | 
| -master-FQDN |  | 主 FQDN。 纵向缩减时需要。 |
| identity-system | adfs | 可选。 如果使用 Active Directory 联合身份验证服务 (AD FS)，请指定标识管理解决方案。 |

缩放 Azure Stack Hub 中的群集时，必须指定 --azure-env 参数。 若要详细了解 AKS 引擎的 scale 命令中使用的参数及其值，请参阅 [Scale - 参数](https://github.com/Azure/aks-engine/blob/master/docs/topics/scale.md#parameters)。

### <a name="command-to-scale-your-cluster"></a>用于缩放群集的命令

若要缩放群集，请运行以下命令：

```bash
aks-engine scale \
    --azure-env AzureStackCloud   \
    --location <for an ASDK is local> \
    --resource-group <cluster resource group>
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model <path to your apomodel.json file>
    --new-node-count <desired node count> \
    --master-FQDN <master FQDN> \
    --identity-system adfs # required if using AD FS
```

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Stack Hub 上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)
- [升级 Azure Stack Hub 上的 Kubernetes 群集](azure-stack-kubernetes-aks-engine-upgrade.md)