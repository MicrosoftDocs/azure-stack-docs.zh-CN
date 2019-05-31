---
title: 使用 Docker 在 Azure Stack 中运行 PowerShell |Microsoft Docs
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
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 42ed9766b43ce3c0c1c455d8ea286a5b453df325
ms.sourcegitcommit: 2ee75ded704e8cfb900d9ac302d269c54a5dd9a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394377"
---
# <a name="use-docker-to-run-powershell-in-azure-stack"></a>使用 Docker 在 Azure Stack 中运行 PowerShell

在本文中，使用 Docker 来创建基于 Windows 的容器以在其上运行的 PowerShell 以使用各种接口所需的版本。 在 Docker，必须使用基于 Windows 的容器。

## <a name="docker-prerequisites"></a>Docker 先决条件

1. 安装[Docker](https://docs.docker.com/install/)。

1. 在命令行程序，例如 Powershell 或 Bash，输入：

    ```bash
        Docker -version
    ```

1. 您需要使用 Windows 容器需要 Windows 10 运行 Docker。 在运行 Docker 时，切换到 Windows 容器。

1. 从加入到与 Azure Stack 相同的域计算机上运行 Docker。 如果使用 Azure Stack 开发工具包 (ASDK)，需要安装[在远程计算机上的 VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)。

## <a name="set-up-a-service-principal-for-using-powershell"></a>为使用 PowerShell 设置服务主体

若要使用 PowerShell 访问 Azure Stack 中的资源，在 Azure Active Directory (Azure AD) 租户中都需要服务主体。 委托的权限与用户基于角色的访问控制 (RBAC)。

1. 若要设置服务主体，请按照中的说明[通过创建服务主体向 Azure Stack 资源的应用程序访问](azure-stack-create-service-principals.md)。

2. 请注意应用程序 ID、 密钥和租户 ID 以供将来使用。

## <a name="docker---azure-stack-api-profiles-module"></a>Docker-Azure Stack API 配置文件模块

Dockerfile 将打开的 Microsoft 映像*microsoft/windowsservercore*，其中包含安装 Windows PowerShell 5.1。 该文件，然后加载 NuGet 和 Azure Stack PowerShell 模块，并从 Azure Stack 工具下载这些工具。

1. [Azure stack powershell 存储库下载](https://github.com/mattbriggs/azure-stack-powershell)作为 ZIP 文件或克隆存储库。

2. 从你的终端中打开存储库文件夹。

3. 在存储库中，打开一个命令行接口，然后输入以下命令：

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. 生成映像后，请输入开始交互式容器：

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. 在 shell 便可供你 cmdlet。

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. 使用服务主体连接到 Azure Stack 实例。 现在在 Docker 中使用 PowerShell 提示符。 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell 将返回你帐户的对象：

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. 通过在 Azure Stack 中创建资源组中测试你的连接。

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>后续步骤

-  阅读的概述[在 Azure Stack 中的 Azure Stack PowerShell](azure-stack-powershell-overview.md)。
- 阅读有关[API 配置文件适用于 PowerShell](azure-stack-version-profiles.md) Azure Stack 中。
- 安装[Azure Stack Powershell](../operator/azure-stack-powershell-install.md)。
- 了解如何创建[Azure 资源管理器模板](azure-stack-develop-templates.md)云保持一致。
