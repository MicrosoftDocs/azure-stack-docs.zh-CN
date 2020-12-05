---
title: 将 Azure Stack HCI 群集上的 Azure Kubernetes 服务连接到 Kubernetes 的 Azure Arc
description: 将 Azure Stack HCI 群集上的 Azure Kubernetes 服务连接到 Kubernetes 的 Azure Arc
author: abha
ms.topic: how-to
ms.date: 12/02/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: e7a407e587918a6ee9648c51c2c218ab51e7132f
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96612346"
---
# <a name="connect-an-azure-kubernetes-service-on-azure-stack-hci-cluster-to-azure-arc-for-kubernetes"></a>将 Azure Stack HCI 群集上的 Azure Kubernetes 服务连接到 Kubernetes 的 Azure Arc

> 适用于 Azure Stack HCI 上的 AKS、Windows Server 2019 Datacenter 上的 AKS 运行时

Azure Stack HCI 群集上的 Azure Kubernetes 服务附加到 Azure Arc 时，它将显示在 Azure 门户中。 它具有一个 Azure 资源管理器 ID 和一个托管标识。 群集附加到标准 Azure 订阅，位于资源组中，可以像任何其他 Azure 资源一样接收标记。

若要将 Kubernetes 群集连接到 Azure，群集管理员需要部署代理。 这些代理在名为 `azure-arc` 的 Kubernetes 命名空间中运行，并且是标准 Kubernetes 部署。 代理负责连接到 Azure、收集 Azure Arc 日志和指标并监视配置请求。

已启用 Azure Arc 的 Kubernetes 支持行业标准 SSL 来保护传输中的数据。 此外，数据以静态加密方式存储在 Azure Cosmos DB 数据库中，确保数据保密性。

以下步骤提供了有关在 Azure Stack HCI 群集到 Azure Arc 上载入 Azure Kubernetes 服务的演练。 **如果已通过 Windows 管理中心将 Kubernetes 群集载入到 Azure Arc，则可以跳过这些步骤。**

## <a name="before-you-begin"></a>在开始之前

验证是否已准备好以下要求：

* Azure Stack HCI 上的 Azure Kubernetes 服务群集，其中至少有一个启动并运行的 Linux 工作器节点。 

* 需要一个 kubeconfig 文件来访问群集上的群集和群集管理角色，以便部署启用了 Arc 的 Kubernetes 代理。
* 安装了 Azure Stack HCI 上的 Azure Kubernetes 服务 PowerShell 模块。
* 安装支持 Azure Arc 的 Kubernetes CLI 扩展需要 Azure CLI 版本 2.3 +。 [安装 Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)。 还可以更新到最新版本，以确保具有 Azure CLI 版本 2.3 +。
* 作为所有者或参与者的 Azure 订阅。 
* 在 PowerShell 管理窗口中运行本文档中的命令。


## <a name="network-requirements"></a>网络要求

Azure Arc 代理需要以下协议/端口/出站 URL 才能正常运行。

* 端口 443 上的 TCP --> `https://:443`
* 端口 9418 上的 TCP --> `git://:9418`

| 终结点 (DNS)                                                                                               | 说明                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | 代理需要该终结点才可连接到 Azure 并注册群集                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | 代理的数据平面终结点，用于推送状态和提取配置信息                                      |
| `https://docker.io`                                                                                            | 拉取容器映像所需                                                                                         |
| `https://github.com`、git://github.com                                                                         | 示例 GitOps 存储库托管在 GitHub 上。 配置代理需要连接到指定的任何 git 终结点。 |
| `https://login.microsoftonline.com`                                                                            | 提取和更新 Azure 资源管理器令牌所需                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | 拉取 Azure Arc 代理的容器映像所需                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  需要请求系统分配的托管标识证书                                                                  |

## <a name="step-1-log-in-to-azure"></a>步骤1：登录到 Azure

登录到 Azure，并在登录后，将你作为默认订阅的所有者或参与者设置为 Azure 订阅。

```console
az login
az account set --subscription "00000000-aaaa-bbbb-cccc-000000000000"
```

## <a name="step-2-register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>步骤2：注册启用了 Azure Arc Kubernetes 的两个提供程序：

如果已为订阅注册了两个启用了 Azure Arc 的 Kubernetes 服务的提供程序，则可以跳过此步骤。 注册是一个异步过程，需要为每个订阅一次。 注册可能需要大约 10 分钟。 

```console
az provider register --namespace Microsoft.Kubernetes
az provider register --namespace Microsoft.KubernetesConfiguration
```

可以检查是否已注册了以下命令：

```console
az provider show -n Microsoft.Kubernetes -o table
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="step-3-create-a-resource-group"></a>步骤3：创建资源组

需要一个资源组来容纳连接的群集资源。 可以在美国东部或西欧位置使用现有资源组。 如果在美国东部或西欧位置没有现有资源组，请使用以下命令创建新的资源组：

```console
az group create --name AzureArcTest -l EastUS -o table
```

## <a name="step-4-create-a-new-service-principal"></a>步骤4：创建新的服务主体


如果已使用角色创建了服务主体， `contributor` 并且知道了服务主体的 appID、password 和租户值，则可以跳过此步骤。

使用信息性名称创建一个新的服务主体。 对于 Azure Active Directory 租户，此名称必须是唯一的。 服务主体的默认角色是 `Contributor` 。 此角色具有读取和写入到 Azure 帐户的完全权限。 你还可以将此服务主体重新用于多个群集到 Azure Arc。将服务主体的作用域设置为 *订阅/资源组*。 *请确保保存了服务主体的 appID、password 和租户值，因为后续步骤中将需要这些详细信息。*

```console
az ad sp create-for-RBAC --name "azure-arc-for-k8s" --scope /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}
```

**输出：**

```
{
  "appId": "00000000-0000-0000-0000-000000000000",
  "displayName": "azure-arc-for-k8s",
  "name": "https://azure-arc-for-k8s",
  "password": "aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee",
  "tenant": "ffffffff-gggg-hhhh-iiii-jjjjjjjjjjjj"
}
```
## <a name="step-5-save-service-principal-details"></a>步骤5：保存服务主体详细信息
将创建的服务主体的 appId、password 和租户值以及群集名称、Azure 订阅 ID、资源组名称和位置保存在 PowerShell 变量中。 这将确保你可以重复使用其他教程中的详细信息。 请确保在记事本中保存这些值，以防需要关闭 PowerShell 会话。

```PowerShell
$clusterName = #<name of your Kubernetes cluster>
$resourceGroup = #<Azure resource group to store your connected Kubernetes cluster in Azure Arc>
$location = #<Azure resource group location. This can only be eastus or westeurope for Azure Arc for Kubernetes>
$subscriptionId = #<Azure subscription Id>
$appId = #<appID from the service principal created above>
$password = #<password from the service principal created above>
$tenant = #<tenant from the service principal created above>
```
请确保已通过运行以下操作将正确的值分配给变量：

```PowerShell
echo $clusterName 
echo $resourceGroup
echo $location 
echo $subscriptionId 
echo $appId 
echo $password 
echo $tenant 
```

## <a name="step-6-connect-to-azure-arc-using-service-principal-and-the-aks-hci-powershell-module"></a>步骤6：使用服务主体和 Aks-Hci PowerShell 模块连接到 Azure Arc

接下来，我们将使用服务主体和 Aks-Hci PowerShell 模块将 Kubernetes 群集连接到 Azure。 此步骤将 Kubernetes 的 Azure Arc 代理部署到 `azure-arc` 命名空间中。

引用新创建的服务主体，并运行 `Install-AksHciArcOnboarding` Aks-Hci PowerShell 模块中提供的命令。

```PowerShell
Install-AksHciArcOnboarding -clusterName $clusterName -resourcegroup $resourceGroup -location $location -subscriptionid $subscriptionId -clientid $appId -clientsecret $password -tenantid $tenant
```
## <a name="verify-connected-cluster"></a>验证已连接的群集

可以在 [Azure 门户](https://portal.azure.com/)上查看 Kubernetes 群集资源。 在浏览器中打开门户后，请导航到资源组和启用了 Azure Arc 的 Kubernetes 资源，该资源基于之前在 PowerShell 命令中使用的资源名称和资源组名称输入 `Install-AksHciArcOnboarding` 。

> [!NOTE]
> 载入群集后，在 Azure 门户中启用了 Azure Arc Kubernetes 资源的 "概述" 页上，大约需要5到10分钟的群集元数据 (群集版本、代理版本、) 的节点数。

若要删除群集，或在群集位于出站代理服务器后面时连接群集，请访问 [连接启用了 Azure Arc 的 Kubernetes 群集](/azure/azure-arc/kubernetes/connect-cluster)。

## <a name="azure-arc-agents-for-kubernetes"></a>适用于 Kubernetes 的 Azure Arc 代理

已启用 Azure Arc 的 Kubernetes 会将几个运算符部署到 `azure-arc` 命名空间中。 可在此处查看这些部署和 Pod：

```console
kubectl -n azure-arc get deployments,pods
```

启用了 Azure Arc 的 Kubernetes 由几个在部署到 `azure-arc` 命名空间的群集中运行的代理（运算符）组成。

* `deployment.apps/config-agent`：监视群集上应用的源代码管理配置资源的已连接群集并更新符合性状态
* `deployment.apps/controller-manager`：是运算符的运算符，用于协调 Azure Arc 组件之间的交互
* `deployment.apps/metrics-agent`：收集其他 Arc 代理的指标，以确保这些代理表现出最佳性能
* `deployment.apps/cluster-metadata-operator`：收集分类元数据-群集版本、节点计数和 Azure Arc 代理版本
* `deployment.apps/resource-sync-agent`：将上面提到的群集元数据同步到 Azure
* `deployment.apps/clusteridentityoperator`：启用了 Azure Arc 的 Kubernetes 目前支持系统分配的标识。 clusteridentityoperator 维护其他代理用于与 Azure 进行通信的托管服务标识 (MSI) 证书。
* `deployment.apps/flux-logs-agent`：从源代码管理配置中部署的 flux 运算符收集日志

## <a name="next-steps"></a>后续步骤

* [在连接的群集中使用 GitOps](/azure/azure-arc/kubernetes/use-gitops-connected-cluster)
* [使用 Azure Policy 来控制群集配置](/azure/azure-arc/kubernetes/use-azure-policy)
* [启用启用了 Azure Arc Kubernetes 群集的 Azure Monitor](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters)
