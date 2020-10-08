---
title: 在 Azure Stack 集线器上通过 AKS 引擎部署 Kubernetes 群集
description: 如何从运行 AKS 引擎的客户端 VM 中将 Kubernetes 群集部署到 Azure Stack Hub 上。
author: mattbriggs
ms.topic: article
ms.date: 09/02/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: b90b7c61e5eeed1265bf258b6ba3ce7b042b6897
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2020
ms.locfileid: "91853187"
---
# <a name="deploy-a-kubernetes-cluster-with-the-aks-engine-on-azure-stack-hub"></a>使用 AKS 引擎在 Azure Stack Hub 上部署 Kubernetes 群集

可以从运行 AKS 引擎的客户端 VM 中，在 Azure Stack Hub 上部署 Kubernetes 群集。 本文介绍如何编写群集规范，如何使用 `apimodel.json` 文件部署群集，以及如何使用 Helm 部署 MySQL 来检查群集。

## <a name="define-a-cluster-specification"></a>定义群集规范

可使用称为 [API 模型](https://github.com/Azure/aks-engine/blob/master/docs/topics/architecture.md#architecture-diagram)的 JSON 格式在文档文件中指定群集规范。 AKS 引擎在 API 模型中使用群集规范来创建群集。 

### <a name="update-the-api-model"></a>更新 API 模型

本部分介绍如何为群集创建 API 模型。

1.  首先使用 Azure Stack Hub [示例](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack) API 模型文件，为部署创建本地副本。 在安装 AKS 引擎的计算机上运行：

    ```bash
    curl -o kubernetes-azurestack.json https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json
    ```

    > [!NOTE]  
    > 如果已断开连接，可以下载该文件，并将其手动复制到计划在其上编辑文件的已断开连接的计算机。 可以使用 [PuTTY 或 WinSCP](https://www.suse.com/documentation/opensuse103/opensuse103_startup/data/sec_filetrans_winssh.html) 等工具将文件复制到 Linux 计算机。

2.  若要在编辑器中打开 API 模型，可以使用 nano：

    ```bash
    nano ./kubernetes-azurestack.json
    ```

    > [!NOTE]  
    > 如果尚未安装 nano，可在 Ubuntu 上安装 nano：`sudo apt-get install nano`。

3.  在 kubernetes-azurestack.json 文件中找到 orchestratorRelease 和 orchestratorVersion。 选择一个受支持的 Kubernetes 版本。 例如，对 `orchestratorRelease` 使用 1.14 或 1.15，对 `orchestratorVersion` 使用 1.14.7 或 1.15.10。 将 `orchestratorRelease` 指定为 x.xx，将 orchestratorVersion 指定为 x.xx.x。 有关当前版本的列表，请参阅[受支持的 AKS 引擎版本](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions)

4.  找到 `customCloudProfile` 并提供租户门户的 URL。 例如，`https://portal.local.azurestack.external`。 

5. 如果使用的是 AD FS，请添加 `"identitySystem":"adfs"`。 例如，

    ```JSON  
        "customCloudProfile": {
            "portalURL": "https://portal.local.azurestack.external",
            "identitySystem": "adfs"
        },
    ```

    > [!NOTE]  
    > 如果为标识系统使用 Azure AD，则无需添加 identitySystem 字段****。

6. 找到 `portalURL` 并提供租户门户的 URL。 例如，`https://portal.local.azurestack.external`。

7.  在 `masterProfile` 中设置以下字段：

    | 字段 | 说明 |
    | --- | --- |
    | dnsPrefix | 输入用于标识 VM 主机名的唯一字符串。 例如基于资源组名称的名称。 |
    | count |  输入要用于部署的主机数。 HA 部署的最小使用数为 3，非 HA 部署的最小使用数可为 1。 |
    | vmSize |  输入 [Azure Stack Hub 支持的大小](./azure-stack-vm-sizes.md)，例如 `Standard_D2_v2`。 |
    | distro | 输入 `aks-ubuntu-16.04`。 |

8.  在 `agentPoolProfiles` 更新中：

    | 字段 | 说明 |
    | --- | --- |
    | count | 输入要用于部署的代理数。 每个订阅使用的节点的最大数目为 50 个。 如果要为每个订阅部署多个群集，请确保代理总数不超过 50 个。 请确保使用[示例 API 模型 JSON 文件](https://github.com/Azure/aks-engine/blob/master/examples/azure-stack/kubernetes-azurestack.json)中指定的配置项目。  |
    | vmSize | 输入 [Azure Stack Hub 支持的大小](./azure-stack-vm-sizes.md)，例如 `Standard_D2_v2`。 |
    | distro | 输入 `aks-ubuntu-16.04`。 |




9.  在 `linuxProfile` 更新中：

    | 字段 | 说明 |
    | --- | --- |
    | adminUsername | 输入 VM 管理员用户名。 |
    | ssh | 输入将用于 VM 的 SSH 身份验证的公钥。 依次使用 `ssh-rsa` 和密钥。 有关创建公钥的说明，请参阅[为 Linux 创建 SSH 密钥](create-ssh-key-on-windows.md)。 |

    如果要部署到自定义虚拟网络，可在[将 Kubernetes 群集部署到自定义虚拟网络](kubernetes-aks-engine-custom-vnet.md)中找到有关查找必需的密钥和值并将其添加到 API 模型中适当数组中的说明。

    > [!NOTE]  
    > Azure Stack Hub 的 AKS 引擎不允许你提供自己的证书来创建群集。

### <a name="more-information-about-the-api-model"></a>有关 API 模型的详细信息

- 有关 API 模型中所有可用选项的完整参考信息，请参阅[群集定义](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)。  
- 有关特定于 Azure Stack Hub 的选项的重点内容，请参阅 [Azure Stack Hub 群集定义详细信息](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cluster-definition-aka-api-model)。  

## <a name="deploy-a-kubernetes-cluster"></a>部署 Kubernetes 群集

收集 API 模型中的所有必需值后，便可以创建群集。 此时应该：

要求 Azure Stack Hub 操作员：

- 验证系统的运行状况，建议运行 `Test-AzureStack` 和 OEM 供应商的硬件监视工具。
- 验证系统容量，包括内存、存储和公共 IP 等资源。
- 提供与你的订阅关联的配额的详细信息，以便验证是否有足够的空间来容纳你计划使用的 VM 数量。

继续部署群集：

1.  查看 Azure Stack Hub [CLI 标志](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cli-flags)上 AKS 引擎的可用参数。

    | 参数 | 示例 | 说明 |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | 若要向 AKS 引擎指示目标平台是 Azure Stack Hub，请使用 `AzureStackCloud`。 |
    | identity-system | adfs | 可选。 如果使用 Active Directory 联合身份验证服务 (AD FS)，请指定标识管理解决方案。 |
    | location | local | Azure Stack Hub 的区域名称。 对于 ASDK，此区域设置为 `local`。 |
    | resource-group | kube-rg | 输入新资源组的名称，或者选择现有资源组。 资源名称必须为字母数字，且必须小写。 |
    | api-model | ./kubernetes-azurestack.json | 群集配置文件的路径或 API 模型。 |
    | output-directory | kube-rg | 输入要包含输出文件 `apimodel.json` 以及其他生成的文件的目录名称。 |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | 输入服务主体 GUID。 Azure Stack Hub 管理员创建服务主体时标识为应用程序 ID 的客户端 ID。 |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | 输入服务主体密码。 在创建服务时设置的客户端密码。 |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | 输入订阅 ID。 必须提供租户的订阅。 不支持部署到管理订阅。  有关详细信息，请参阅[订阅套餐](./azure-stack-subscribe-services.md#subscribe-to-an-offer) |

    以下是示例：

    ```bash  
    aks-engine deploy \
    --azure-env AzureStackCloud \
    --location <for asdk is local> \
    --resource-group kube-rg \
    --api-model ./kubernetes-azurestack.json \
    --output-directory kube-rg \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

2.  如果由于某种原因，输出目录在创建后执行失败，可以更正此问题并重新运行该命令。 如果正在重新运行部署并在之前已经使用了相同的输出目录，则 AKS 引擎将返回一个错误，指出该目录已存在。 可使用标志 `--force-overwrite` 覆盖现有目录。

3.  将 AKS 引擎群集配置保存在安全、已加密的位置。

    找到文件 `apimodel.json`。 将其保存到安全位置。 此文件将用作所有其他 AKS 引擎操作的输入。

    生成的 `apimodel.json` 包含“输入 API 模型”中使用的服务主体、密码和 SSH 公钥。 它还包含 AKS 引擎执行所有其他操作所需的所有其他元数据。 如果丢失，AKS 引擎将无法配置群集。

    机密未加密****。 将该文件保存在已加密的安全位置。 

## <a name="verify-your-cluster"></a>验证群集

使用 Helm 部署 MySql 来检查群集，从而验证群集。

1. 使用 Azure Stack Hub 门户获取其中一个主节点的公共 IP 地址。

2. 在可访问 Azure Stack Hub 实例的计算机上，使用客户端（如 PuTTY 或 MobaXterm）通过 SSH 连接到新的主节点。 

3. 对于 SSH 用户名，可使用“azureuser”和为群集部署提供的密钥对的私钥文件。

4. 运行以下命令以创建 Redis master 的示例部署（仅适用于连接的标记）：

   ```bash
   kubectl apply -f https://k8s.io/examples/application/guestbook/redis-master-deployment.yaml
   ```

    1. 查询 Pod 列表：

       ```bash
       kubectl get pods
       ```

    2. 响应应类似于以下形式：

       ```shell
       NAME                            READY     STATUS    RESTARTS   AGE
       redis-master-1068406935-3lswp   1/1       Running   0          28s
       ```

    3. 查看部署日志：

       ```shell
       kubectl logs -f <pod name>
       ```

    对于包含 Redis master 的示例 PHP 应用的完整部署，请按照[此处说明](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/)操作。

5. 对于已断开连接的标记，使用以下命令即可：

    1. 首先检查群集终结点是否正在运行：

       ```bash
       kubectl cluster-info
       ```

       输出应如下所示：

       ```shell
       Kubernetes master is running at https://democluster01.location.domain.com
       CoreDNS is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
       kubernetes-dashboard is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy
       Metrics-server is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy
       ```

    2. 然后查看节点状态：

       ```bash
       kubectl get nodes
       ```

       输出应如下所示：

       ```shell
       k8s-linuxpool-29969128-0   Ready      agent    9d    v1.15.5
       k8s-linuxpool-29969128-1   Ready      agent    9d    v1.15.5
       k8s-linuxpool-29969128-2   Ready      agent    9d    v1.15.5
       k8s-master-29969128-0      Ready      master   9d    v1.15.5
       k8s-master-29969128-1      Ready      master   9d    v1.15.5
       k8s-master-29969128-2      Ready      master   9d    v1.15.5
       ```

6. 若要清理上一步中的 redis POD 部署，请运行以下命令：

    ```bash
    kubectl delete deployment -l app=redis
    ```

## <a name="rotate-your-service-principle-secret"></a>轮换服务主体机密

使用 AKS 引擎部署 Kubernetes 群集后，服务主体 (SPN) 用于管理与 Azure Stack 中心实例上的 Azure 资源管理器的交互。 在某些时候，此服务主体的机密可能会过期。 如果你的机密过期，你可以通过以下方式刷新凭据：

- 正在用新服务主体机密更新每个节点。
- 或更新 API 模型凭据并运行升级。

### <a name="update-each-node-manually"></a>手动更新每个节点

1. 从云操作员获取服务主体的新机密。 有关 Azure Stack 中心的说明，请参阅 [使用应用标识访问 Azure Stack 中心资源](/azure-stack/operator/azure-stack-create-service-principals)。
2. 使用云操作员提供的新凭据 `/etc/kubernetes/azure.json` 在每个节点上进行更新。 进行更新后，重新启动 **kubelet** 和 **kube**。

### <a name="update-the-cluster-with-aks-engine-update"></a>用 aks 更新更新群集

或者，你可以替换中的凭据 `apimodel.json` ，并使用更新的 json 将升级运行到相同或较新的 Kubernetes 版本。 有关升级模型的说明，请参阅 [在 Azure Stack 集线器上升级 Kubernetes 群集](azure-stack-kubernetes-aks-engine-upgrade.md)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [排查 Azure Stack Hub 上的 AKS 引擎问题](azure-stack-kubernetes-aks-engine-troubleshoot.md)
