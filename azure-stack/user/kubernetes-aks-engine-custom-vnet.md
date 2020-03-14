---
title: 将 Kubernetes 群集部署到 Azure Stack 集线器上的自定义虚拟网络
description: 了解如何将 Kubernetes 群集部署到 Azure Stack 集线器上的自定义虚拟网络。
author: mattbriggs
ms.topic: article
ms.date: 2/28/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 2/28/2020
ms.openlocfilehash: 91b70c490f2771510a5bde202484837dd170166f
ms.sourcegitcommit: 4301e8dee16b4db32b392f5979dfec01ab6566c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79318055"
---
# <a name="deploy-a-kubernetes-cluster-to-a-custom-virtual-network-on-azure-stack-hub"></a>将 Kubernetes 群集部署到 Azure Stack 集线器上的自定义虚拟网络 

可以使用 Azure Kubernetes Service （AKS）引擎在自定义虚拟网络上部署 Kubernetes 群集。 本文介绍如何在虚拟网络中查找所需的信息。 你可以找到用于计算群集使用的 IP 地址、在 API 模型中设置工位以及设置路由表和网络安全组的步骤。

使用 AKS 引擎 Azure Stack 集线器中的 Kubernetes 群集使用 kubenet 网络插件。 有关 Azure 中的 kubenet 网络插件网络的讨论，请参阅[在 Azure Kubernetes 服务（AKS）中使用你自己的 IP 地址范围的 kubenet 网络](https://docs.microsoft.com/azure/aks/configure-kubenet)。

## <a name="create-custom-virtual-network"></a>创建自定义虚拟网络

你的 Azure Stack 中心实例中必须有一个自定义虚拟网络。 有关详细信息，请参阅[快速入门：使用 Azure 门户创建虚拟网络](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)。

在虚拟网络中创建新的子网。 你将需要获取子网资源 ID 和 IP 地址范围。 部署群集时，将在 API 模型中使用资源 ID 和范围。

1. 在 Azure Stack 中心实例中打开 Azure Stack 集线器用户门户。
2. 选择“所有资源”，
3. 在 "搜索" 框中输入虚拟网络的名称。
4. 选择 "**子**网" > " **+** 子网" 以添加子网。
5. 使用 CIDR 表示法添加**名称**和**地址范围**。 选择“确定”。
4. 在 "**虚拟网络**" 边栏选项卡中选择 "**属性**"。 复制**资源 ID**，然后添加 `/subnets/<nameofyoursubnect>`。 你将使用此值作为群集的 API 模型中 `vnetSubnetId` 密钥的值。 子网的资源 ID 使用以下格式：<br>`/subscriptions/SUB_ID/resourceGroups/RG_NAME/providers/Microsoft.Network/virtualNetworks/VNET_NAME/subnets/SUBNET_NAME`

    ![虚拟网络资源 ID](media/kubernetes-aks-engine-custom-vnet/virtual-network-id.png)

5. 在 "**虚拟网络**" 边栏选项卡中选择**子网**。 选择 "子网名称"，例如 "默认"。
    
    ![虚拟网络 CIDR 块](media/kubernetes-aks-engine-custom-vnet/virtual-network-cidr-block.png)
    
6. 在 "子网" 边栏选项卡中，记下地址范围和虚拟网络 CIDR 块，例如： `10.1.0.0 - 10.1.0.255 (256 addresses)` 和 `10.1.0.0/24`。



## <a name="get-the-ip-address-block"></a>获取 IP 地址块

AKS 引擎支持部署到现有的虚拟网络中。 将部署到现有子网中时，群集将使用代理的连续 Ip 块，并为主机使用另一个。

需要设置两个值。 你将需要知道需要为群集保留的 IP 地址数，以及子网 IP 空间内的第一个连续静态 IP。

当使用多个主节点时，AKS 引擎要求的范围最多为16个未使用的 IP 地址。 群集将为每个主节点使用一个 IP 地址，最多五个主机。 AKS 引擎还需要在最后一个主节点后面提供后10个 IP 地址，以便保留 IP 地址。 最后，在主保留项和预留空间保留后，负载均衡器将使用额外的 IP 地址（共16个）。

放置 IP 地址块时，子网需要以下现有 IP 地址的分配：
 - 前四个 IP 地址和最后一个 IP 地址已保留，不能在任何 Azure 子网中使用
 - 应将16个 IP 地址的缓冲区保持为打开状态。
 - 群集的第一个 IP 的值应接近地址空间的末尾，以免发生 IP 冲突。 如果可能，请将 `firstConsecutiveStaticIP` 属性分配到子网中可用 IP 地址空间*末尾*附近的 ip 地址。

在以下示例中，你可以看到这些各种注意事项如何填写子网中的 IP 范围。 这适用于三个母版。 如果你使用的子网包含256地址，例如 10.1.0.0/24，则需要在207设置第一个连续的静态 IP 地址。 下表显示了地址和注意事项：

| /24 子网的范围 | Number | 注意 |
| --- | --- | --- |
| 10.1.0.0-10.1.03 | 4 | 在 Azure 子网中保留。 |
| **10.1.0.224**-10.1.0.238 | 14 | AKS 引擎定义的群集的 IP 地址计数。<br><br> 3个主机的3个 IP 地址<br>10个 IP 地址用于预留空间<br>1负载均衡器的 IP 地址 |
| 10.1.0.239 - 10.1.0.255 | 16 | 16个 IP 地址缓冲区。 |
| 10.1.0.256 | 1 | 在 Azure 子网中保留。 |

在此示例中，`firstConsecutiveStaticIP` 属性将 `10.1.0.224`。

对于较大的子网（例如/16 个以上60000的地址），可能找不到将静态 IP 分配设置到网络空间末尾的情况。 设置群集静态 IP 地址范围，使其远离 IP 空间中的前24个地址，以便在声明地址时可以恢复群集。


## <a name="update-the-api-model"></a>更新 API 模型

更新用于将群集从 AKS 引擎部署到自定义虚拟网络的 API 模型。

在**发现 masterprofile**中，设置以下值：

| 字段 | 示例 | 说明 |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | 指定子网的资源 ID。  |
| firstConsecutiveStaticIP | 10.1.0.224 | 为 `firstConsecutiveStaticIP` 配置属性分配一个在所需子网中可用 IP 地址空间*末尾*附近的 ip 地址。 `firstConsecutiveStaticIP` 仅适用于主池。 |

在**agentPoolProfiles**中，设置以下值：

| 字段 | 示例 | 说明 |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | 指定子网的 Azure 资源管理器路径 ID。  |

例如：

```json
"masterProfile": {
  ...
  "vnetSubnetId": "/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default",
  "firstConsecutiveStaticIP": "10.1.0.224",
  ...
},
...
"agentPoolProfiles": [
  {
    ...
    "vnetSubnetId": "/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default",
    ...
  },

```

## <a name="deploy-your-cluster"></a>部署群集

将值添加到 API 模型后，可以使用 AKS 引擎通过使用 `deploy` 命令从客户端计算机部署群集。 有关说明，请参阅[部署 Kubernetes 群集](azure-stack-kubernetes-aks-engine-deploy-cluster.md#deploy-a-kubernetes-cluster)。

## <a name="set-the-route-table-and-network-security-group"></a>设置路由表和网络安全组

部署群集后，请在 Azure Stack 用户门户中返回到虚拟网络。 设置 "子网" 边栏选项卡中的路由表和网络安全组（NSG）。 例如，如果不使用 Azure CNI，则在 `kubernetesConfig` API 模型配置对象中 `networkPlugin`： `kubenet`。 成功将群集部署到自定义虚拟网络后，从群集资源组中的**网络**边栏选项卡获取路由表资源的 ID。

1. 在 Azure Stack 中心实例中打开 Azure Stack 集线器用户门户。
2. 选择“所有资源”，
3. 在 "搜索" 框中输入虚拟网络的名称。
4. 选择 "**子网**"，然后选择包含群集的子网的名称。
    
    ![路由表和网络安全组](media/kubernetes-aks-engine-custom-vnet/virtual-network-rt-nsg.png)
    
5. 选择 "**路由表**"，然后选择群集的路由表。
6. 选择 "**网络安全组**"，然后选择群集的 NSG。

> [!Note]  
> 用于 Kubernetes Windows 群集的自定义虚拟网络具有一个[已知问题](https://github.com/Azure/aks-engine/issues/371)。

## <a name="next-steps"></a>后续步骤

- 了解[Azure Stack 集线器上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)  
- 了解[容器 Azure Monitor 概述](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)