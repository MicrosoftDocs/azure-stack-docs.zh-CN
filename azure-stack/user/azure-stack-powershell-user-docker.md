---
title: 使用 Docker 在 Azure Stack Hub 中运行 PowerShell |Microsoft Docs
description: 使用 Docker 在 Azure Stack 集线器中运行 PowerShell
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
ms.openlocfilehash: e55fd18babea30d0b004c1219d8ce4842f4750bd
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75819465"
---
# <a name="use-docker-to-run-powershell-in-azure-stack-hub"></a>使用 Docker 在 Azure Stack 集线器中运行 PowerShell

本文介绍如何使用 Docker 创建基于 Windows 的容器，这些容器用于运行使用各种接口所需的 PowerShell 版本。 在 Docker 中，必须使用基于 Windows 的容器。

## <a name="docker-prerequisites"></a>Docker 必备组件

1. 安装[Docker](https://docs.docker.com/install/)。

1. 在命令行程序（例如 Powershell 或 Bash）中，输入：

    ```bash
        Docker --version
    ```

1. 需要使用需要 Windows 10 的 Windows 容器来运行 Docker。 运行 Docker 时，切换到 Windows 容器。

1. 从与 Azure Stack 中心加入同一域的计算机中运行 Docker。 如果使用的是 Azure Stack 开发工具包（ASDK），则需要[在远程计算机上安装 VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn)。

## <a name="set-up-a-service-principal-for-using-powershell"></a>使用 PowerShell 设置服务主体

若要使用 PowerShell 来访问 Azure Stack 集线器中的资源，需要 Azure Active Directory （Azure AD）租户中的服务主体。 您可以使用用户的基于角色的访问控制（RBAC）委托权限。

1. 若要设置服务主体，请按照使用[为应用程序授予 Azure Stack 集线器资源的访问权限，方法是创建服务主体](azure-stack-create-service-principals.md)。

2. 记下 "应用程序 ID"、"机密" 和 "租户 ID" 以供以后使用。

## <a name="docker---azure-stack-hub-api-profiles-module"></a>Docker-Azure Stack 中心 API 配置文件模块

Dockerfile 打开安装了 Windows PowerShell 5.1 的 Microsoft 映像*microsoft/windowsservercore*。 然后，该文件会加载 NuGet 和 Azure Stack 中心 PowerShell 模块，并从 Azure Stack 中心工具下载工具。

1. 将[azure stack powershell 存储库下载](https://github.com/mattbriggs/azure-stack-powershell)为 ZIP 文件，或者克隆存储库。

2. 从终端打开存储库文件夹。

3. 在存储库中打开命令行界面，然后输入以下命令：

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. 生成映像后，请输入以下内容启动交互式容器：

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. Shell 已准备就绪，可用于你的 cmdlet。

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. 使用服务主体连接到 Azure Stack 中心实例。 现在，在 Docker 中使用 PowerShell 提示符。 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell 将返回你的帐户对象：

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. 通过在 Azure Stack Hub 中创建资源组来测试连接性。

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>后续步骤

-  阅读[Azure Stack 中心中 Azure Stack 中心 PowerShell](azure-stack-powershell-overview.md)的概述。
- 在 Azure Stack Hub 中了解[PowerShell 的 API 配置文件](azure-stack-version-profiles.md)。
- 安装[Azure Stack Hub Powershell](../operator/azure-stack-powershell-install.md)。
- 了解如何创建[Azure 资源管理器模板](azure-stack-develop-templates.md)来实现云一致性。
