---
title: 访问 Azure Stack 集线器中的 Kubernetes 仪表板
description: 了解如何访问 Azure Stack 集线器中的 Kubernetes 仪表板
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 0efcac6635a94b3fdc5551f9aa61b5c774e93905
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84631188"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack-hub"></a>访问 Azure Stack 集线器中的 Kubernetes 仪表板 

> [!Note]   
> 仅使用 Kubernetes Azure Stack Marketplace 项将群集部署为概念证明。 有关 Azure Stack 上支持的 Kubernetes 群集，请使用[AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)。

Kubernetes 包含一个 web 仪表板，可用于基本管理操作。 使用此仪表板，可以查看应用程序的基本运行状况状态和指标，创建并部署服务，以及编辑现有应用程序。 本文介绍如何在 Azure Stack 集线器上设置 Kubernetes 仪表板。

## <a name="prerequisites-for-kubernetes-dashboard"></a>Kubernetes 仪表板的先决条件

* Azure Stack 中心 Kubernetes 群集

    需要将 Kubernetes 群集部署到 Azure Stack 中心。 有关详细信息，请参阅[部署 Kubernetes](azure-stack-solution-template-kubernetes-deploy.md)。

* SSH 客户端

    需要使用 SSH 客户端来连接到群集中的主节点。 如果使用的是 Windows，则可以使用[Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm)。 部署 Kubernetes 群集时，需要使用私钥。

* FTP （PSCP）

    你可能还需要一个支持 SSH 和 SSH 文件传输协议的 FTP 客户端，以便将证书从主节点传输到 Azure Stack 中心管理计算机。 可以使用[FileZilla](https://filezilla-project.org/download.php?type=client)。 部署 Kubernetes 群集时，需要使用私钥。

## <a name="overview-of-steps-to-enable-dashboard"></a>启用仪表板的步骤概述

1.  从群集的主节点中导出 Kubernetes 证书。 
2.  将证书导入到 Azure Stack 集线器管理计算机。
2.  打开 "Kubernetes web 仪表板"。 

## <a name="export-certificate-from-the-master"></a>从主服务器导出证书 

可从群集中的主节点检索仪表板的 URL。

1. 从 Azure Stack 中心仪表板获取群集主机的公共 IP 地址和用户名。 若要获取此信息：

    - 登录到 Azure Stack 中心门户 `https://portal.local.azurestack.external/` 。
    - 选择 "**所有服务**" "  >  **所有资源**"。 在群集资源组中查找主节点。 主节点命名为 `k8s-master-<sequence-of-numbers>` 。 

2. 在门户中打开主节点。 复制**公共 IP**地址。 单击 "**连接**"，在 "**使用 VM 本地帐户登录**" 框中获取用户名。 这是创建群集时所设置的相同用户名。 使用公共 IP 地址，而不使用 "连接" 边栏选项卡中列出的专用 IP 地址。

3.  打开 SSH 客户端以连接到主节点。 如果在 Windows 上操作，可以使用 [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) 创建连接。 你将使用主节点的公共 IP 地址、用户名，并添加在创建群集时使用的私钥。

4.  当终端连接时，键入 `kubectl` 以打开 Kubernetes 命令行客户端。

5. 运行以下命令：

    ```Bash   
    kubectl cluster-info 
    ``` 
    查找仪表板的 URL。 例如：`https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  提取自签名证书并将其转换为 PFX 格式。 运行以下命令：

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  获取**kube**命名空间中的机密列表。 运行以下命令：

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    记下 kubernetes 的标记 \<XXXXX> 值。 

8.  获取令牌并将其保存。 将更新为 `kubernetes-dashboard-token-<####>` 上一步中的机密值。

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>导入证书

1. 打开 Filezilla 并连接到主节点。 你将需要：

    - 主节点公共 IP
    - 用户名
    - 私有机密
    - 使用**SFTP SSH 文件传输协议**

2. 将 `/etc/kubernetes/certs/client.pfx` 和复制 `/etc/kubernetes/certs/ca.crt` 到 Azure Stack 集线器管理计算机。

3. 记下文件位置。 使用位置更新脚本，然后使用提升的提示符打开 PowerShell。 运行更新的脚本：  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>打开 Kubernetes 仪表板 

1. 在 Web 浏览器上禁用弹出窗口阻止程序。

2. 当你运行该命令时，将浏览器指向所述的 URL `kubectl cluster-info` 。 例如： https： \/ /azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https： kubernetes：/proxy 
3. 选择客户端证书。
4. 输入令牌。 
5. 重新连接到主节点上的 bash 命令行并为授予权限 `kubernetes-dashboard` 。 运行以下命令：

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    此脚本提供 `kubernetes-dashboard` 云管理员权限。 有关详细信息，请参阅[for 已启用 RBAC 的群集](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)。

您可以使用 "仪表板"。 有关 Kubernetes 仪表板的详细信息，请参阅[Kubernetes WEB UI 仪表板](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

![Azure Stack 中心 Kubernetes 仪表板](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="troubleshooting"></a>疑难解答

### <a name="custom-virtual-networks"></a>自定义虚拟网络

如果在将 Kubernetes 部署到[自定义虚拟网络](https://docs.microsoft.com/azure-stack/user/kubernetes-aks-engine-custom-vnet)后访问 Kubernetes 仪表板时遇到连接问题，请确保目标子网链接到 AKS 引擎创建的路由表和网络安全组资源。

请确保网络安全组规则允许主节点和 Kubernetes 仪表板 pod IP 之间的通信。 这可以通过使用主节点上的 ping 命令进行验证。

## <a name="next-steps"></a>后续步骤 

[将 Kubernetes 部署到 Azure Stack 中心](azure-stack-solution-template-kubernetes-deploy.md)  

[将 Kubernetes 群集添加到 Marketplace （适用于 Azure Stack 中心操作员）](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)  

[Azure 上的 Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
