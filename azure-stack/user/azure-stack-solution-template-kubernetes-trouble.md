---
title: 排查向 Azure Stack Hub 部署 Kubernetes 的问题
description: 了解如何排查向 Azure Stack Hub 部署 Kubernetes 的问题。
author: mattbriggs
ms.topic: article
ms.author: mabrigg
ms.date: 08/24/2020
ms.reviewer: waltero
ms.lastreviewed: 11/14/2019
ms.openlocfilehash: 9a88b6ff0e759c38ad62e6455cbee25eaa9be9eb
ms.sourcegitcommit: a5d3cbe1a10c2a63de95b9e72391dd83473ee299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88920757"
---
# <a name="troubleshoot-kubernetes-deployment-to-azure-stack-hub"></a>排查向 Azure Stack Hub 部署 Kubernetes 的问题

> [!NOTE]  
> 仅使用 Kubernetes Azure Stack Hub 市场项将部署群集作为概念证明。 有关 Azure Stack Hub 上支持的 Kubernetes 群集，请使用 [AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)。

本文介绍如何排查 Kubernetes 群集故障。 若要开始故障排除，请审阅部署所需的元素。 可能需要从 Azure Stack Hub 或托管 Kubernetes 的 Linux VM 收集部署日志。 若要从管理终结点检索日志，请联系 Azure Stack Hub 管理员。

## <a name="overview-of-kubernetes-deployment"></a>Kubernetes 部署概述

开始排查群集问题之前，请查看 Azure Stack Hub Kubernetes 群集部署过程。 该部署使用 Azure 资源管理器解决方案模板来为群集创建 VM 并安装 AKS 引擎。

### <a name="kubernetes-deployment-workflow"></a>Kubernetes 部署工作流

下图显示了用于部署群集的一般过程。

![部署 Kubernetes 过程](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>部署步骤

1. 从市场项收集输入参数。

    输入设置 Kubernetes 群集所需的值，包括：
    -  **用户名**：构成 Kubernetes 群集和 DVM 的 Linux 虚拟机 (VM) 的用户名。
    -  **SSH 公钥**：用于向所有作为 Kubernetes 群集和 DVM 的一部分创建的 Linux 计算机授权的密钥。
    -  **服务主体**：供 Kubernetes Azure 云提供商使用的 ID。 创建服务主体时标识为应用程序 ID 的客户端 ID。 
    -  **客户端机密**：创建服务主体时创建的密钥。

2. 创建部署 VM 和自定义脚本扩展。
    -  使用市场 Linux 映像 **Ubuntu Server 16.04 LTS** 创建部署 Linux VM。
    -  从市场下载自定义脚本扩展并运行该扩展。 该脚本是“用于 Linux 2.0 的自定义脚本”。
    -  运行 DVM 自定义脚本。 该脚本执行以下任务：
        1. 从 Azure 资源管理器元数据终结点获取库终结点。
        2. 从 Azure 资源管理器元数据终结点获取 Active Directory 资源 ID。
        3. 为 AKS 引擎加载 API 模型。
        4. 将 AKS 引擎部署到 Kubernetes 群集，然后将 Azure Stack Hub 云配置文件保存到 `/etc/kubernetes/azurestackcloud.json`。
3. 创建主 VM。

4. 下载并运行自定义脚本扩展。

5. 运行主脚本。

    该脚本执行以下任务：
    - 安装 etcd、Docker 和 Kubernetes 资源（如 kubelet）。 etcd 是一种分布式键值存储，用于跨一组计算机存储数据。 Docker 支持基本功能操作系统级别虚拟化（称为容器）。 Kubelet 是在每个 Kubernetes 节点上运行的节点代理。
    - 设置 etcd 服务。
    - 设置 kubelet 服务。
    - 启动 kubelet。 此任务涉及以下步骤：
        1. 启动 API 服务。
        2. 启动控制器服务。
        3. 启动计划程序服务。
6. 创建代理 VM。

7. 下载并运行自定义脚本扩展。

7. 运行代理脚本。 代理自定义脚本执行以下任务：
    - 安装 etcd。
    - 设置 kubelet 服务。
    - 加入 Kubernetes 群集。

## <a name="steps-to-troubleshoot-kubernetes"></a>排查 Kubernetes 问题的步骤

可以在支持 Kubernetes 群集的 VM 上收集部署日志并查看。 请与 Azure Stack Hub 管理员联系，以验证需要使用的 Azure Stack Hub 版本并从与部署相关的 Azure Stack Hub 获取日志。

1. 在部署了群集的资源组的“部署”窗格中查看 ARM 部署返回的错误代码。 可在 AKS 引擎 GitHub 存储库中的[排除故障](https://github.com/msazurestackworkloads/azurestack-gallery/blob/master/kubernetes/docs/troubleshooting.md)一文中找到错误代码的说明。 如果参考错误说明后无法解决问题，请继续执行以下步骤。
2. 从 Kubernetes 群集中的主节点查看[部署状态](#review-deployment-status)并检索日志。
3. 检查是否使用最新版本的 Azure Stack Hub。 如果不确定使用的是哪个版本，请与 Azure Stack Hub 管理员联系。
4. 查看 VM 创建文件。 可能遇到了以下问题：  
    - 公钥可能无效。 查看创建的密钥。  
    - 创建 VM 可能触发了内部错误或触发了创建错误。 许多因素（包括 Azure Stack Hub 订阅的容量限制）都可能导致错误。
    - 确保 VM 的完全限定的域名 (FQDN) 以重复的前缀开头。
5.  如果 VM **正常**，请评估 DVM。 如果 DVM 有一条错误消息：
    - 公钥可能无效。 查看创建的密钥。  
    - 请与 Azure Stack Hub 管理员联系，以使用特权终结点检索 Azure Stack Hub 的日志。 有关详细信息，请参阅 [Azure Stack Hub 诊断工具](../operator/azure-stack-get-azurestacklog.md)。
5. 如果对部署有疑问，可以在 [Azure Stack Hub 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提问，或查看是否已经有人回答了该问题。 


## <a name="review-deployment-status"></a>查看部署状态

部署 Kubernetes 群集时，可以查看部署状态以检查任何问题。

1. 打开 Azure Stack Hub 门户 `https://portal.local.azurestack.external`。
2. 选择“资源组”，然后选择在部署 Kubernetes 群集时使用的资源组名称。
3. 依次选择“部署”、“部署名称”。

    ![排查 Kubernetes 问题：选择部署](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  查看故障排除窗口。 部署的每个资源提供以下信息：
    
    | 属性 | 说明 |
    | ----     | ----        |
    | 资源 | 资源的名称。 |
    | 类型 | 资源提供程序和资源的类型。 |
    | 状态 | 项状态。 |
    | 时间戳 | 时间的 UTC 时间戳。 |
    | 操作详细信息 | 操作详细信息，例如操作中涉及的资源提供程序、资源终结点和资源名称。 |

    每个项都有绿色或红色的状态图标。

## <a name="review-deployment-logs"></a>查看部署日志

如果 Azure Stack Hub 门户没有提供足够的信息用于解决或克服部署失败问题，接下来应深入分析群集日志。 若要手动检索部署日志，通常需要连接到群集的主 VM 之一。 更简单的替代方法是下载并运行以下 [Bash 脚本](https://aka.ms/AzsK8sLogCollectorScript)，该脚本由 Azure Stack Hub 团队提供。 此脚本连接到 DVM 和群集的 VM，收集相关的系统和群集日志，并将它们下载到工作站。

### <a name="prerequisites"></a>先决条件

用于管理 Azure Stack Hub 的计算机上需要有 Bash 提示符。 在 Windows 计算机上，可以通过安装[适用于 Windows 的 Git](https://git-scm.com/downloads) 来获取 Bash 提示符。 安装完成后，在“开始”菜单中查找 Git Bash。

### <a name="retrieving-the-logs"></a>检索日志

按照以下步骤收集和下载群集日志：

1. 打开 Bash 提示符。 在 Windows 计算机上，打开 Git Bash 或运行：`C:\Program Files\Git\git-bash.exe`。

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
    | -d、--vmd-host      | DVM 的公共 IP 或完全限定的域名 (FQDN)。 该 VM 名称以 `vmd-` 开头。 | IP：192.168.102.38<br>DNS：vmd-myk8s.local.cloudapp.azurestack.external |
    | -h、--help  | 打印命令用法。 | |
    | -i、--identity-file | 创建 Kubernetes 群集时，传递给市场项的 RSA 私钥文件的路径。 需要使用它远程登录到 Kubernetes 节点。 | C:\data\id_rsa.pem (Putty)<br>~/.ssh/id_rsa (SSH)
    | -m、--master-host   | Kubernetes 主节点的公共 IP 或完全限定的域名 (FQDN)。 该 VM 名称以 `k8s-master-` 开头。 | IP：192.168.102.37<br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u、--user          | 创建 Kubernetes 群集时，传递给市场项的用户名。 需要使用它远程登录到 Kubernetes 节点。 | azureuser（默认值） |


   添加参数值时，命令可能类似于以下示例：

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. 几分钟后，该脚本会将收集的日志输出到名为 `KubernetesLogs_{{time-stamp}}` 的目录。 可在其中找到群集中的每个 VM 的目录。

    日志收集器脚本还在日志文件中查找错误，如果发现已知问题，会包含故障排除步骤。 请确保运行最新版本的脚本，以增加找到已知问题的几率。

> [!NOTE]  
> 查看此 GitHub [存储库](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) 以详细了解日志收集器脚本。

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md)

[将 Kubernetes 群集添加到市场（面向 Azure Stack Hub 操作员）](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Azure 上的 Kubernetes](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
