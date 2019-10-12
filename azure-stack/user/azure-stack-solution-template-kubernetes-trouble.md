---
title: Kubernetes 部署故障排除 Azure Stack |Microsoft Docs
description: 了解如何对要 Azure Stack 的 Kubernetes 部署进行故障排除。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.author: mabrigg
ms.date: 10/10/2019
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 14a32696a3e46782b8990ba57f9510976200f7d3
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277567"
---
# <a name="troubleshoot-kubernetes-deployment-to-azure-stack"></a>Kubernetes 部署故障排除 Azure Stack

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

> [!Note]  
> Azure Stack 上的 Kubernetes 现为预览版。 预览版目前不支持 Azure Stack 脱接方案。 仅在开发和测试方案中使用 marketplace 项。

本文介绍了如何对 Kubernetes 群集进行故障排除。 若要开始进行故障排除，请查看部署所需的元素。 可能需要从 Azure Stack 或托管 Kubernetes 的 Linux VM 收集部署日志。 若要从管理终结点检索日志，请联系 Azure Stack 管理员。

## <a name="overview-of-kubernetes-deployment"></a>Kubernetes 部署概述

在对群集进行故障排除之前，请查看 Azure Stack Kubernetes 群集部署过程。 部署使用 Azure 资源管理器解决方案模板来创建 Vm，并安装群集的 AKS 引擎。

### <a name="kubernetes-deployment-workflow"></a>Kubernetes 部署工作流

下图显示了用于部署群集的一般过程。

![部署 Kubernetes 过程](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>部署步骤

1. 从市场项收集输入参数。

    输入设置 Kubernetes 群集所需的值，包括：
    -  **用户名**：属于 Kubernetes 群集和 DVM 的 Linux 虚拟机（Vm）的用户名。
    -  **SSH 公钥**：用于向所有作为 Kubernetes 群集和 DVM 的一部分创建的 Linux 计算机授权的密钥。
    -  **服务主体**：供 Kubernetes Azure 云提供商使用的 ID。 创建服务主体时标识为应用程序 ID 的客户端 ID。 
    -  **客户端机密**：在创建服务主体时创建的密钥。

2. 创建部署 VM 和自定义脚本扩展。
    -  使用市场 Linux 映像 **Ubuntu Server 16.04 LTS** 创建部署 Linux VM。
    -  从 marketplace 下载并运行自定义脚本扩展。 该脚本是“用于 Linux 2.0 的自定义脚本”。
    -  运行 DVM 自定义脚本。 该脚本执行以下任务：
        1. 从 Azure 资源管理器元数据终结点获取库终结点。
        2. 从 Azure 资源管理器元数据终结点获取 Active Directory 资源 ID。
        3. 加载 AKS 引擎的 API 模型。
        4. 将 AKS 引擎部署到 Kubernetes 群集并将 Azure Stack 云配置文件保存到 @no__t。
3. 创建主 VM。

4. 下载并运行自定义脚本扩展。

5. 运行主脚本。

    该脚本执行以下任务：
    - 安装 etcd、Docker 和 Kubernetes 资源（如 kubelet）。 etcd 是一种分布式键值存储，用于跨一组计算机存储数据。 Docker 支持基本功能操作系统级别虚拟化（称为容器）。 Kubelet 是在每个 Kubernetes 节点上运行的节点代理。
    - 设置**etcd**服务。
    - 设置**kubelet**服务。
    - 启动 kubelet。 此任务涉及以下步骤：
        1. 启动 API 服务。
        2. 启动控制器服务。
        3. 启动计划程序服务。
6. 创建代理 VM。

7. 下载并运行自定义脚本扩展。

7. 运行代理脚本。 代理自定义脚本执行以下任务：
    - 安装**etcd**。
    - 设置**kubelet**服务。
    - 加入 Kubernetes 群集。

## <a name="steps-to-troubleshoot-kubernetes"></a>对 Kubernetes 进行故障排除的步骤

可以在支持 Kubernetes 群集的 Vm 上收集和查看部署日志。 与 Azure Stack 管理员联系，验证你需要使用的 Azure Stack 版本，并从与你的部署相关 Azure Stack 获取日志。

1. 查看[部署状态](#review-deployment-status)，并从 Kubernetes 群集中的主节点检索日志。
2. 确保使用最新版本的 Azure Stack。 如果不确定使用的是哪个版本，请与 Azure Stack 管理员联系。
3.  查看 VM 创建文件。 可能遇到了以下问题：  
    - 公钥可能无效。 查看创建的密钥。  
    - 创建 VM 可能触发了内部错误或触发了创建错误。 许多因素（包括 Azure Stack 订阅的容量限制）都可能导致错误。
    - 请确保 VM 的完全限定的域名（FQDN）以重复的前缀开头。
4.  如果 VM **正常**，请评估 DVM。 如果 DVM 有一条错误消息：

    - 公钥可能无效。 查看创建的密钥。  
    - 请与 Azure Stack 管理员联系，使用特权终结点检索 Azure Stack 的日志。 有关详细信息，请参阅 [Azure Stack 诊断工具](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep-to-collect-diagnostic-logs)。
5. 如果对部署有疑问，可以在 [Azure Stack 论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)中提问，或查看是否已经有人回答了该问题。 

## <a name="review-deployment-status"></a>查看部署状态

部署 Kubernetes 群集时，可以查看部署状态以检查任何问题。

1. 打开 [Azure Stack 门户](https://portal.local.azurestack.external)。
2. 选择“资源组”，然后选择在部署 Kubernetes 群集时使用的资源组名称。
3. 依次选择“部署”、“部署名称”。

    ![排查 Kubernetes 问题：选择部署](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  请参阅故障排除窗口。 部署的每个资源提供以下信息：
    
    | 属性 | 描述 |
    | ----     | ----        |
    | Resource | 资源的名称。 |
    | 类型 | 资源提供程序和资源的类型。 |
    | 状态 | 项状态。 |
    | 时间戳 | 时间的 UTC 时间戳。 |
    | 操作详细信息 | 操作详细信息，例如操作中涉及的资源提供程序、资源终结点和资源名称。 |

    每个项都有绿色或红色的状态图标。

## <a name="review-deployment-logs"></a>查看部署日志

如果 Azure Stack 门户没有提供足够的信息，无法进行故障排除或解决部署失败，则下一步就是深入了解群集日志。 若要手动检索部署日志，通常需要连接到群集的主 Vm 之一。 更简单的替代方法是下载并运行 Azure Stack 团队提供的以下[Bash 脚本](https://aka.ms/AzsK8sLogCollectorScript)。 此脚本连接到 DVM 和群集的 Vm，收集相关的系统和群集日志，并将它们下载回你的工作站。

### <a name="prerequisites"></a>先决条件

你需要在用于管理 Azure Stack 的计算机上具有 Bash 提示。 在 Windows 计算机上，可以通过安装[适用于 windows 的 Git](https://git-scm.com/downloads)来获取 Bash 提示。 安装后，在 "开始" 菜单中查找 " _Git Bash_ "。

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

    | 参数           | 描述                                                                                                      | 示例                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host      | 公共 IP 或 DVM 的完全限定的域名（FQDN）。 该 VM 名称以 `vmd-` 开头。 | IP：192.168.102.38<br>DNS： myk8s. p p. test-azurestack |
    | -h、--help  | 打印命令用法。 | |
    | -i、--identity-file | 创建 Kubernetes 群集时，传递给 marketplace 项的 RSA 私钥文件的路径。 需要将远程登录到 Kubernetes 节点。 | C:\data\id_rsa.pem （Putty）<br>~/.ssh/id_rsa (SSH)
    | -m、--master-主机   | 公共 IP 或 Kubernetes 主节点的完全限定的域名（FQDN）。 该 VM 名称以 `k8s-master-` 开头。 | IP：192.168.102.37<br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u、--user          | 创建 Kubernetes 群集时，传递给 marketplace 项的用户名。 需要将远程登录到 Kubernetes 节点。 | azureuser （默认值） |


   添加参数值时，命令可能类似于以下示例：

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. 几分钟后，该脚本会将收集的日志输出到名为 @no__t 的目录。 可在其中找到属于群集的每个 VM 的目录。

    日志收集器脚本还将在日志文件中查找错误，如果发现已知问题，则包括故障排除步骤。 请确保运行最新版本的脚本，以增加查找已知问题的机会。

> [!Note]  
> 请查看此 GitHub[存储库](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis)，了解有关日志收集器脚本的更多详细信息。

## <a name="next-steps"></a>后续步骤

[将 Kubernetes 部署到 Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[将 Kubernetes 群集添加到市场（面向 Azure Stack 操作员）](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)

[Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
