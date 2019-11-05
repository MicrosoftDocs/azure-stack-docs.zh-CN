---
title: 缩放 Azure Stack 上的 Kubernetes 群集 |Microsoft Docs
description: 了解如何缩放 Azure Stack 上的 Kubernetes 群集。
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
ms.date: 09/25/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/25/2019
ms.openlocfilehash: 39c651649aea60ab12aae9daf35b0d07615bf697
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595301"
---
# <a name="scale-a-kubernetes-cluster-on-azure-stack"></a>缩放 Azure Stack 上的 Kubernetes 群集

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以使用 "**缩放**" 命令通过 AKS 引擎缩放群集。 **Scale**命令在输出目录中重复使用群集配置文件（`apimodel.json`）作为新的 Azure 资源管理器部署的输入。 引擎针对指定的代理池执行缩放操作。 缩放操作完成后，引擎将更新同一 `apimodel.json` 文件中的群集定义，以反映新的节点计数，以反映更新的当前群集配置。

## <a name="scale-a-cluster"></a>缩放群集

`aks-engine scale` 命令可以增加或减少 `aks-engine` Kubernetes 群集中现有代理池中的节点数。 将始终在代理池的末尾添加或删除节点。 删除之前，将封锁和排出节点。

### <a name="values-for-the-scale-command"></a>Scale 命令的值

Scale 命令使用以下参数查找群集定义文件并更新群集。

| 参数 | 示例 | 说明 |
| --- | --- | --- | 
| azure-env | AzureStackCloud | 使用 Azure Stack 时，环境名称需要设置为 `AzureStackCloud`。 | 
| location | local | 这是 Azure Stack 实例的区域。 对于 ASDK，区域设置为 `local`。  | 
| resource-group | kube-rg | 包含群集的资源组的名称。 | 
| subscription-id |  | 包含群集使用的资源的订阅的 GUID。 请确保在订阅中拥有足够的配额来进行缩放。 | 
| 客户端-id |  | 在从 AKS 引擎创建群集时使用的服务主体的客户端 ID。 | 
| 客户端-密码 |  | 创建群集时使用的服务主体密码。 | 
| api 模型 | kube-rg/apimodel | 群集定义文件的路径（apimodel）。 这可能是： _output/\<dnsPrefix >/apimodel.json | 
| -新节点计数 | 9 | 所需的节点计数。 | 
| -master-FQDN |  | 主 FQDN。 缩小时需要。 |
| 标识-系统 | adfs | 可选。 如果使用 Active Directory 联合服务（AD FS），则指定标识管理解决方案。 |

在 Azure Stack 中缩放群集时，必须指定 **--azure 环境**参数。 有关 AKS 引擎的**scale**命令中使用的参数及其值的详细信息，请参阅[scale-parameters](https://github.com/Azure/aks-engine/blob/master/docs/topics/scale.md#parameters)。

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

- 了解[Azure Stack 上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)
- [升级 Azure Stack 上的 Kubernetes 群集](azure-stack-kubernetes-aks-engine-upgrade.md)