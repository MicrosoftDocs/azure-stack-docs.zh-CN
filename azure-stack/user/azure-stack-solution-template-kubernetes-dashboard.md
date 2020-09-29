---
title: 在 Azure Stack Hub 中访问 Kubernetes 仪表板
description: 了解如何在 Azure Stack Hub 中访问 Kubernetes 仪表板
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 06/18/2019
ms.openlocfilehash: 03dcd58562aaa0bc65bbc545e19c9e3a24c52a4b
ms.sourcegitcommit: a5d3cbe1a10c2a63de95b9e72391dd83473ee299
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88920485"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack-hub"></a>在 Azure Stack Hub 中访问 Kubernetes 仪表板 

> [!NOTE]   
> 仅使用 Kubernetes Azure Stack 市场项将部署群集作为概念证明。 有关 Azure Stack 上支持的 Kubernetes 群集，请使用 [AKS 引擎](azure-stack-kubernetes-aks-engine-overview.md)。

Kubernetes 包含一个可用于基本管理操作的 Web 仪表板。 使用此仪表板，可以查看应用程序的基本运行状况状态和指标，创建并部署服务，以及编辑现有应用程序。 本文介绍如何在 Azure Stack Hub 中设置 Kubernetes 仪表板。

## <a name="prerequisites-for-kubernetes-dashboard"></a>Kubernetes 仪表板的先决条件

* Azure Stack Hub Kubernetes 群集

    需要将 Kubernetes 群集部署到 Azure Stack Hub。 有关详细信息，请参阅[部署 Kubernetes](azure-stack-solution-template-kubernetes-deploy.md)。

* SSH 客户端

    需要使用 SSH 客户端安全地连接到群集中的主节点。 如果使用的是 Windows，则可以使用 [Putty](/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm)。 需要在部署 Kubernetes 群集时使用的私钥。

* FTP (PSCP)

    可能还需要支持 SSH 和 SSH 文件传输协议的 FTP 客户端，以将证书从主节点传输到 Azure Stack Hub 管理计算机。 可以使用 [FileZilla](https://filezilla-project.org/download.php?type=client)。 需要在部署 Kubernetes 群集时使用的私钥。

## <a name="overview-of-steps-to-enable-dashboard"></a>仪表板启用步骤概述

1.  从群集中的主节点导出 Kubernetes 证书。 
2.  将证书导入 Azure Stack Hub 管理计算机。
2.  打开 Kubernetes Web 仪表板。 

## <a name="export-certificate-from-the-master"></a>导出来自主节点的证书 

可以从群集中的主节点检索仪表板的 URL。

1. 从 Azure Stack Hub 仪表板获取群集主机的公共 IP 地址和用户名。 若要获取此信息，请执行以下操作：

    - 登录到 Azure Stack Hub 门户 `https://portal.local.azurestack.external/`。
    - 选择“所有服务” > “所有资源” 。 找到群集资源组中的主节点。 主节点名为 `k8s-master-<sequence-of-numbers>`。 

2. 在门户中打开主节点。 复制公共 IP 地址。 单击“连接”，在“使用 VM 本地帐户登录”框中输入用户名 。 即创建群集时设置的用户名。 使用公共 IP 地址，而不是“连接”边栏选项卡中列出的专用 IP 地址。

3.  打开 SSH 客户端以连接到主节点。 如果在 Windows 上操作，可以使用 [Putty](/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) 创建连接。 使用主节点的公共 IP 地址、用户名，并添加创建群集时所用的私钥。

4.  连接终端后，键入 `kubectl` 以打开 Kubernetes 命令行客户端。

5. 运行以下命令：

    ```Bash   
    kubectl cluster-info 
    ``` 
    找到仪表板的 URL。 例如：`https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  提取自签名证书，然后将其转换为 PFX 格式。 运行以下命令：

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  获取 kube-system 命名空间中的机密列表。 运行以下命令：

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    记下 kubernetes-dashboard-token-\<XXXXX> 值。 

8.  获取令牌并保存。 将 `kubernetes-dashboard-token-<####>` 更新为前面步骤中的机密值。

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>导入证书

1. 打开 Filezilla 并连接到主节点。 需以下各项：

    - 主节点公共 IP
    - 用户名
    - 专用机密
    - 使用 SFTP - SSH 文件传输协议

2. 将 `/etc/kubernetes/certs/client.pfx` 和 `/etc/kubernetes/certs/ca.crt` 复制到 Azure Stack Hub 管理计算机。

3. 记下该文件的位置。 使用该位置更新脚本，然后使用权限提升的提示打开 PowerShell。 运行更新的脚本：  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>打开 Kubernetes 仪表板 

1. 禁用 Web 浏览器上的弹出式窗口阻止程序。

2. 将浏览器指向运行命令 `kubectl cluster-info` 时所记录的 URL。 例如：https:\//azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy 
3. 选择客户端证书。
4. 输入令牌。 
5. 重新连接到主节点上的 bash 命令行，并授予对 `kubernetes-dashboard` 的权限。 运行以下命令：

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    脚本可为 `kubernetes-dashboard` 授予云管理员权限。 有关详细信息，请参阅[对于启用了 RBAC 的群集](/azure/aks/kubernetes-dashboard)。

可以使用仪表板。 有关 Kubernetes 仪表板的详细信息，请参阅 [Kubernetes Web UI 仪表板](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

![Azure Stack Hub Kubernetes 仪表板](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="troubleshooting"></a>故障排除

### <a name="custom-virtual-networks"></a>自定义虚拟网络

在[自定义虚拟网络](./kubernetes-aks-engine-custom-vnet.md)中部署 Kubernetes 后，如果访问 Kubernetes 仪表板时遇到连接问题，请确保目标子网已链接到 AKS 引擎创建的路由表和网络安全组资源。

确保网络安全组规则允许主节点与 Kubernetes 仪表板 pod IP 之间的通信。 可以使用主节点上的 ping 命令来对此进行验证。

## <a name="next-steps"></a>后续步骤 

[将 Kubernetes 部署到 Azure Stack Hub](azure-stack-solution-template-kubernetes-deploy.md)  

[将 Kubernetes 群集添加到市场（面向 Azure Stack Hub 操作员）](../operator/azure-stack-solution-template-kubernetes-cluster-add.md)  

[Azure 上的 Kubernetes](/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
