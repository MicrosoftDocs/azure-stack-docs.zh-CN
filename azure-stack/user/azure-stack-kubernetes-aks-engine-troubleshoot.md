---
title: 对 Azure Stack 集线器上的 AKS 引擎进行故障排除
description: 本文包含 Azure Stack 集线器上的 AKS 引擎的故障排除步骤。
author: mattbriggs
ms.topic: article
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: aa41ddde0986716e49073d571e967a050ef660f6
ms.sourcegitcommit: 4301e8dee16b4db32b392f5979dfec01ab6566c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79313003"
---
# <a name="troubleshoot-the-aks-engine-on-azure-stack-hub"></a>对 Azure Stack 集线器上的 AKS 引擎进行故障排除

在 Azure Stack 集线器上部署或使用 AKS 引擎时，可能会遇到问题。 本文介绍了对 AKS 引擎的部署进行故障排除的步骤，收集有关 AKS 引擎的信息，收集 Kubernetes 日志，查看自定义脚本扩展错误代码，以及针对 AKS 引擎打开 GitHub 问题的说明。

## <a name="troubleshoot-the-aks-engine-install"></a>排查 AKS 引擎安装问题

### <a name="try-gofish"></a>试用 GoFish

如果先前的安装步骤失败，可以使用 GoFish 包管理器安装 AKS 引擎。 [GoFish](https://gofi.sh)将自身描述为跨平台 Homebrew。

#### <a name="install-the-aks-engine-with-gofish-on-linux"></a>在 Linux 上安装带有 GoFish 的 AKS 引擎

从[安装](https://gofi.sh/#install)页面安装 GoFish。

1. 在 bash 提示符下，运行以下命令：

    ```bash
    curl -fsSL https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.sh | bash
    ```

2.  运行以下命令，通过 GoFish 安装 AKS 引擎：

    ```bash
    Run "gofish install aks-engine"
    ```

#### <a name="install-the-aks-engine-with-gofish-on-windows"></a>在 Windows 上安装带有 GoFish 的 AKS 引擎

从[安装](https://gofi.sh/#install)页面安装 GoFish。

1. 在提升的 PowerShell 提示符下，运行以下命令：

    ```PowerShell
    Set-ExecutionPolicy Bypass -Scope Process -Force
    iex ((New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/fishworks/gofish/master/scripts/install.ps1'))
    ```

2.  在同一会话中运行以下命令，以通过 GoFish 安装 AKS 引擎：

    ```PowerShell
    gofish install aks-engine
    ```

### <a name="checklist-for-common-deployment-issues"></a>常见部署问题的清单

当使用 AKS 引擎部署 Kubernetes 群集时遇到错误时，可以检查：

1.  你使用的是正确的服务主体凭据（SPN）吗？
2.  SPN 是否对 Azure Stack 中心订阅有 "参与者" 角色？
3. Azure Stack 中心计划中是否有足够大的配额？
4.  Azure Stack 的中心实例是否正在应用修补或升级？

有关详细信息，请参阅**Azure/aks** GitHub 存储库中的[故障排除](https://github.com/Azure/aks-engine/blob/master/docs/howto/troubleshooting.md)一文。

## <a name="collect-aks-engine-logs"></a>收集 AKS 引擎日志

可以访问由 AKS 引擎创建的查看信息。 当应用程序运行时，AKS 引擎会报告状态和错误。 可以通过管道将输出传递给文本文件，也可以直接从命令行控制台复制。 请参阅 AKS 引擎触发的错误代码列表，[查看自定义脚本扩展错误代码](#review-custom-script-extension-error-codes)。

1.  从 AKS 引擎命令行工具中显示的信息收集标准输出和错误。

2. 从本地文件获取日志。 可以用 **--output-directory**参数设置输出目录。

    设置日志的本地路径：

    ```bash  
    aks-engine --output-directory <path to the directory>
    ```

## <a name="collect-kubernetes-logs"></a>收集 Kubernetes 日志

除了 AKS 引擎日志以外，Kubernetes 组件还生成状态和错误消息。 可以使用 Bash 脚本[getkuberneteslogs.sh](https://github.com/msazurestackworkloads/azurestack-gallery/releases/tag/diagnosis-v0.1.3)收集这些日志。

此脚本将自动收集以下日志： 

 - Microsoft Azure Linux 代理（waagent）日志
 - 自定义脚本扩展日志
 - 运行 kube 容器元数据
 - 运行 kube 容器日志
 - Kubelet 服务状态和日志
 - Etcd 服务状态和日志
 - 库项的 DVM 日志
 - kube-系统快照

如果没有此脚本，则需要连接到群集中的每个节点，以手动查找并下载日志。 此外，该脚本还可以将收集的日志上传到可用来与他人共享日志的存储帐户。

要求：

 - Windows 上的 Linux VM、Git Bash 或 Bash。
 - [Azure CLI](azure-stack-version-profiles-azurecli2.md)安装在运行脚本的计算机中。
 - 已登录到 Azure Stack 中心的 Azure CLI 会话的服务主体标识。 由于脚本能够发现和创建 ARM 资源来完成其工作，因此需要 Azure CLI 和服务主体标识。
 - 在环境中已选择 Kubernetes 群集的用户帐户（订阅）。 
1. 将最新版本的脚本 tar 文件下载到客户端 VM、有权访问 Kubernetes 群集的计算机或使用 AKS 引擎部署群集所用的同一台计算机。

    运行以下命令：

    ```bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    wget https://github.com/msazurestackworkloads/azurestack-gallery/releases/download/diagnosis-v0.1.1/diagnosis-v0.1.1.tar.gz
    tar xvf diagnosis-v0.1.1.tar.gz -C ./
    ```

2. 查找 `getkuberneteslogs.sh` 脚本所需的参数。 此脚本将使用以下参数：

    | 参数 | 说明 | 必选 | 示例 |
    | --- | --- | --- | --- |
    | -h、--help | 打印命令用法。 | 否 | 
    -u,--用户 | 群集 Vm 的管理员用户名 | 是 | azureuser<br>（默认值） |
    | -i、--file | 与用于创建 Kubernetes 群集的公钥关联的 RSA 私钥（有时名为 "id_rsa"）  | 是 | `./rsa.pem` （Putty）<br>`~/.ssh/id_rsa` （SSH） |
    |   -g, --resource-group    | Kubernetes 群集资源组 | 是 | k8sresourcegroup |
    |   -n、--user-namespace               | 从指定命名空间中的容器收集日志（始终收集 kube 系统日志） | 否 |   monitoring |
    |       --api 模型                    | 将 apimodel 文件保存在 Azure Stack 中心存储帐户中。 如果同时提供了--apimodel 参数，则会将文件上传到存储帐户。 | 否 | `./apimodel.json` |
    | --所有-命名空间               | 从所有命名空间中的容器收集日志。 它替代--user-namespace | 否 | |
    | --上传日志                  | 将检索到的日志保存在 Azure Stack 中心存储帐户中。 可在 KubernetesLogs 资源组中找到日志 | 否 | |
    --禁用-主机键检查    | 执行脚本时，将 SSH 的 StrictHostKeyChecking 选项设置为 "否"。 仅在安全环境中使用。 | 否 | |

3. 对你的信息运行以下任何示例命令：

    ```bash
    ./getkuberneteslogs.sh -u azureuser -i private.key.1.pem -g k8s-rg
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --disable-host-key-checking
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg -n default -n monitoring
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs --api-model clusterDefinition.json
    ./getkuberneteslogs.sh -u azureuser -i ~/.ssh/id_rsa -g k8s-rg --upload-logs
    ```

## <a name="review-custom-script-extension-error-codes"></a>查看自定义脚本扩展错误代码

你可以在运行群集中查看由自定义脚本扩展（CSE）创建的错误代码列表。 CSE 错误有助于诊断问题的根本原因。 Kubernetes 群集中使用的 Ubuntu 服务器的 CSE 支持许多 AKS 引擎操作。 有关 CSE 退出代码的详细信息，请参阅[cse_helpers。](https://github.com/Azure/aks-engine/blob/master/parts/k8s/cloud-init/artifacts/cse_helpers.sh)

### <a name="providing-kubernetes-logs-to-a-microsoft-support-engineer"></a>向 Microsoft 支持工程师提供 Kubernetes 日志

如果在收集和检查日志后仍无法解决问题，则可能需要开始创建支持票证的过程，并提供通过使用 `--upload-logs` 参数集运行 `getkuberneteslogs.sh` 收集的日志。 

请与 Azure Stack 中心操作员联系。 操作员使用日志中的信息来创建支持案例。

在解决任何支持问题的过程中，Microsoft 支持工程师可能会要求 Azure Stack 中心操作员收集 Azure Stack 中心系统日志。 你可能需要向操作员提供通过运行 `getkuberneteslogs.sh`上传 Kubernetes 日志的存储帐户信息。

操作员可以运行**Get-azurestacklog** PowerShell cmdlet。 此命令使用参数（`-InputSaSUri`）来指定存储 Kubernetes 日志的存储帐户。

操作员可以将生成的日志与 Microsoft 支持人员可能需要的任何其他系统日志合并，并将其提供给 Microsoft。

## <a name="open-github-issues"></a>打开 GitHub 问题

如果无法解决部署错误，可以打开 GitHub 问题。 

1. 在 AKS 引擎存储库中打开[GitHub 问题](https://github.com/Azure/aks-engine/issues/new)。
2. 使用以下格式添加标题： C`SE error: exit code <INSERT_YOUR_EXIT_CODE>`。
3. 在此问题中包含以下信息：

    - 用于部署群集的群集配置文件 `apimodel json`。 在 GitHub 上发布所有机密和密钥之前，请将其删除。  
     - 以下**kubectl**命令的输出 `get nodes`。  
     - `/var/log/azure/cluster-provision.log` 和 `/var/log/cloud-init-output.log` 的内容

## <a name="next-steps"></a>后续步骤

- 了解[Azure Stack 集线器上的 AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)
