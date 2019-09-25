---
title: 在 Azure Stack 中的 Linux 上安装 AKS 引擎 |Microsoft Docs
description: 了解如何使用 Azure Stack 中的 Linux 计算机来托管 AKS 引擎，以便部署和管理 Kubernetes 群集。
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
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: 9c600451070373d10ee943d8e497693d89708801
ms.sourcegitcommit: 4e48f1e5af74712a104eda97757dc5f50a591936
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224970"
---
# <a name="install-the-aks-engine-on-linux-in-azure-stack"></a>在 Azure Stack 中的 Linux 上安装 AKS 引擎

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

你可以使用 Azure Stack 中的 Linux 计算机来托管 AKS 引擎，以便部署和管理 Kubernetes 群集。 在本文中，我们将了解如何准备客户端 VM 来管理群集，以确保连接和断开连接的 Azure Stack 实例，检查安装，并在 ASDK 上设置客户端 VM。

## <a name="prepare-the-client-vm"></a>准备客户端 VM

AKS 引擎是用于部署和管理 Kubernetes 群集的命令行工具。 可以在 Azure Stack 中的计算机上运行引擎。 在此计算机上，你将执行 AKS 引擎来部署运行群集所需的 IaaS 资源和软件。 然后，你可以使用运行引擎的计算机在群集上执行管理任务。

选择客户端计算机时，请考虑以下事项：

1. 如果在发生灾难时客户端计算机应可恢复。
2. 如何连接到客户端计算机以及计算机如何与群集进行交互。

## <a name="install-in-a-connected-environment"></a>在连接的环境中安装

可以安装客户端 VM 来管理连接到 Internet Azure Stack 上的 Kubernetes 群集。

1. 在 Azure Stack 中创建 Linux VM。 有关说明，请[参阅快速入门：使用 Azure Stack 门户](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal)创建 Linux 服务器 VM。
2. 连接到 VM。
3. 在[支持的 Kubernetes 版本](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions)表中查找 AKS 引擎的版本。 Azure Stack Marketplace 中必须提供 AKS 基本引擎。 运行命令时，必须指定版本`--version v0.41.0`。 如果未指定版本，则该命令将安装最新版本，此版本可能需要在 marketplace 中不可用的 VHD 映像。
4. 运行下面的命令：

    ```bash  
        curl -o get-akse.sh https://raw.githubusercontent.com/Azure/aks-engine/master/scripts/get-akse.sh
        chmod 700 get-akse.sh
        ./get-akse.sh --version v0.41.0
    ```

    > [!Note]  
    > 如果安装失败，则可以尝试[断开连接环境](#install-in-a-disconnected-environment)中的步骤，或[尝试 GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish)（备用程序包管理器）。

## <a name="install-in-a-disconnected-environment"></a>在断开连接的环境中安装

可以在与 Internet 断开连接 Azure Stack 上安装客户端 VM 以管理 Kubernetes 群集。

1.  在有权访问 Internet 的计算机上，请访问 GitHub [Azure/aks](https://github.com/Azure/aks-engine/releases/latest)。 下载适用于 Linux 计算机的存档（* gz），例如`aks-engine-v0.xx.x-linux-amd64.tar.gz`。

2.  在 Azure Stack 实例中创建一个存储帐户，以使用 AKS 引擎二进制文件上传存档文件（* gz）。 有关使用 Azure 存储资源管理器的说明，请参阅[使用 Azure Stack Azure 存储资源管理器](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se)。

3. 在 Azure Stack 中创建 Linux VM。 有关说明，请[参阅快速入门：使用 Azure Stack 门户](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal)创建 Linux 服务器 VM。

3.  从上传存档文件的 Azure Stack 存储帐户 blob URL （* gz），将文件下载到管理 VM。 将存档提取到目录`/usr/local/bin`。

4. 连接到 VM。

5.  运行下面的命令：

    ```bash  
    curl -o aks-engine-v0.xx.x-linux-amd64.tar.gz <httpurl/aks-engine-v0.xx.x-linux-amd64.tar.gz>
    tar xvzf aks-engine-v0.xx.x-linux-amd64.tar.gz -C /usr/local/bin
    ```

## <a name="verify-the-installation"></a>验证安装

设置客户端 VM 后，请检查是否已安装 AKS 引擎。

1. 连接到客户端 VM。
2. 运行下面的命令：

    ```bash  
    aks-engine version
    ```

如果无法验证是否已在客户端 VM 上安装了 AKS 引擎，请参阅[排查 AKS engine 安装问题](azure-stack-kubernetes-aks-engine-troubleshoot.md)


## <a name="asdk-installation"></a>ASDK 安装

在 ASDK 上运行 AKS 引擎的客户端 VM 时，需要添加证书。

当你使用 ASDK 时，Azure 资源管理器终结点使用自签名证书时，需要显式将此证书添加到计算机的受信任证书存储区中。 可以在 ASDK 中部署的任何 VM 中找到 ASDK 根证书。 例如，在 Ubuntu VM 中，你将在此目录`/var/lib/waagent/Certificates.pem`中找到它。 

将证书文件复制到以下命令：

```bash
sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt

sudo update-ca-certificates
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在 Azure Stack 上使用 AKS 引擎部署 Kubernetes 群集](azure-stack-kubernetes-aks-engine-deploy-cluster.md)