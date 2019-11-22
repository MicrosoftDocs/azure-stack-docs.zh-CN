---
title: 升级 Azure Stack 上的 Kubernetes 群集 |Microsoft Docs
description: 了解如何升级 Azure Stack 上的 Kubernetes 群集。
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
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: c7db293cb1a24079f66dcf0b047cf66218857003
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310129"
---
# <a name="upgrade-a-kubernetes-cluster-on-azure-stack"></a>升级 Azure Stack 上的 Kubernetes 群集

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

## <a name="upgrade-a-cluster"></a>升级群集

使用 AKS 引擎，可以升级最初使用工具部署的群集。 您可以使用 AKS 引擎来维护群集。 维护任务类似于任何 IaaS 系统。 你应了解新更新的可用性，并使用 AKS 引擎来应用这些更新。

升级命令将更新 Kubernetes 版本和基本操作系统映像。 每次运行升级命令时，对于群集的每个节点，AKS 引擎都将使用与所使用的**AKS**版本关联的 AKS 基础映像创建新的 VM。 您可以使用 `aks-engine upgrade` 命令来维护群集中每个主节点和代理节点的货币。 

Microsoft 不管理群集。 但 Microsoft 提供了可用于管理群集的工具和 VM 映像。 

对于已部署的群集升级，涵盖：

-   Kubernetes
-   Azure Stack Kubernetes 提供程序
-   基本操作系统

升级生产群集时，请考虑以下事项：

-   是否对目标群集使用正确的群集规范（`apimodel.json`）和资源组？
-   是否正在为客户端计算机使用可靠的计算机来运行 AKS 引擎以及要从其执行升级操作的计算机？
-   请确保你有备份群集并且它可操作。
-   如果可能，请从 Azure Stack 环境内的 VM 运行该命令，以减少网络跃点和可能的连接故障。
-   请确保你的订阅具有足够的空间来完成整个过程。 进程将在此过程中分配新的 Vm。
-   未计划系统更新或计划任务。
-   在配置与生产群集完全相同的群集上设置分步升级，并在生产群集中进行升级之前测试升级

## <a name="steps-to-upgrade-to-a-newer-kubernetes-version"></a>升级到较新的 Kubernetes 版本的步骤

> [!Note]  
> 如果你使用的是较新版本的 AKS 并在 marketplace 中提供映像，则也会升级 AKS 基本映像。

以下说明使用执行升级的最少步骤。 如需更多详细信息，请参阅[升级 Kubernetes 群集](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md)一文。

1. 需要首先确定可用于升级的版本。 此版本依赖于当前拥有的版本，然后使用该版本值执行升级。

    运行以下命令：

    ```bash  
    $ aks-engine get-versions
    Version Upgrades
    1.15.0
    1.14.3  1.15.0
    1.14.1  1.14.3, 1.15.0
    1.13.7  1.14.1, 1.14.3
    1.13.5  1.13.7, 1.14.1, 1.14.3
    1.12.8  1.13.5, 1.13.7
    1.12.7  1.12.8, 1.13.5, 1.13.7
    1.11.10 1.12.7, 1.12.8
    1.11.9  1.11.10, 1.12.7, 1.12.8
    1.10.13 1.11.9, 1.11.10
    1.10.12 1.10.13, 1.11.9, 1.11.10
    1.9.11  1.10.12, 1.10.13
    1.9.10  1.9.11, 1.10.12, 1.10.13
    1.6.9   1.9.10, 1.9.11
    ```

    例如，根据 `get-versions` 命令的输出，如果当前 Kubernetes 版本是 "1.13.5"，则可以升级到 "1.13.7，1.14.1，1.14.3"。

2. 收集运行 `upgrade` 命令所需的信息。 升级使用以下参数：

    | 参数 | 示例 | 说明 |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | 若要指示 AKS 引擎 Azure Stack 目标平台使用 `AzureStackCloud`。 |
    | location | local | Azure Stack 的区域名称。 对于 ASDK，区域设置为 `local`。 |
    | resource-group | kube-rg | 输入新资源组的名称，或者选择现有资源组。 资源名称必须为字母数字，且必须小写。 |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | 输入订阅 ID。 有关详细信息，请参阅[订阅产品/服务](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer) |
    | api 模型 | ./kubernetes-azurestack.json | 群集配置文件的路径或 API 模型。 |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | 输入服务主体 GUID。 Azure Stack 管理员创建服务主体时标识为应用程序 ID 的客户端 ID。 |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | 输入服务主体密码。 这是你在创建服务时设置的客户端密码。 |
    | 标识-系统 | adfs | 可选。 如果使用 Active Directory 联合服务（AD FS），则指定标识管理解决方案。 |

3. 准备好值后，运行以下命令：

    ```bash  
    aks-engine upgrade \
    --azure-env AzureStackCloud   
    --location <for an ASDK is local> \
    --resource-group kube-rg \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model kube-rg/apimodel.json \
    --upgrade-version 1.13.5 \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

4.  如果由于任何原因升级操作遇到故障，你可以在解决问题后重新运行升级命令。 AKS 引擎将恢复之前发生故障的操作。

## <a name="steps-to-only-upgrade-the-os-image"></a>仅升级 OS 映像的步骤

1. 查看[支持的 kubernetes 版本表](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions)，确定你是否具有计划升级所需的 AKS 和 Aks 基本映像版本。 查看 aks 运行的版本： `aks-engine version`。
2. 相应地升级 AKS 引擎，在安装了 AKS 的计算机上运行： `./get-akse.sh --version vx.xx.x` 将**替换为目标版本。**
3. 询问 Azure Stack 操作员在你计划使用的 Azure Stack Marketplace 中添加所需的 AKS 基本映像版本。
4. 使用已在使用的 Kubernetes 的相同版本运行 `aks-engine upgrade` 命令，但添加 `--force`。 你可以查看[强制升级](#forcing-an-upgrade)的示例。


## <a name="forcing-an-upgrade"></a>强制升级

在某些情况下，可能需要强制实施群集升级。 例如，在第一天，使用最新的 Kubernetes 版本在断开连接的环境中部署群集。 以下第一天的 Ubuntu 发布了一个修补程序，该漏洞适用于 Microsoft 生成新的**AKS 基本映像**。 可以通过使用已部署的相同 Kubernetes 版本强制升级，来应用新映像。

```bash  
aks-engine upgrade \
--azure-env AzureStackCloud   
--location <for an ASDK is local> \
--resource-group kube-rg \
--subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--api-model kube-rg/apimodel.json \
--upgrade-version 1.13.5 \
--client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--force
```

有关说明，请参阅[强制升级](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md#force-upgrade)。

## <a name="next-steps"></a>后续步骤

- 了解[Azure Stack 上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)
- [缩放 Azure Stack 上的 Kubernetes 群集](azure-stack-kubernetes-aks-engine-scale.md)