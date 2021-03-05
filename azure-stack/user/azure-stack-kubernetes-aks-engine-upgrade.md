---
title: 升级 Azure Stack Hub 上的 Kubernetes 群集
description: 了解如何升级 Azure Stack Hub 上的 Kubernetes 群集。
author: mattbriggs
ms.topic: article
ms.date: 3/4/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/4/2021
ms.openlocfilehash: 0db454750b56e9c4dbb765092c48643b1df15470
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231551"
---
# <a name="upgrade-a-kubernetes-cluster-on-azure-stack-hub"></a>升级 Azure Stack Hub 上的 Kubernetes 群集

使用 AKS 引擎可升级最初使用工具部署的群集。 可使用 AKS 引擎维护群集。 维护任务类似于任何 IaaS 系统。 需要注意新更新的可用性并使用 AKS 引擎应用这些更新。
## <a name="upgrade-a-cluster"></a>升级群集

升级命令将更新 Kubernetes 版本和基本 OS 映像。 每次运行升级命令时，AKS 引擎会使用与所使用的 aks-engine 版本相关联的 AKS 基础映像，为群集的每个节点创建一个新 VM。 可以使用 `aks-engine upgrade` 命令来维护群集中每个主节点和代理节点的货币。 

Microsoft 不管理群集。 但 Microsoft 提供了可用于管理群集的工具和 VM 映像。 

对于已部署的群集，升级包括：

-   kubernetes
-   Azure Stack Hub Kubernetes 提供程序
-   基本 OS

升级生产群集时，请考虑以下事项：

-   是否为目标群集使用了正确的群集规范 (`apimodel.json`) 和资源组？
-   是否为客户端计算机使用了可靠的计算机来运行 AKS 引擎并从中执行升级操作？
-   请确保拥有可操作的备份群集。
-   如果可能，请从 Azure Stack Hub 环境中的 VM 运行命令，以减少网络跃点和潜在的连接故障。
-   请确保订阅有足够的空间来完成整个过程。 相应进程将在此过程中分配新的 VM。
-   未规划系统更新或计划的任务。
-   在精确配置为生产群集的群集上，设置分阶段升级，并在生产群集中执行此操作之前，在该群集上测试升级

## <a name="steps-to-upgrade-to-a-newer-kubernetes-version"></a>升级到更新的 Kubernetes 版本的步骤

> [!NOTE]  
> 如果使用的是较新版本的 aks-engine 且应用市场提供映像，也将升级 AKS 基础映像。

下面的说明使用最少的步骤来执行升级。 如需更多详细信息，请参阅 [升级 Kubernetes 群集](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping)一文。

1. 需要首先确定可用于升级的版本。 此版本取决于当前拥有的版本，然后使用该版本值执行升级。 可以通过运行以下命令列出 AKS 引擎支持的 Kubernetes 版本：
    
    ```bash
    aks-engine get-versions --azure-env AzureStackCloud
    ```
    
    有关 AKS 引擎、AKS 基础映像和 Kubernetes 版本的完整映射，请参阅[受支持的 AKS 引擎版本](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping)。

2. 收集运行 `upgrade` 命令所需的信息。 该升级使用以下参数：

    | 参数 | 示例 | 说明 |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | 若要向 AKS 引擎指示目标平台是 Azure Stack Hub，请使用 `AzureStackCloud`。 |
    | location | local | Azure Stack Hub 的区域名称。 对于 ASDK，此区域设置为 `local`。 |
    | resource-group | kube-rg | 输入新资源组的名称，或者选择现有资源组。 资源名称必须为字母数字，且必须小写。 |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | 输入订阅 ID。 有关详细信息，请参阅[订阅套餐](./azure-stack-subscribe-services.md#subscribe-to-an-offer) |
    | api-model | ./kubernetes-azurestack.json | 群集配置文件的路径或 API 模型。 |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | 输入服务主体 GUID。 Azure Stack Hub 管理员创建服务主体时标识为应用程序 ID 的客户端 ID。 |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | 输入服务主体密码。 这是在创建服务时设置的客户端机密。 |
    | identity-system | adfs | 可选。 如果使用 Active Directory 联合身份验证服务 (AD FS)，请指定标识管理解决方案。 |

3. 准备好值后，请运行以下命令：

    ```bash  
    aks-engine upgrade \
    --azure-env AzureStackCloud \
    --location <for an ASDK is local> \
    --resource-group kube-rg \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model kube-rg/apimodel.json \
    --upgrade-version 1.18.15 \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

4.  如果由于任何原因升级操作遇到故障，可以在解决问题后重新运行升级命令。 AKS 引擎将继续从之前失败的操作开始运行。

## <a name="steps-to-only-upgrade-the-os-image"></a>仅升级 OS 映像的步骤

1. 查看 [supported-kubernetes-versions 表](kubernetes-aks-engine-release-notes.md#aks-engine-and-azure-stack-version-mapping)并确定是否有升级所需的 aks-engine 和 AKS 基础映像版本。 查看 aks-engine 运行的版本：`aks-engine version`。
2. 相应地升级 AKS 引擎，在安装了 aks-engine 的计算机中运行：`./get-akse.sh --version vx.xx.x`，并将 x.xx.x 替换为目标版本。
3. 要求 Azure Stack Hub 操作员在 Azure Stack Hub 市场中添加所需的计划使用的 AKS 基础映像版本。
4. 使用正在使用的 Kubernetes 版本运行 `aks-engine upgrade` 命令，但添加 `--force`。 可在[强制升级](#forcing-an-upgrade)中查看示例。


## <a name="steps-to-update-cluster-to-os-version-ubuntu-1804"></a>将群集更新到操作系统版本 Ubuntu 18.04 的步骤

借助 AKS 引擎版本0.60.1 和更高版本，你可以将群集 Vm 从 Ubuntu 16.04 升级到18.04。 执行以下步骤:

1. 查找并编辑 `api-model.json` 部署过程中生成的文件。 这应该是用于进行任何升级或缩放操作的同一文件 `aks-engine` 。
2. 找到和的各个 `masterProfile` 部分 `agentPoolProfiles` ，在这些部分中，将的值更改 `distro` 为 `aks-ubuntu-18.04` 。
2. 保存该 `api-model.json` 文件并在命令中使用该文件，就 `api-model.json` ` aks-engin upgrade` 像[升级到较新的 Kubernetes 版本的步骤](#steps-to-upgrade-to-a-newer-kubernetes-version)所述。

## <a name="forcing-an-upgrade"></a>强制升级

在某些情况下，可能需要强制实施群集升级。 例如，在第一天，你使用最新 Kubernetes 版本在断开连接的环境中部署群集。 第二天，Ubuntu 发布了一个漏洞修补程序，Microsoft 为其生成了新的 AKS 基础映像。 这时就可以使用已部署的 Kubernetes 版本强制进行升级来应用新映像。

```bash  
aks-engine upgrade \
--azure-env AzureStackCloud   
--location <for an ASDK is local> \
--resource-group kube-rg \
--subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--api-model kube-rg/apimodel.json \
--upgrade-version 1.18.15 \
--client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--force
```

有关说明，请参阅[强制升级](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md#force-upgrade)。

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Stack Hub 上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)
- [缩放 Azure Stack Hub 上的 Kubernetes 群集](azure-stack-kubernetes-aks-engine-scale.md)
