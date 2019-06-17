---
title: 访问 Azure Stack 中的 Kubernetes 仪表板 |Microsoft Docs
description: 了解如何访问 Azure Stack 中的 Kubernetes 仪表板
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 04/19/2019
ms.openlocfilehash: 003149d15a8509e37f01e379e0929c2ef758bcef
ms.sourcegitcommit: ad2f2cb4dc8d5cf0c2c37517d5125921cff44cdd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/14/2019
ms.locfileid: "67138835"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>访问 Azure Stack 中的 Kubernetes 仪表板 

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包* 
> [!Note]   
> Azure Stack 上的 Kubernetes 现为预览版。 Azure Stack 离线的场景中当前不支持预览。 

Kubernetes 包括用于基本的管理操作的 web 仪表板。 此仪表板可让您查看基本运行状况状态和您的应用程序的指标、 创建和部署服务，以及编辑现有的应用。 本文介绍如何设置 Azure Stack 上的 Kubernetes 仪表板。

## <a name="prerequisites-for-kubernetes-dashboard"></a>Kubernetes 仪表板的先决条件

* Azure Stack Kubernetes 群集

    你将需要在部署到 Azure Stack 的 Kubernetes 群集。 有关详细信息，请参阅[部署 Kubernetes](azure-stack-solution-template-kubernetes-deploy.md)。

* SSH 客户端

    你将需要 SSH 客户端安全地连接到主节点在群集中。 如果使用的 Windows，则可以使用[Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm)。 你将需要部署 Kubernetes 群集时所用的私钥。

* FTP (PSCP)

    您可能还需要 FTP 客户端 (如[FileZilla](https://filezilla-project.org/download.php?type=client)) 支持 SSH 和 ssh 配合使用 FTP 将证书从主节点传输到您的 Azure Stack 管理计算机。 你将需要部署 Kubernetes 群集时所用的私钥。

## <a name="overview-of-steps-to-enable-dashboard"></a>若要启用仪表板的步骤概述

1.  从群集中的主节点中导出的 Kubernetes 证书。 
2.  导入到 Azure Stack 管理计算机的证书。
2.  打开 Kubernetes web 仪表板。 

## <a name="export-certificate-from-the-master"></a>从主节点中导出证书 

在群集中，可以从主节点检索仪表板的 URL。

1. 在 Azure Stack 仪表板中，群集主机获取公共 IP 地址和用户名。 若要获取此信息：

    - 登录到 [Azure Stack 门户](https://portal.local.azurestack.external/)。
    - 选择**所有服务** > **的所有资源**。 在群集资源组中查找母版。 名为 master `k8s-master-<sequence-of-numbers>`。 

2. 在门户中打开主节点。 复制**公共 IP**地址。 单击**Connect**获取您的用户名称**使用 VM 本地帐户登录**框。 此名称是相同的用户名创建群集时设置。 使用公共 IP 地址而不是 connect 边栏选项卡中列出的专用 IP 地址。

3.  打开 SSH 客户端以连接到 master。 如果您从事 Windows 上，则可以使用[Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm)以创建连接。 将公共 IP 地址用于主节点，用户名，并添加创建群集时所用的私钥。

4.  当终端连接时，键入`kubectl`打开 Kubernetes 命令行客户端。

5. 运行以下命令：

    ```Bash   
    kubectl cluster-info 
    ``` 
    找到仪表板的 URL。 例如：`https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  提取自签名的证书并将其转换为 PFX 格式。 运行以下命令：

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  获取列表中的机密**kube 系统**命名空间。 运行以下命令：

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    请记下的 kubernetes 的仪表板-令牌-\<XXXXX > 值。 

8.  获取令牌，并将其保存。 更新`kubernetes-dashboard-token-<####>`与前一步骤中的机密值。

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>导入证书

1. 打开 Filezilla 并连接到主节点。 你需要:

    - 主节点的公共 IP
    - 用户名
    - 专用密钥
    - 使用**SFTP-SSH 文件传输协议**

2. 复制`/etc/kubernetes/certs/client.pfx`和`/etc/kubernetes/certs/ca.crt`到 Azure Stack 管理计算机。

3. 记下文件位置。 更新脚本的位置，并使用提升的提示符打开 PowerShell。 运行更新的脚本：  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>打开 Kubernetes 仪表板 

1. 禁用浏览器弹出窗口阻止程序。

2. 转至 URL 记下运行该命令时`kubectl cluster-info`。 例如： `https:\//azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy` 
3. 选择客户端证书。
4. 输入的令牌。 
5. 重新连接到主节点上的 bash 命令行并对其授予权限`kubernetes-dashboard`。 运行以下命令：

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    脚本将向提供`kubernetes-dashboard`云管理员权限。 有关详细信息，请参阅[有关 RBAC 启用群集](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)。

在仪表板已做好准备。 有关 Kubernetes 仪表板的详细信息，请参阅[Kubernetes Web UI 仪表板](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

![Azure Stack Kubernetes 仪表板](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>后续步骤 

[将 Kubernetes 部署到 Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)  

[将 Kubernetes 群集添加到市场（面向 Azure Stack 操作员）](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)  

[在 Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
