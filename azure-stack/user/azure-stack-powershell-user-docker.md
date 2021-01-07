---
title: 使用 Docker 在 Azure Stack Hub 中运行 PowerShell
description: 使用 Docker 在 Azure Stack Hub 中运行 PowerShell
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/16/2020
ms.openlocfilehash: d187058561f166084b672d941262c7979f7deec2
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974041"
---
# <a name="use-docker-to-run-powershell-for-azure-stack-hub"></a>使用 Docker 运行适用于 Azure Stack Hub 的 PowerShell

在本文中，你可使用 Docker 创建容器，在其中运行使用各种接口所需的 PowerShell 版本。 你可以找到使用 AzureRM 模块和最新 Az 模块的说明。 AzureRM 要求使用基于 Windows 的容器。 Az 使用基于 Linux 的容器。

## <a name="docker-prerequisites"></a>Docker 先决条件

### <a name="install-docker"></a>安装 Docker

1. 安装 [Docker](https://docs.docker.com/install/)。

1. 在命令行程序（例如 PowerShell 或 Bash）中输入以下内容：

    ```bash
    docker --version
    ```

### <a name="set-up-a-service-principal-for-using-powershell"></a>使用 PowerShell 设置服务主体

若要使用 PowerShell 访问 Azure Stack Hub 中的资源，需要在 Azure Active Directory (Azure AD) 租户中有一个服务主体。 通过基于角色的访问控制 (RBAC) 来委派权限。 可能需要向云运营商请求服务主体。

1. 若要设置服务主体，请按[通过创建服务主体向应用程序授予对 Azure Stack Hub 资源的访问权限](../operator/azure-stack-create-service-principals.md)中的说明操作。

2. 记下应用程序 ID、机密、租户 ID 和对象 ID 供以后使用。

## <a name="run-powershell-in-docker"></a>在 Docker 中运行 PowerShell

### <a name="az-modules"></a>[Az 模块](#tab/az)

在这些说明中，你将运行基于 Linux 的容器映像，该映像包含 PowerShell 和 Azure Stack Hub 所需的模块。

1. 需要使用 Linux 容器运行 Docker。 运行 Docker 时，请切换到 Linux 容器。

1. 从已加入 Azure Stack Hub 所在的域的计算机运行 Docker。 如果使用 Azure Stack 开发工具包 (ASDK)，需[在远程计算机上安装 VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn)。


## <a name="install-azure-stack-hub-az-module-on-a-linux-container"></a>在 Linux 容器上安装 Azure Stack Hub Az 模块

1. 在命令行中运行以下 Docker 命令，以在 Ubuntu 容器中运行 PowerShell：

    ```bash
    docker run -it mcr.microsoft.com/azurestack/powershell
    ```

    可运行 Ubuntu、Debian 或 Centos。 可在 GitHub 存储库 [azurestack-powershell](https://github.com/Azure/azurestack-powershell) 中找到以下 Docker 文件。 有关 Docker 文件的最新更改，请参阅 GitHub 存储库。 每个 OS 均已标记。 将冒号之后部分的标记替换为所需 OS 的标记。

    | Linux | Docker 映像 |
    | --- | --- |
    | Ubuntu | `docker run -it mcr.microsoft.com/azurestack/powershell:ubuntu-18.04` |
    | Debian | `docker run -it mcr.microsoft.com/azurestack/powershell:debian-9` |
    | CentOS | `docker run -it mcr.microsoft.com/azurestack/powershell:centos-7` |

2. 可以将此 shell 用于 cmdlet 了。 通过登录并运行 `Test-AzureStack.ps1` 来测试 shell 连接性。

    首先创建服务主体凭据。 你将需要“机密”和“应用程序 ID” 。 在运行 `Test-AzureStack.ps1` 检查容器时，还需要“对象 ID”。 可能需要向云运营商请求服务主体。

    键入以下 cmdlet 来创建服务主体对象：

    ```powershell  
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    ```

5. 通过从 Azure Stack Hub 实例运行具有以下值的以下脚本来连接到你的环境。

    | 值 | 描述 |
    | --- | --- |
    | 环境的名称。 | Azure Stack Hub 环境的名称。 |
    | 资源管理器终结点 | 资源管理器的 URL。 如果你不知道，请联系你的云运营商。 该 URL 应类似于 `https://management.region.domain.com`。 | 
    | 目录租户 ID | Azure Stack Hub 租户目录的 ID。 | 
    | 凭据 | 包含服务主体的对象。 在本例中为 `$pscredential`。  |

    ```powershell
    ./Login-Environment.ps1 -Name <String> -ResourceManagerEndpoint <resource manager endpoint> -DirectoryTenantId <String> -Credential $pscredential
    ```

   PowerShell 返回帐户对象。

7. 通过在容器中运行 `Test-AzureStack.ps1` 脚本来测试环境。 指定服务主体“对象 ID”。 如果未指明对象 ID，脚本仍将运行，但它只是测试租户（用户）模块，无法测试需要管理员权限的模块。

    ```powershell  
    ./Test-AzureStack.ps1 <Object ID>
    ```

### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/rm)

在这些说明中，你将运行基于 Windows 的容器映像，并安装 PowerShell 和 Azure Stack Hub 所需的模块。 如果计划在 Windows 上运行 Docker，将需要在物理计算机上运行 Docker。 Hyper-v 不支持嵌套虚拟化。

1. 需要使用需要 Windows 10 的 Windows 容器来运行 Docker。 在运行 Docker 时，请切换到 Windows 容器。 支持 Az 模块的映像将需要 Docker 17.05 或更高版本。

1. 从已加入 Azure Stack Hub 所在的域的计算机运行 Docker。 如果使用 Azure Stack 开发工具包 (ASDK)，需[在远程计算机上安装 VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn)。

### <a name="install-azure-stack-hub-azurerm-module-on-a-windows-container"></a>在 Windows 容器上安装 Azure Stack Hub AzureRM 模块

Dockerfile 打开 Microsoft 映像 *microsoft/windowsservercore*，其中已安装 Windows PowerShell 5.1。 该文件然后会加载 NuGet 和 Azure Stack Hub PowerShell 模块，并从 Azure Stack Hub Tools 下载工具。

1. 以 ZIP 文件形式[下载 azure-stack-powershell 存储库](https://github.com/Azure-Samples/azure-stack-hub-powershell-in-docker.git)，或者克隆该存储库。

2. 从终端打开存储库文件夹。

3. 在存储库中打开命令行界面，然后输入以下命令：

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. 生成映像以后，请输入以下内容，以便启动交互式容器：

    ```bash  
    docker run -it azure-stack-powershell powershell
    ```

    记下容器名称。 可以使用相同的容器，而不是每次通过运行以下 Docker 命令来创建新容器：

    ```bash  
        docker exec -it "Container name" powershell
    ```

5. 可以将此 shell 用于 cmdlet 了。

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. 使用服务主体连接到 Azure Stack Hub 实例。 现在使用 Docker 中的 PowerShell 提示符。 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint <Your Azure Resource Manager endoint>
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId <TenantID> -ServicePrincipal -Credential $pscredential
    ```

   PowerShell 返回帐户对象：

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. 通过在 Azure Stack Hub 中创建资源组，测试连接性。

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

---

## <a name="next-steps"></a>后续步骤

- 阅读 [Azure Stack Hub 中的 Azure Stack Hub PowerShell](azure-stack-powershell-overview.md) 概述。
- 了解 Azure Stack Hub 中的 [PowerShell 的 API 配置文件](azure-stack-version-profiles.md)。
- 安装 [Azure Stack Hub PowerShell](../operator/azure-stack-powershell-install.md)。
- 了解如何创建 [Azure 资源管理器模板](azure-stack-develop-templates.md)以实现云一致性。
