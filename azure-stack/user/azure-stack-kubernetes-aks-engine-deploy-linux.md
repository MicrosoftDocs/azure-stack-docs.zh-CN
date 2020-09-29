---
title: 在 Azure Stack Hub 中的 Linux 上安装 AKS 引擎
description: 了解如何在 Azure Stack Hub 中使用 Linux 计算机托管 AKS 引擎，以便部署和管理 Kubernetes 群集。
author: mattbriggs
ms.topic: article
ms.date: 09/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/16/2020
ms.openlocfilehash: 49855cbe926dc29b20c3309e4fc3a084ee5a5e70
ms.sourcegitcommit: 719569bb9e3f9924494a9229b4f2d211ae3f4f74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2020
ms.locfileid: "90717950"
---
# <a name="install-the-aks-engine-on-linux-in-azure-stack-hub"></a>在 Azure Stack Hub 中的 Linux 上安装 AKS 引擎

可以在 Azure Stack Hub 中使用 Linux 计算机托管 AKS 引擎，以便部署和管理 Kubernetes 群集。 在本文中，我们将介绍如何准备客户端 VM 以便针对联网 Azure Stack Hub 实例和离线 Azure Stack Hub 实例管理群集，如何检查安装，以及如何在 ASDK 上设置客户端 VM。

## <a name="prepare-the-client-vm"></a>准备客户端 VM

AKS 引擎是一种用于部署和管理 Kubernetes 群集的命令行工具。 可以在 Azure Stack Hub 中的计算机上运行该引擎。 从这台计算机中，你将执行 AKS 引擎来部署运行群集所需的 IaaS 资源和软件。 然后，可以使用运行该引擎的计算机在群集上执行管理任务。

在选择客户端计算机时，请考虑：

1. 当发生灾难时该客户端计算机是否应为可恢复的。
2. 如何连接到该客户端计算机以及该计算机如何与群集交互。

## <a name="install-in-a-connected-environment"></a>在联网环境中安装

可以安装客户端 VM，以便在连接到 Internet 的 Azure Stack Hub 上管理 Kubernetes 群集。

1. 在 Azure Stack Hub 中创建 Linux VM。 有关说明，请参阅[快速入门：通过使用 Azure Stack Hub 门户创建 Linux 服务器 VM](./azure-stack-quick-linux-portal.md)。
2. 连接到 VM。
3. 在[受支持的 Kubernetes 版本](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions)表中查找 AKS 引擎的版本。 AKS 基础映像必须已在 Azure Stack Hub 市场中提供。 运行该命令时，必须指定版本 `--version v0.55.4`。 如果不指定版本，该命令将安装最新版，这样可能就会需要市场中未提供的 VHD 映像。
4. 运行以下命令：

    ```bash  
        curl -o get-akse.sh https://raw.githubusercontent.com/Azure/aks-engine/master/scripts/get-akse.sh
        chmod 700 get-akse.sh
        ./get-akse.sh --version 0.55.4
    ```

    > [!NOTE]  
    > 如果此安装方法失败，可以尝试[离线环境](#install-in-a-disconnected-environment)中的步骤，或者[尝试 GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish)（一个备用包管理器）。

## <a name="install-in-a-disconnected-environment"></a>在离线环境中安装

可以安装客户端 VM，以便在与 Internet 断开连接的 Azure Stack Hub 上管理 Kubernetes 群集。

1.  在可访问 Internet 的计算机上，转到 GitHub [Azure/aks-engine](https://github.com/Azure/aks-engine/releases/latest)。 下载用于 Linux 计算机的存档 (*.tar.gz)，例如 `aks-engine-v0.xx.x-linux-amd64.tar.gz`。

2.  在 Azure Stack Hub 实例中创建存储帐户，以上传包含 AKS 引擎二进制文件的存档文件 (*.tar.gz)。 有关使用 Azure 存储资源管理器的说明，请参阅 [Azure 存储资源管理器与 Azure Stack Hub](./azure-stack-storage-connect-se.md)。

3. 在 Azure Stack Hub 中创建 Linux VM。 有关说明，请参阅[快速入门：通过使用 Azure Stack Hub 门户创建 Linux 服务器 VM](./azure-stack-quick-linux-portal.md)。

3.  从上传存档文件 (*.tar.gz) 的 Azure Stack Hub 存储帐户 Blob URL 将文件下载到管理 VM。 将该存档提取到 `/usr/local/bin` 目录。

4. 连接到 VM。

5.  运行以下命令：

    ```bash  
    curl -o aks-engine-v0.xx.x-linux-amd64.tar.gz <httpurl/aks-engine-v0.xx.x-linux-amd64.tar.gz>
    tar xvzf aks-engine-v0.xx.x-linux-amd64.tar.gz -C /usr/local/bin
    ```

## <a name="verify-the-installation"></a>验证安装

设置客户端 VM 后，请检查是否已安装了 AKS 引擎。

1. 连接到客户端 VM。
2. 运行以下命令：

   ```bash  
   aks-engine version
   ```

3. 如果 Azure 资源管理器终结点使用自签名证书，你需要将根证书显式添加到该计算机的受信任证书存储。 可在 VM 中的此目录下找到根证书：/var/lib/waagent/Certificates.pem。 使用以下命令复制该证书文件： 

   ```bash
   sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt 
   sudo update-ca-certificates
   ```

如果无法验证是否已在客户端 VM 上安装了 AKS 引擎，请参阅 [AKS 引擎安装故障排除](azure-stack-kubernetes-aks-engine-troubleshoot.md)


## <a name="asdk-installation"></a>ASDK 安装

在 ASDK 上为 AKS 引擎运行客户端 VM 时，需要添加证书。

在使用 ASDK 时，如果 Azure 资源管理器终结点使用自签名证书，你需要将此证书显式添加到该计算机的受信任证书存储。 可以在 ASDK 中部署的任何 VM 上找到 ASDK 根证书。 例如，在 Ubuntu VM 上，可在 `/var/lib/waagent/Certificates.pem` 目录中找到根证书。 

使用以下命令复制该证书文件：

```bash
sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt

sudo update-ca-certificates
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 AKS 引擎在 Azure Stack Hub 上部署 Kubernetes 群集](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
