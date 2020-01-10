---
title: 在 Azure Stack Hub 中的 Windows 上部署 AKS 引擎 |Microsoft Docs
description: 了解如何使用 Azure Stack 集线器中的 Windows 计算机来托管 AKS 引擎，以便部署和管理 Kubernetes 群集。
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
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 3c329217ae6b8331c2b74fb46d4082069b011429
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75820196"
---
# <a name="install-the-aks-engine-on-windows-in-azure-stack-hub"></a>在 Azure Stack 集线器中的 Windows 上安装 AKS 引擎

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

你可以使用 Azure Stack 集线器中的 Windows 计算机来托管 AKS 引擎，以便部署和管理 Kubernetes 群集。 在本文中，我们将介绍如何准备客户端 VM 来管理群集，以实现连接和断开连接的 Azure Stack 中心实例，检查安装，并在 ASDK 上设置客户端 VM。

## <a name="prepare-the-client-vm"></a>准备客户端 VM

AKS 引擎是用于部署和管理 Kubernetes 群集的命令行工具。 可以在 Azure Stack 中心内的计算机上运行引擎。 在此计算机上，你将执行 AKS 引擎来部署运行群集所需的 IaaS 资源和软件。 然后，你可以使用运行引擎的计算机在群集上执行管理任务。

选择客户端计算机时，请考虑以下事项：

1. 如果在发生灾难时客户端计算机应可恢复。
3. 如何连接到客户端计算机以及计算机如何与群集进行交互。

## <a name="install-in-a-connected-environment"></a>在连接的环境中安装

可以安装客户端 VM 来管理连接到 Internet 的 Azure Stack 集线器上的 Kubernetes 群集。

1. 在 Azure Stack 中心创建 Windows VM。 有关说明，请参阅[快速入门：使用 Azure Stack 集线器门户创建 Windows SERVER VM](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal)。
2. 连接到 VM。
3. [使用 PowerShell 说明安装 Chocolatey。](https://chocolatey.org/install#install-with-powershellexe) 

    根据 Chocolaty 网站： Chocolatey 是适用于 Windows 的程序包管理器，例如 apt 或 yum，但适用于 Windows。 它旨在作为一个分散框架，用于快速安装所需的应用程序和工具。 它构建于 NuGet 基础结构上，该基础结构当前使用 PowerShell 来将包从发行版传送到你的门、err、computer。
4. 在[支持的 Kubernetes 版本](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions)表中查找 AKS 引擎的版本。 Azure Stack 中心市场中必须提供 AKS 基本引擎。 运行命令时，必须指定 `--version v0.43.0`版本。 如果未指定版本，则该命令将安装最新版本，此版本可能需要在 marketplace 中不可用的 VHD 映像。
5. 在权限提升的提示符下运行以下命令，并包括版本号：

    ```PowerShell  
        choco install aks-engine --version 0.43.0 -y
    ```

> [!Note]  
> 如果此方法的安装失败，你可以尝试[断开连接环境](#install-in-a-disconnected-environment)中的步骤，或[尝试 GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish)（备用程序包管理器）。

## <a name="install-in-a-disconnected-environment"></a>在断开连接的环境中安装

可以安装客户端 VM 来管理从 Internet 断开连接 Azure Stack 集线器上的 Kubernetes 群集。

1.  在有权访问 Internet 的计算机上，请访问 GitHub [Azure/aks](https://github.com/Azure/aks-engine/releases/latest)。 下载 Windows 计算机的存档（* gz），例如 `aks-engine-v0.38.8-windows-amd64.tar.gz`。

2.  在 Azure Stack Hub 实例中创建一个存储帐户，以使用 AKS 引擎二进制文件上传存档文件（* gz）。 有关使用 Azure 存储资源管理器的说明，请参阅[使用 Azure Stack 集线器 Azure 存储资源管理器](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se)。

3. 在 Azure Stack 中心创建 Windows VM。 有关说明，请参阅[快速入门：使用 Azure Stack 中心门户创建 Windows SERVER VM](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal)

4.  从上传存档文件的 Azure Stack 中心存储帐户 blob URL （* gz），将文件下载到管理 VM。 在命令提示符下，将存档提取到可以访问的目录。

5. 连接到 VM。

6. [使用 PowerShell 说明安装 Chocolatey。](https://chocolatey.org/install#install-with-powershellexe) 

7.  在权限提升的提示符下运行以下命令。 包含正确的版本号：

    ```PowerShell  
        choco install aks-engine --version 0.43.0 -y
    ```

## <a name="verify-the-installation"></a>验证安装

设置客户端 VM 后，请检查是否已安装 AKS 引擎。

1. 连接到客户端 VM。
2. 运行以下命令：

    ```PowerShell  
    aks-engine version
    ```

如果无法验证是否已在客户端 VM 上安装了 AKS 引擎，请参阅[排查 AKS engine 安装问题](azure-stack-kubernetes-aks-engine-troubleshoot.md)


## <a name="asdk-installation"></a>ASDK 安装

在 ASDK 外的计算机上的 ASDK 上运行 AKS 引擎的客户端 VM 时，需要添加证书。 如果你在 ASDK 环境中使用 Windows VM，则该计算机已信任 ASDK 证书。 如果客户端计算机不在 ASDK，则需要从 ASDK 提取证书，并将其添加到 Windows 计算机。

当你使用 ASDK 时，Azure 资源管理器终结点使用自签名证书时，需要显式将此证书添加到计算机的受信任证书存储区中。 可以在 ASDK 中部署的任何 VM 中找到 ASDK 根证书。

1. 导出 CA 根证书。 有关说明，请参阅[导出 Azure Stack 中心 CA 根证书](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2#export-the-azure-stack-hub-ca-root-certificate)
2. 信任 Azure Stack 中心 CA 根证书。 有关说明，请参阅[信任 Azure Stack 中心 CA 根证书](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2#trust-the-azure-stack-hub-ca-root-certificate)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [在 Azure Stack 集线器上使用 AKS 引擎部署 Kubernetes 群集](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
