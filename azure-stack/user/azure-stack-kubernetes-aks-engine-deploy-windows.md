---
title: 在 Azure Stack Hub 中的 Windows 上部署 AKS 引擎
description: 了解如何在 Azure Stack Hub 中使用 Windows 计算机托管 AKS 引擎，以便部署和管理 Kubernetes 群集。
author: mattbriggs
ms.topic: article
ms.date: 09/16/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/16/2020
ms.openlocfilehash: 29255d706edcb052711b2ed42e65fd3dc0b6412b
ms.sourcegitcommit: b960df16e84ec9fbccfce772102b91f0b7ae7060
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/03/2020
ms.locfileid: "93291232"
---
# <a name="install-the-aks-engine-on-windows-in-azure-stack-hub"></a>在 Azure Stack Hub 中的 Windows 上安装 AKS 引擎

可以在 Azure Stack Hub 中使用 Windows 计算机托管 AKS 引擎，以便部署和管理 Kubernetes 群集。 在本文中，我们将介绍准备客户端 VM 以管理已连接和断开连接的 Azure Stack Hub 实例的群集、检查安装并在 ASDK 上设置客户端 VM。

## <a name="prepare-the-client-vm"></a>准备客户端 VM

AKS 引擎是一种命令行工具，用于部署和管理 Kubernetes 群集。 可以在 Azure Stack Hub 中的计算机上运行引擎。 从此计算机中，你将执行 AKS 引擎来部署运行群集所需的 IaaS 资源和软件。 然后，可以使用运行引擎的计算机在群集上执行管理任务。

选择客户端计算机时，请考虑：

1. 发生灾难时是否应恢复客户端计算机。
3. 如何连接到客户端计算机以及计算机将如何与群集交互。

## <a name="install-in-a-connected-environment"></a>安装在连接的环境中

可以安装客户端 VM 以在连接到 Internet 的 Azure Stack Hub 上管理 Kubernetes 群集。

1. 在 Azure Stack Hub 中创建 Windows VM。 有关说明，请参阅[快速入门：使用 Azure Stack Hub 门户创建 Windows 服务器 VM](./azure-stack-quick-windows-portal.md)。
2. 连接到 VM。
3. [使用 PowerShell 说明安装 Chocolatey](https://chocolatey.org/install#install-with-powershellexe)。 

    根据 Chocolatey 网站：Chocolatey 是 Windows 的包管理器，类似于 apt-get 或 yum，但适用于 Windows。 它设计为一个分散的框架，用于快速安装所需的应用程序和工具。 它建立在 NuGet 基础结构上，目前使用 PowerShell 作为将包从发行版传送到门、错误、计算机的重点。
4. 在[受支持的 Kubernetes 版本](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions)表中找到 AKS 引擎的版本。 必须在 Azure Stack Hub 市场中提供 AKS 基础引擎。 运行该命令时，必须指定版本 `--version v0.48.0`。 如果不指定版本，该命令将安装最新版本，这可能需要一个市场中不提供的 VHD 映像。
5. 从提升的提示符运行以下命令，并添加版本号：

    ```PowerShell  
        choco install aks-engine --version 0.55.4 -y
    ```

> [!NOTE]  
> 如果此安装方法失败，可以在[断开连接的环境](#install-in-a-disconnected-environment)或者 [Try GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish)（一个备用包管理器）中尝试这些步骤。

## <a name="install-in-a-disconnected-environment"></a>在已断开连接的环境中安装

可以安装客户端 VM 以在与 Internet 断开连接的 Azure Stack Hub 上管理 Kubernetes 群集。

1.  从可访问 Internet 的计算机，转到 GitHub [Azure/aks-engine ](https://github.com/Azure/aks-engine/releases/latest)。 下载 Windows 计算机的存档 (*.tar.gz)，例如 `aks-engine-v0.38.8-windows-amd64.tar.gz`。

2.  在 Azure Stack Hub 实例中创建存储帐户，以便使用 AKS 引擎二进制文件上传存档文件 (*.tar.gz)。 有关使用 Azure 存储资源管理器的说明，请参阅 [Azure 存储资源管理器与 Azure Stack Hub](./azure-stack-storage-connect-se.md)。

3. 在 Azure Stack Hub 中创建 Windows VM。 有关说明，请参阅[快速入门：使用 Azure Stack Hub 门户创建 Windows 服务器 VM](./azure-stack-quick-windows-portal.md)

4.  从上传存档文件 (*.tar.gz) 所在的 Azure Stack Hub 存储帐户 Blob URL，将文件下载到管理 VM。 从命令提示符将存档提取到你有权访问的目录。

5. 连接到 VM。

6. [使用 PowerShell 说明安装 Chocolatey](https://chocolatey.org/install#install-with-powershellexe)。 

7.  在提升的提示符下运行以下命令。 包括正确的版本号：

    ```PowerShell  
        choco install aks-engine --version 0.55.4 -y
    ```

## <a name="verify-the-installation"></a>验证安装

设置客户端 VM 后，请检查是否安装了 AKS 引擎。

1. 连接到客户端 VM。
2. 运行以下命令：

    ```PowerShell  
    aks-engine version
    ```

如果无法验证是否已在客户端 VM 上安装了 AKS 引擎，请参阅 [AKS 引擎安装故障排除](azure-stack-kubernetes-aks-engine-troubleshoot.md)。


## <a name="asdk-installation"></a>ASDK 安装

在 ASDK 外的计算机的 ASDK 上运行 AKS 引擎的客户端 VM 时，需要添加证书。 如果你在 ASDK 环境中使用 Windows VM，则计算机已信任 ASDK 证书。 如果客户端计算机在 ASDK 之外，则需要从 ASDK 中提取证书，并将其添加到 Windows 计算机。

在使用 ASDK 时，如果 Azure 资源管理器终结点使用自签名证书，你需要将此证书显式添加到计算机的受信任证书存储。 可以在 ASDK 中部署的任何 VM 中找到 ASDK 根证书。

1. 导出 CA 根证书。 有关说明，请参阅[导出 Azure Stack Hub CA 根证书](../asdk/asdk-cli.md#export-the-azure-stack-hub-ca-root-certificate)。
2. 信任 Azure Stack Hub CA 根证书。 有关说明，请参阅[信任 Azure Stack Hub CA 根证书](../asdk/asdk-cli.md#trust-the-certificate)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 AKS 引擎在 Azure Stack Hub 上部署 Kubernetes 群集](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
