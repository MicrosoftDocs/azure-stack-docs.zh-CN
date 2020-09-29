---
title: 排查 Azure Stack Hub 上的 AKS 引擎问题
description: 本文包含针对 Azure Stack Hub 上 AKS 引擎的故障排除步骤。
author: mattbriggs
ms.topic: article
ms.date: 09/08/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/08/2020
ms.openlocfilehash: e9e1e09d40be623dfb973503295274790a86dfb8
ms.sourcegitcommit: 2407498dc34158a49959d9f87f84d6a1cde0cca6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89560957"
---
# <a name="troubleshoot-the-aks-engine-on-azure-stack-hub"></a>排查 Azure Stack Hub 上的 AKS 引擎问题

在 Azure Stack Hub 上部署或使用 AKS 引擎时，可能会遇到问题。 本文介绍了对 AKS 引擎的部署进行故障排除、收集 AKS 引擎的信息、收集 Kubernetes 日志、查看自定义脚本扩展错误代码的步骤以及有关为 AKS 引擎提交 GitHub 问题的说明。

## <a name="troubleshoot-the-aks-engine-install"></a>排查 AKS 引擎安装问题

### <a name="try-gofish"></a>试用 GoFish

如果之前的安装步骤失败，可以使用 GoFish 包管理器安装 AKS 引擎。 [GoFish](https://gofi.sh) 将自己定义为跨平台的 Homebrew。

#### <a name="install-the-aks-engine-with-gofish-on-linux"></a>在 Linux 上使用 GoFish 安装 AKS 引擎

通过[安装](https://gofi.sh/#install)页安装 GoFish。

1. 在 bash 提示符下，运行以下命令：

    ```bash
    curl -fsSL https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.sh | bash
    ```

2.  运行以下命令以使用 GoFish 安装 AKS 引擎：

    ```bash
    Run "gofish install aks-engine"
    ```

#### <a name="install-the-aks-engine-with-gofish-on-windows"></a>在 Windows 上使用 GoFish 安装 AKS 引擎

通过[安装](https://gofi.sh/#install)页安装 GoFish。

1. 在权限提升的 PowerShell 提示符下，运行以下命令：

    ```PowerShell
    Set-ExecutionPolicy Bypass -Scope Process -Force
    iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.ps1'))
    ```

2.  在同一会话中运行以下命令以使用 GoFish 安装 AKS 引擎：

    ```PowerShell
    gofish install aks-engine
    ```

### <a name="checklist-for-common-deployment-issues"></a>常见部署问题的清单

当使用 AKS 引擎部署 Kubernetes 群集时，如遇错误，可以检查：

1.  是否使用了正确的服务主体凭据 (SPN)？
2.  SPN 是否具有对 Azure Stack Hub 订阅的“参与者”角色？
3. Azure Stack Hub 计划中是否有足够大的配额？
4.  Azure Stack Hub 实例是否正在应用修补程序或进行升级？

有关详细信息，请参阅 Azure/aks-engine GitHub 存储库中的[故障排除](https://github.com/Azure/aks-engine/blob/master/docs/howto/troubleshooting.md)一文****。

## <a name="collect-aks-engine-logs"></a>收集 AKS 引擎日志

您可以查看 AKS 引擎创建的信息。 当应用程序运行时，AKS 引擎会报告状态和错误。 可以通过管道将输出传送到文本文件，也可以直接从命令行控制台复制输出。 请参阅[查看自定义脚本扩展错误代码](#review-custom-script-extension-error-codes)，查看 AKS 引擎触发的错误代码的列表。

1.  从 AKS 引擎命令行工具中显示的信息中收集标准输出和错误信息。

2. 从本地文件获取日志。 可以 `get-logs` 通过设置 **--output-directory** 标志，使用命令来设置输出目录。

    设置日志的本地路径：

    ```bash  
    aks-engine get-logs --output-directory <path to the directory>
    ```

## <a name="collect-kubernetes-logs"></a>收集 Kubernetes 日志

此外，对于 AKS 引擎日志，Kubernetes 组件会生成状态和错误消息。 可使用 Bash 脚本 [getkuberneteslogs.sh](https://github.com/msazurestackworkloads/azurestack-gallery/releases/tag/diagnosis-v0.1.3) 收集这些日志。

此脚本可实现自动收集以下日志： 

 - Microsoft Azure Linux 代理 (waagent) 日志
 - 自定义脚本扩展日志
 - 运行 kube-system 容器元数据
 - 运行 kube-system 容器日志
 - Kubelet 服务状态和日记
 - Etcd 服务状态和日记
 - 库项的 DVM 日志
 - kube-system 快照

如果没有此脚本，需要连接到群集中的每个节点，手动查找并下载日志。 此外，该脚本还可以（可选）将收集的日志上传到存储帐户，通过该帐户与其他人共享日志。

要求：

 - Windows 上的 Linux VM、Git Bash 或 Bash。
 - [Azure CLI](azure-stack-version-profiles-azurecli2.md) 安装在运行脚本的计算机上。
 - 服务主体标识已登录到 Azure Stack Hub 的 Azure CLI 会话。 由于脚本能够发现和创建 Azure Stack 资源管理器资源来完成其工作，因此它需要 Azure CLI 和服务主体标识。
 - 用户帐户（订阅），且已在环境中选择了 Kubernetes 群集。 
1. 将最新版本的脚本 tar 文件下载到客户端 VM，该 VM 应是有权访问 Kubernetes 群集的计算机，或是使用 AKS 引擎部署群集时使用的同一计算机。

    运行以下命令：

    ```bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    wget https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/diagnosis-v0.1.1/diagnosis-v0.1.1.tar.gz
    tar xvf diagnosis-v0.1.1.tar.gz -C ./
    ```

2. 查找 `getkuberneteslogs.sh` 脚本所需的参数。 此脚本将使用以下参数：

    | 参数 | 说明 | 必须 | 示例 |
    | --- | --- | --- | --- |
    | -h、--help | 打印命令用法。 | 否 | 
    -u、--user | 群集 VM 的管理员用户名 | 是 | azureuser<br>（默认值） |
    | -i、--identity-file | 与用于创建 Kubernetes 群集的公钥绑定的 RSA 私钥（有时称为“id_rsa”）  | 是 | `./rsa.pem` (Putty)<br>`~/.ssh/id_rsa` (SSH) |
    |   -g, --resource-group    | Kubernetes 群集资源组 | 是 | k8sresourcegroup |
    |   -n、--user-namespace               | 从指定的命名空间中的容器中收集日志（始终收集 kube-system 日志） | 否 |   monitoring |
    |       --api-model                    | 将 apimodel.json 文件保存在 Azure Stack Hub 存储帐户中。 如果也提供了“--upload-logs”参数，则会将 apimodel.json 文件上传到存储帐户。 | 否 | `./apimodel.json` |
    | --all-namespaces               | 从所有命名空间中的容器中收集日志。 It overrides --user-namespace | 否 | |
    | --upload-logs                  | 将检索的日志保存在 Azure Stack Hub 存储帐户中。 可在 KubernetesLogs 资源组中找到日志 | 否 | |
    --disable-host-key-checking    | 在执行脚本时将 SSH 的 StrictHostKeyChecking 选项设置为“否”。 仅在安全环境中使用。 | 否 | |

3. 使用你的信息运行以下任何示例命令：

    ```bash
    ./getkuberneteslogs.sh -u azureuser -i private.key.1.pem -g k8s-rg
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --disable-host-key-checking
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg -n default -n monitoring
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs --api-model clusterDefinition.json
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs
    ```

## <a name="review-custom-script-extension-error-codes"></a>查看自定义脚本扩展错误代码

在运行群集时，可以查看由自定义脚本扩展 (CSE) 创建的错误代码列表。 CSE 错误可帮助诊断问题的根本原因。 Kubernetes 群集中使用的 Ubuntu 服务器的 CSE 支持许多 AKS 引擎操作。 有关 CSE 退出代码的详细信息，请参阅 [cse_helpers.sh](https://github.com/Azure/aks-engine/blob/master/pkg/engine/cse.go)。

### <a name="providing-kubernetes-logs-to-a-microsoft-support-engineer"></a>向 Microsoft 支持工程师提供 Kubernetes 日志

如果在收集和检查日志后仍无法解决问题，则可能需要启动创建支持票证的过程，并提供通过使用 `--upload-logs` 参数集运行 `getkuberneteslogs.sh` 后所收集的日志。 

请联系 Azure Stack Hub 操作员。 操作员使用日志中的信息创建支持案例。

在解决任何支持问题的过程中，Microsoft 支持工程师可能会要求 Azure Stack 中心操作员收集 Azure Stack 中心系统日志。 可能需要向操作员提供运行 `getkuberneteslogs.sh` 来上传 Kubernetes 日志时所用的存储帐户的信息。

操作员可能运行 Get-AzureStackLog PowerShell cmdlet****。 此命令使用参数 (`-InputSaSUri`) 来指定存储 Kubernetes 日志的存储帐户。

操作员可以将生成的日志与 Microsoft 支持人员可能需要的任何其他系统日志合并，并将其提供给 Microsoft。

## <a name="open-github-issues"></a>提交 GitHub 问题

如果无法解决部署错误，可以提交 GitHub 问题。 

1. 在 AKS 引擎存储库中提交 [GitHub 问题](https://github.com/Azure/aks-engine/issues/new)。
2. 使用以下格式添加标题：C`SE error: exit code <INSERT_YOUR_EXIT_CODE>`。
3. 在问题中包含以下信息：

    - 用于部署群集的群集配置文件 `apimodel json`。 在将其发布到 GitHub 之前，请删除所有机密和密钥。  
     - 以下 kubectl 命令 `get nodes` 的输出****。  
     - `/var/log/azure/cluster-provision.log` 和 `/var/log/cloud-init-output.log` 的内容

## <a name="next-steps"></a>后续步骤

- 了解 [Azure Stack Hub 上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)
