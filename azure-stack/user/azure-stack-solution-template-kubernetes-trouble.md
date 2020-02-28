---
title: Azure Stack 中心排查 Kubernetes 部署问题
description: 了解如何对 Kubernetes 部署到 Azure Stack 中心进行故障排除。
author: mattbriggs
ms.topic: article
ms.author: mabrigg
ms.date: 11/14/2019
ms.reviewer: waltero
ms.lastreviewed: 11/14/2019
ms.openlocfilehash: 80222728bca3aa65fd211ccc5ef5700d0a15f397
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703615"
---
# <a name="troubleshoot-kubernetes-deployment-to-azure-stack-hub"></a>Azure Stack 中心排查 Kubernetes 部署问题

> [!Note]  
> 仅使用 Kubernetes Azure Stack 中心市场项将群集部署为概念证明。 对于 Azure Stack 集线器上支持的 Kubernetes 群集，请使用[AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)。

本文介绍了如何对 Kubernetes 群集进行故障排除。 若要开始进行故障排除，请查看部署所需的元素。 可能需要从托管 Kubernetes 的 Azure Stack 集线器或 Linux Vm 收集部署日志。 若要从管理终结点检索日志，请联系 Azure Stack 中心管理员。

## <a name="overview-of-kubernetes-deployment"></a>Kubernetes 部署概述

在对群集进行故障排除之前，请查看 Azure Stack 中心 Kubernetes 群集部署过程。 部署使用 Azure 资源管理器解决方案模板来创建 Vm，并安装群集的 AKS 引擎。

### <a name="kubernetes-deployment-workflow"></a>Kubernetes 部署工作流

下图显示了部署群集的一般过程。

![部署 Kubernetes 进程](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>部署步骤

1. 收集 marketplace 项中的输入参数。

    输入设置 Kubernetes 群集所需的值，包括：
    -  **用户名**：属于 Kubernetes 群集和 DVM 的 Linux 虚拟机（vm）的用户名。
    -  **SSH 公钥**：作为 Kubernetes 群集和 DVM 的一部分而创建的所有 Linux 计算机的授权的密钥。
    -  **服务主体**： Kubernetes Azure 云提供程序使用的 ID。 在你创建服务主体时标识为应用程序 ID 的客户端 ID。 
    -  **客户端密钥**：创建服务主体时创建的密钥。

2. 创建部署 VM 和自定义脚本扩展。
    -  使用 marketplace Linux 映像**Ubuntu Server 16.04-LTS**创建部署 linux VM。
    -  从 marketplace 下载并运行自定义脚本扩展。 此脚本是**适用于 Linux 2.0 的自定义脚本**。
    -  运行 DVM 自定义脚本。 此脚本执行以下任务：
        1. 从 Azure 资源管理器元数据终结点获取库终结点。
        2. 从 Azure 资源管理器元数据终结点获取 active directory 资源 ID。
        3. 加载 AKS 引擎的 API 模型。
        4. 将 AKS 引擎部署到 Kubernetes 群集并将 Azure Stack 中心云配置文件保存到 `/etc/kubernetes/azurestackcloud.json`。
3. 创建主 Vm。

4. 下载并运行自定义脚本扩展。

5. 运行主脚本。

    此脚本执行以下任务：
    - 安装 etcd、Docker 和 Kubernetes 资源，如 kubelet。 etcd 是一种分发密钥值存储，它提供了一种在计算机群集中存储数据的方法。 Docker 支持基本的操作系统级 virtualizations，称为容器。 Kubelet 是在每个 Kubernetes 节点上运行的节点代理。
    - 设置**etcd**服务。
    - 设置**kubelet**服务。
    - 启动 kubelet。 此任务包含以下步骤：
        1. 启动 API 服务。
        2. 启动控制器服务。
        3. 启动计划程序服务。
6. 创建代理 Vm。

7. 下载并运行自定义脚本扩展。

7. 运行代理脚本。 代理自定义脚本执行以下任务：
    - 安装**etcd**。
    - 设置**kubelet**服务。
    - 加入 Kubernetes 群集。

## <a name="steps-to-troubleshoot-kubernetes"></a>对 Kubernetes 进行故障排除的步骤

可以在支持 Kubernetes 群集的 Vm 上收集和查看部署日志。 与 Azure Stack 中心管理员联系，验证你需要使用的 Azure Stack 中心的版本，以及从与部署相关的 Azure Stack 中心获取日志。

1. 在部署了群集的资源组的 "**部署**" 窗格中查看 ARM 部署返回的错误代码。 有关错误代码的说明，请在 AKS engine GitHub 存储库中的[故障排除](https://github.com/msazurestackworkloads/azurestack-gallery/blob/master/kubernetes/docs/troubleshooting.md)一文。 如果无法解决与错误说明有关的问题，请继续执行这些步骤。
2. 查看[部署状态](#review-deployment-status)，并从 Kubernetes 群集中的主节点检索日志。
3. 检查是否使用的是最新版本的 Azure Stack 中心。 如果不确定要使用的版本，请与 Azure Stack 中心管理员联系。
4. 查看 VM 创建文件。 您可能遇到了以下问题：  
    - 公钥可能无效。 查看已创建的密钥。  
    - 创建 VM 可能触发了内部错误或触发了创建错误。 许多因素可能导致错误，包括 Azure Stack 中心订阅的容量限制。
    - 请确保 VM 的完全限定的域名（FQDN）以重复的前缀开头。
5.  如果 VM**正常**，则计算 DVM。 如果 DVM 有错误消息：
    - 公钥可能无效。 查看已创建的密钥。  
    - 请与 Azure Stack 中心管理员联系，使用特权终结点检索 Azure Stack 中心的日志。 有关详细信息，请参阅[Azure Stack 集线器诊断工具](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)。
5. 如果你对部署有疑问，可以发布它，或查看是否有人在[Azure Stack 中心论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中回答了问题。 


## <a name="review-deployment-status"></a>查看部署状态

部署 Kubernetes 群集时，可以查看部署状态以检查是否存在任何问题。

1. 打开[Azure Stack 集线器门户](https://portal.local.azurestack.external)。
2. 选择 "**资源组**"，然后选择部署 Kubernetes 群集时使用的资源组的名称。
3. 选择 "**部署**"，然后选择**部署名称**。

    ![排查 Kubernetes 问题：选择部署](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  请参阅故障排除窗口。 每个已部署的资源提供以下信息：
    
    | properties | 说明 |
    | ----     | ----        |
    | 资源 | 资源的名称。 |
    | 类型 | 资源提供程序和资源的类型。 |
    | 状态 | 项的状态。 |
    | TimeStamp | 时间的 UTC 时间戳。 |
    | 操作详细信息 | 操作详细信息，如操作中涉及的资源提供程序、资源终结点和资源的名称。 |

    每个项的状态图标为绿色或红色。

## <a name="review-deployment-logs"></a>查看部署日志

如果 Azure Stack 集线器门户没有提供足够的信息，无法进行故障排除或解决部署失败，下一步就是深入了解群集日志。 若要手动检索部署日志，通常需要连接到群集的主 Vm 之一。 更简单的替代方法是下载并运行 Azure Stack 中心团队提供的以下[Bash 脚本](https://aka.ms/AzsK8sLogCollectorScript)。 此脚本连接到 DVM 和群集的 Vm，收集相关的系统和群集日志，并将它们下载回你的工作站。

### <a name="prerequisites"></a>必备条件

你需要在用于管理 Azure Stack 集线器的计算机上具有 Bash 提示。 在 Windows 计算机上，可以通过安装[适用于 windows 的 Git](https://git-scm.com/downloads)来获取 Bash 提示。 安装后，在 "开始" 菜单中查找 " _Git Bash_ "。

### <a name="retrieving-the-logs"></a>检索日志

按照以下步骤收集和下载群集日志：

1. 打开 Bash 提示符。 在 Windows 计算机中，打开_Git Bash_或运行： `C:\Program Files\Git\git-bash.exe`。

2. 在 Bash 提示符下运行以下命令，下载日志收集器脚本：

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    chmod 744 getkuberneteslogs.sh
    ```

3. 查找脚本所需的信息并运行该脚本：

    | 参数           | 说明                                                                                                      | 示例                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host      | 公共 IP 或 DVM 的完全限定的域名（FQDN）。 VM 名称以 `vmd-`开头。 | IP：192.168.102.38<br>DNS： myk8s. p p. test-azurestack |
    | -h、--help  | 打印命令用法。 | |
    | -i、--file | 创建 Kubernetes 群集时，传递给 marketplace 项的 RSA 私钥文件的路径。 需要将远程登录到 Kubernetes 节点。 | C:\data\ id_rsa （Putty）<br>~/.ssh/id_rsa (SSH)
    | -m、--master-主机   | 公共 IP 或 Kubernetes 主节点的完全限定的域名（FQDN）。 VM 名称以 `k8s-master-`开头。 | IP：192.168.102.37<br>FQDN： k8s-p p. test-azurestack      |
    | -u, --user          | 创建 Kubernetes 群集时，传递给 marketplace 项的用户名。 需要将远程登录到 Kubernetes 节点。 | azureuser （默认值） |


   添加参数值时，命令可能类似于以下示例：

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. 几分钟后，该脚本会将收集的日志输出到名为 `KubernetesLogs_{{time-stamp}}`的目录。 可在其中找到属于群集的每个 VM 的目录。

    日志收集器脚本还将在日志文件中查找错误，如果发现已知问题，则包括故障排除步骤。 请确保运行最新版本的脚本，以增加查找已知问题的机会。

> [!Note]  
> 请查看此 GitHub[存储库](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis)，了解有关日志收集器脚本的更多详细信息。

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack 中心](azure-stack-solution-template-kubernetes-deploy.md)

[将 Kubernetes 群集添加到 Marketplace （适用于 Azure Stack 中心操作员）](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
