---
title: 将 Kubernetes 群集部署到 Azure Stack Hub 上的自定义虚拟网络
description: 了解如何将 Kubernetes 群集部署到 Azure Stack Hub 上的自定义虚拟网络。
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 9/2/2020
ms.openlocfilehash: bc1aee737743656d200bc51c431bfac64af5edf3
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247041"
---
# <a name="deploy-a-kubernetes-cluster-to-a-custom-virtual-network-on-azure-stack-hub"></a>将 Kubernetes 群集部署到 Azure Stack Hub 上的自定义虚拟网络 

可使用 Azure Kubernetes 服务 (AKS) 引擎在自定义虚拟网络上部署 Kubernetes 群集。 本文介绍如何在虚拟网络中查找所需的信息。 你可以在本文中找到计算群集使用的 IP 地址、在 API 模型中设置值以及设置路由表和网络安全组的步骤。

使用 AKS 引擎的 Azure Stack Hub 中的 Kubernetes 群集使用 kubenet 网络插件。 有关 Azure 中的 kubenet 网络插件联网的讨论，请参阅[在 Azure Kubernetes 服务 (AKS) 中结合自己的 IP 地址范围使用 kubenet 网络](/azure/aks/configure-kubenet)。

## <a name="constraints-when-creating-a-custom-virtual-network"></a>创建自定义虚拟网络时的约束

-  自定义 VNET 必须与 Kubernetes 群集的所有其他组件位于同一订阅中。
-  主节点池和代理节点池必须位于同一虚拟网络中。 你可以将节点部署到同一虚拟网络的不同子网中。
-  Kubernetes 群集子网必须使用自定义虚拟网络 IP 范围空间内的 IP 范围，请参阅[获取 IP 地址块](#get-the-ip-address-block)。

## <a name="create-custom-virtual-network"></a>创建自定义虚拟网络

Azure Stack Hub 实例中必须有一个自定义虚拟网络。 有关详细信息，请参阅[快速入门：使用 Azure 门户创建虚拟网络](/azure/virtual-network/quick-create-portal)。

在虚拟网络中创建新的子网。 需要获取子网资源 ID 和 IP 地址范围。 部署群集时，将在 API 模型中使用该资源 ID 和范围。

1. 在 Azure Stack Hub 实例中打开 Azure Stack Hub 用户门户。
2. 选择“所有资源”，
3. 在搜索框中输入虚拟网络的名称。
4. 选择“子网” > “+ 子网”，添加子网 。
5. 使用 CIDR 表示法添加“名称”和“地址范围” 。 选择“确定” 。
4. 在“虚拟网络”边栏选项卡中，选择“属性” 。 复制“资源 ID”，然后添加 `/subnets/<nameofyoursubnect>`。 将使用此值作为群集的 API 模型中 `vnetSubnetId` 密钥的值。 子网的资源 ID 使用以下格式：<br>`/subscriptions/SUB_ID/resourceGroups/RG_NAME/providers/Microsoft.Network/virtualNetworks/VNET_NAME/subnets/SUBNET_NAME`

    ![虚拟网络资源 ID](media/kubernetes-aks-engine-custom-vnet/virtual-network-id.png)

5. 在“虚拟网络”边栏选项卡中，选择“子网” 。 选择一个子网名称，例如默认。
    
    ![虚拟网络 CIDR 块](media/kubernetes-aks-engine-custom-vnet/virtual-network-cidr-block.png)
    
6. 在“子网”边栏选项卡中，记下地址范围和虚拟网络 CIDR 块，例如：`10.1.0.0 - 10.1.0.255 (256 addresses)` 和 `10.1.0.0/24`。

## <a name="considerations-for-selecting-an-address-space"></a>选择地址空间的注意事项

创建自定义虚拟网络时，可以为每个子网指定网络的 IP 地址空间和 IP 地址范围。 选择要在 Kubernetes 群集中使用的地址空间和范围时，请考虑以下因素：
-  地址空间重叠可能会导致 IP 地址冲突或通信错误。 若要降低 IP 地址重叠的风险，请为新的虚拟网络选择唯一的地址空间。
-  `10.` 和 `172.` 范围内的地址空间通常用于专用网络，并且它们可能由现有数据中心基础结构使用。 如果 Kubernetes 应用程序使用数据中心中的资源，可通过为自定义虚拟网络选择不同于数据中心地址空间的地址空间来降低冲突风险。
-  建议为 Kubernetes 群集使用专用子网。

## <a name="get-the-ip-address-block"></a>获取 IP 地址块

AKS 引擎支持部署到现有虚拟网络中。 部署到现有子网中时，群集将为代理使用一个连续 IP 的块，为主机使用另一个 IP 块。

需要设置两个值。 需了解要为群集保留的 IP 地址数，以及子网 IP 空间中的第一个连续静态 IP。

使用多个主节点时，AKS 引擎需要最多 16 个未使用的 IP 地址。 群集将为每个主节点使用一个 IP 地址（最多五个主节点）。 在最后一个主节点之后，AKS 引擎还需要 10 个 IP 地址，用于为 IP 地址预留所准备的空余空间。 最后，为主机和空余空间预留使用了总共 16 个地址之后，还要为负载均衡器使用一个额外的 IP 地址。

放置 IP 地址块时，需要为子网分配以下现有的 IP 地址：
 - 前四个 IP 地址和最后一个 IP 地址为预留地址，不能在任何 Azure 子网中使用
 - 应开启为 16 个 IP 地址配置的缓冲区并保持开启状态。
 - 群集的第一个 IP 的值应接近地址空间的末端值，以免发生 IP 冲突。 如果可能，为 `firstConsecutiveStaticIP` 属性分配一个接近子网中可用 IP 地址空间末端值的 IP 地址。

在下面的示例中，可以看到基于上述各注意事项如何填充子网中的 IP 地址。 上述内容适用于三个主节点。 如果使用的子网有 256 个地址，例如 10.1.0.0/24，则需要将第一个连续静态 IP 地址设置为 207。 下表显示了地址和注意事项：

| /24 子网的范围 | Number | 注意 |
| --- | --- | --- |
| 10.1.0.0 - 10.1.03 | 4 | 预留在 Azure 子网中。 |
| **10.1.0.224** - 10.1.0.238 | 14 | AKS 引擎定义的群集的 IP 地址计数。<br><br> 3 个主机的 3 个 IP 地址<br>空余空间的 10 个 IP 地址<br>负载均衡器的 1 个 IP 地址 |
| 10.1.0.239 - 10.1.0.255 | 16 | 16 个 IP 地址缓冲区。 |
| 10.1.0.256 | 1 | 预留在 Azure 子网中。 |

在此示例中，`firstConsecutiveStaticIP` 属性将为 `10.1.0.224`。

对于较大的子网（例如超过 6 万个地址的 /16），可能会发现将静态 IP 地址设置为网络空间的末端值是不切实际的。 在设置群集静态 IP 地址范围时，应选择与 IP 空间中前 24 个地址距离较远的值，以便在声明地址时可以复原群集。

## <a name="update-the-api-model"></a>更新 API 模型

更新用于将群集从 AKS 引擎部署到自定义虚拟网络的 API 模型。

在 masterProfile 中设置以下值：

| 字段 | 示例 | 说明 |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | 指定子网的资源 ID。  |
| firstConsecutiveStaticIP | 10.1.0.224 | 为 `firstConsecutiveStaticIP` 配置属性分配一个 IP 地址，该地址接近所需子网中可用 IP 地址空间的末端值。 `firstConsecutiveStaticIP` 仅适用于主池。 |

在 agentPoolProfiles 中设置以下值：

| 字段 | 示例 | 说明 |
| --- | --- | --- |
| vnetSubnetId | `/subscriptions/77e28b6a-582f-42b0-94d2-93b9eca60845/resourceGroups/MDBN-K8S/providers/Microsoft.Network/virtualNetworks/MDBN-K8S/subnets/default` | 指定子网的 Azure 资源管理器路径 ID。  |

在“orchestratorProfile”中，找到“kubernetesConfig”并设置以下值 ：

| 字段 | 示例 | 描述 |
| --- | --- | --- |
| clusterSubnet | `172.16.244.0/24` | 群集子网（POD 网络）的 IP 范围必须是你定义的自定义 VNET IP 范围空间内的 IP 范围。 |

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
    ...
"kubernetesConfig": [
  {
    ...
    "clusterSubnet": "172.16.244.0/24",
    ...
  },

```

## <a name="deploy-your-cluster"></a>部署群集

将值添加到 API 模型后，可以通过使用 AKS 引擎的 `deploy` 命令从客户端计算机上部署群集。 有关说明，请参阅[部署 Kubernetes 群集](azure-stack-kubernetes-aks-engine-deploy-cluster.md#deploy-a-kubernetes-cluster)。

## <a name="set-the-route-table"></a>设置路由表

部署群集后，返回 Azure Stack 用户门户中的虚拟网络。 在子网边栏选项卡中同时设置路由表和网络安全组 (NSG)。 如果不使用 Azure CNI，例如，`networkPlugin`：`kubernetesConfig` API 模型配置对象中的 `kubenet`。 成功将群集部署到自定义虚拟网络后，请从群集资源组中的网络边栏选项卡获取路由表资源的 ID。

1. 在 Azure Stack Hub 实例中打开 Azure Stack Hub 用户门户。
2. 选择“所有资源”，
3. 在搜索框中输入虚拟网络的名称。
4. 选择“子网”，然后选择包含群集的子网的名称。
    
    ![路由表和网络安全组](media/kubernetes-aks-engine-custom-vnet/virtual-network-route-table.png)
    
5. 选择“路由表”，然后为群集选择路由表。

> [!NOTE]  
> 用于 Kubernetes Windows 群集的自定义虚拟网络存在一个[已知问题](https://github.com/Azure/aks-engine/issues/371)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Stack Hub 上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)  
- 请阅读[用于容器的 Azure Monitor 概述](/azure/azure-monitor/insights/container-insights-overview)
