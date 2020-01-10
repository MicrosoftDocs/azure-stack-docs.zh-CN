---
title: 在 Azure Stack Hub 上使用 AKS 引擎部署 Kubernetes 群集 |Microsoft Docs
description: 如何从运行 AKS 引擎的客户端 VM 在 Azure Stack 集线器上部署 Kubernetes 群集。
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
ms.openlocfilehash: da1b38df17904042ade28575f1f919708d845252
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820247"
---
# <a name="deploy-a-kubernetes-cluster-with-the-aks-engine-on-azure-stack-hub"></a>在 Azure Stack 集线器上使用 AKS 引擎部署 Kubernetes 群集

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

可以通过运行 AKS 引擎的客户端 VM 在 Azure Stack 集线器上部署 Kubernetes 群集。 在本文中，我们将介绍如何编写群集规范，如何使用 `apimodel.json` 文件部署群集，以及如何通过使用 Helm 部署 MySQL 来检查群集。

## <a name="define-a-cluster-specification"></a>定义群集规范

您可以使用称为[API 模型](https://github.com/Azure/aks-engine/blob/master/docs/topics/architecture.md#architecture-diagram)的 JSON 格式在文档文件中指定分类规范。 AKS 引擎在 API 模型中使用群集规范来创建群集。 

### <a name="update-the-api-model"></a>更新 API 模型

本部分介绍如何为群集创建 API 模型。

1.  首先使用 Azure Stack 中心[示例](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack)API 模型文件，并为部署创建本地副本。 在计算机上，已安装 AKS 引擎，运行：

    ```bash
    curl -o kubernetes-azurestack.json https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json
    ```

    > [!Note]  
    > 如果你已断开连接，则可以下载该文件，并将其手动复制到你计划对其进行编辑的已断开连接的计算机。 可以使用[PuTTY 或 WinSCP](https://www.suse.com/documentation/opensuse103/opensuse103_startup/data/sec_filetrans_winssh.html)等工具将文件复制到 Linux 计算机。

2.  若要在编辑器中打开，可以使用 nano：

    ```bash
    nano ./kubernetes-azurestack.json
    ```

    > [!Note]  
    > 如果尚未安装 nano，可以在 Ubuntu 上安装 nano： `sudo apt-get install nano`。

3.  在 kubernetes-test-azurestack 文件中，查找 `orchestratorRelease`。 选择一个受支持的 Kubernetes 版本。 例如，1.14、1.15。 这些版本经常会更新。 将版本指定为 "x"，而不是 "xx. x"。 有关当前版本的列表，请参阅[支持的 Kubernetes 版本](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions)。 可以通过运行以下 AKS 引擎命令来查找支持的版本：

    ```bash
    aks-engine get-versions
    ```

4.  查找 `customCloudProfile` 并提供租户门户的 URL。 例如，`https://portal.local.azurestack.external` 。 

5. 如果正在使用 AD FS，请添加 `"identitySystem":"adfs"`。 例如，

    ```JSON  
        "customCloudProfile": {
            "portalURL": "https://portal.local.azurestack.external",
            "identitySystem": "adfs"
        },
    ```

    > [!Note]  
    > 如果对标识系统使用 Azure AD，则无需添加**identitySystem**字段。

6. 查找 `portalURL` 并提供租户门户的 URL。 例如，`https://portal.local.azurestack.external` 。

7.  在数组 `masterProfile`中，设置以下字段：

    | 字段 | Description |
    | --- | --- |
    | dnsPrefix | 输入用于标识 Vm 主机名的唯一字符串。 例如，基于资源组名称的名称。 |
    | count |  输入要用于部署的主机数。 HA 部署的最小值为3，但不允许对非 HA 部署使用1。 |
    | vmSize |  输入[Azure Stack 集线器支持的大小](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)，例如 `Standard_D2_v2`。 |
    | 发行版 | 输入 `aks-ubuntu-16.04`。 |

8.  `agentPoolProfiles` 更新的数组中：

    | 字段 | Description |
    | --- | --- |
    | count | 输入要用于部署的代理数。 |
    | vmSize | 输入[Azure Stack 集线器支持的大小](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes)，例如 `Standard_D2_v2`。 |
    | 发行版 | 输入 `aks-ubuntu-16.04`。 |

9.  `linuxProfile` 更新的数组中：

    | 字段 | Description |
    | --- | --- |
    | adminUsername | 输入 VM 管理员用户名。 |
    | ssh | 输入将用于通过 Vm 进行 SSH 身份验证的公钥。 |

### <a name="more-information-about-the-api-model"></a>有关 API 模型的详细信息

- 有关 API 模型中所有可用选项的完整参考，请参阅[群集定义](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)。  
- 有关 Azure Stack 中心特定选项的突出显示，请参阅[Azure Stack 中心群集定义详细信息](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cluster-definition-aka-api-model)。  

## <a name="deploy-a-kubernetes-cluster"></a>部署 Kubernetes 群集

收集 API 模型中的所有必需值后，便可以创建群集了。 此时，应执行以下操作：

向 Azure Stack 中心操作员询问：

- 验证系统的运行状况，并建议运行 `Test-AzureStack` 和 OEM 供应商的硬件监视工具。
- 验证系统容量，包括内存、存储和公共 Ip 等资源。
- 提供与你的订阅关联的配额的详细信息，以便你可以验证是否有足够的空间来容纳你计划使用的 Vm 数量。

继续部署群集：

1.  查看 Azure Stack 集线器[CLI 标志](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cli-flags)上的 AKS 引擎的可用参数。

    | 参数 | 示例 | Description |
    | --- | --- | --- |
    | azure-env | AzureStackCloud | 若要指示 AKS 引擎 Azure Stack 集线器使用 `AzureStackCloud`的目标平台。 |
    | 标识-系统 | adfs | 可选。 如果使用 Active Directory 联合服务（AD FS），则指定标识管理解决方案。 |
    | location | local | Azure Stack 中心的区域名称。 对于 ASDK，区域设置为 `local`。 |
    | resource-group | kube-rg | 输入新资源组的名称，或者选择现有资源组。 资源名称必须为字母数字，且必须小写。 |
    | api 模型 | ./kubernetes-azurestack.json | 群集配置文件的路径或 API 模型。 |
    | 输出-目录 | kube-rg | 输入要包含输出文件的目录的名称 `apimodel.json` 以及其他生成的文件。 |
    | client-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | 输入服务主体 GUID。 Azure Stack 中心管理员创建服务主体时标识为应用程序 ID 的客户端 ID。 |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | 输入服务主体密码。 这是你在创建服务时设置的客户端密码。 |
    | subscription-id | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | 输入订阅 ID。 有关详细信息，请参阅[订阅产品/服务](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer) |

    下面是一个示例：

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

2.  如果由于某种原因在创建输出目录后执行失败，你可以更正此问题并重新运行该命令。 如果要重新运行部署并在以前使用相同的输出目录，则 AKS 引擎将返回一个错误，指出该目录已存在。 您可以使用标志： `--force-overwrite`覆盖现有目录。

3.  将 AKS 引擎群集配置保存在安全的加密位置。

    找到 `apimodel.json`的文件。 将其保存到安全位置。 此文件将用作所有其他 AKS 引擎操作的输入。

    生成的 `apimodel.json` 包含在输入 API 模型中使用的服务主体、机密和 SSH 公钥。 它还包含 AKS 引擎执行所有其他操作所需的所有其他元数据。 如果丢失，AKS 引擎将无法配置群集。

    密码**未加密**。 将该文件保存在安全的安全位置。 

## <a name="verify-your-cluster"></a>验证群集

通过使用 Helm 部署 mysql 检查群集来验证群集。

1. 使用 Azure Stack 集线器门户获取其中一个主节点的公共 IP 地址。

2. 在可访问 Azure Stack 中心实例的计算机上，使用客户端（如 PuTTY 或 MobaXterm）通过 SSH 连接到新的主节点。 

3. 对于 SSH 用户名，你可以使用 "azureuser" 和你为群集部署提供的密钥对的私钥文件。

4.  运行以下命令：

    ```bash
    sudo snap install helm --classic
    kubectl -n kube-system create serviceaccount tiller
    kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller
    helm init --service-account=tiller
    helm repo update
    helm install stable/mysql
    ```

5.  若要清理测试，请查找用于 mysql 部署的名称。 在下面的示例中，名称为 `wintering-rodent`。 然后将其删除。 

    运行以下命令：

    ```bash
    helm ls
    NAME REVISION UPDATED STATUS CHART APP VERSION NAMESPACE
    wintering-rodent 1 Thu Oct 18 15:06:58 2018 DEPLOYED mysql-0.10.1 5.7.14 default
    helm delete wintering-rodent
    ```

    CLI 将显示：
    ```bash
    release "wintering-rodent" deleted
    ```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [对 Azure Stack 集线器上的 AKS 引擎进行故障排除](azure-stack-kubernetes-aks-engine-troubleshoot.md)