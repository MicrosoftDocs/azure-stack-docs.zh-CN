---
title: 使用 Docker 在 Azure Stack 中运行 PowerShell | Microsoft Docs
description: 使用 Docker 在 Azure Stack 中运行 PowerShell
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 07/09/2019
ms.openlocfilehash: 118f29c46a1b11c07c62407f19b86aa28ada3bd1
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277784"
---
# <a name="use-docker-to-run-powershell-in-azure-stack"></a>使用 Docker 在 Azure Stack 中运行 PowerShell

在本文中，我们使用 Docker 创建基于 Windows 的容器，在其中运行使用各种接口所需的 PowerShell 版本。 必须在 Docker 中使用基于 Windows 的容器。

## <a name="docker-prerequisites"></a>Docker 先决条件

1. 安装 [Docker](https://docs.docker.com/install/)。

1. 在命令行程序（例如 Powershell 或 Bash）中输入以下内容：

    ```bash
        Docker --version
    ```

1. 需要使用需要 Windows 10 的 Windows 容器来运行 Docker。 在运行 Docker 时，请切换到 Windows 容器。

1. 从已加入 Azure Stack 所在的域的计算机运行 Docker。 如果使用 Azure Stack 开发工具包 (ASDK)，需[在远程计算机上安装 VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)。

## <a name="set-up-a-service-principal-for-using-powershell"></a>使用 PowerShell 设置服务主体

若要使用 PowerShell 访问 Azure Stack 中的资源，需要在 Azure Active Directory (Azure AD) 租户中有一个服务主体。 通过基于角色的访问控制 (RBAC) 来委派权限。

1. 若要设置服务主体，请按[通过创建服务主体向应用程序授予对 Azure Stack 资源的访问权限](azure-stack-create-service-principals.md)中的说明操作。

2. 记下应用程序 ID、机密和租户 ID 供以后使用。

## <a name="docker---azure-stack-api-profiles-module"></a>Docker - Azure Stack API 配置文件模块

Dockerfile 打开 Microsoft 映像 *microsoft/windowsservercore*，其中已安装 Windows PowerShell 5.1。 该文件然后会加载 NuGet 和 Azure Stack PowerShell 模块，并从 Azure Stack Tools 下载工具。

1. 以 ZIP 文件形式[下载 azure-stack-powershell 存储库](https://github.com/mattbriggs/azure-stack-powershell)，或者克隆该存储库。

2. 从终端打开存储库文件夹。

3. 在存储库中打开命令行界面，然后输入以下命令：

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. 生成映像以后，请输入以下内容，以便启动交互式容器：

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. 可以将此 shell 用于 cmdlet 了。

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. 使用服务主体连接到 Azure Stack 实例。 现在使用 Docker 中的 PowerShell 提示符。 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell 返回帐户对象：

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. 通过在 Azure Stack 中创建资源组，测试连接性。

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>后续步骤

-  阅读 [Azure Stack 中的 Azure Stack PowerShell](azure-stack-powershell-overview.md) 概述。
- 了解 Azure Stack 中的 [PowerShell 的 API 配置文件](azure-stack-version-profiles.md)。
- 安装 [Azure Stack Powershell](../operator/azure-stack-powershell-install.md)。
- 了解如何创建 [Azure 资源管理器模板](azure-stack-develop-templates.md)以实现云一致性。
