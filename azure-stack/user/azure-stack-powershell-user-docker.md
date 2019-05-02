---
title: 使用 Docker 来运行适用于 Azure Stack 的 PowerShell |Microsoft Docs
description: 使用 Docker 在 Azure Stack 上运行 PowerShell
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
ms.openlocfilehash: 0eacd2c5a058bca68e86f2d34df8d3cc91987c1c
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985542"
---
# <a name="use-docker-to-run-powershell"></a>使用 Docker 来运行 PowerShell

可以使用 Docker 来创建基于 Windows 的容器以在其运行的特定版本的 PowerShell 所需的不同的接口。 必须在 Docker 中使用基于 Windows 的容器。

## <a name="docker-prerequisites"></a>Docker 先决条件

1. 安装[Docker](https://docs.docker.com/install/)。
2. 打开命令行，例如 Powershell 或 Bash，然后键入：

    ```bash
        Docker -version
    ```

3. 需要运行 Docker 使用需要 Windows 10 的 Windows 容器。 当运行 Docker，切换到 Windows 容器。

4. 您必须从计算机加入到与 Azure Stack 相同的域中运行 Docker。 如果使用 ASDK，需要安装[在远程计算机上的 VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)。

## <a name="service-principals-for-using-powershell"></a>使用 PowerShell 的服务主体

若要使用 PowerShell 为 Azure Stack 中访问资源在 Azure AD 租户中需要服务主体。 委托的权限与用户的基于角色的访问控制。

1. 按照文章中的说明设置你主体[通过创建服务主体向 Azure Stack 资源的应用程序访问](azure-stack-create-service-principals.md)。
2. 请记下的应用程序 ID、 密钥和租户 id。

## <a name="docker---azure-stack-api-profiles-module"></a>Docker-Azure Stack API 配置文件模块

Dockerfile 将打开 Microsoft 映像 microsoft/windowsservercore，其中已安装的 Windows PowerShell 5.1。 该文件，然后加载 NuGet，Azure Stack PowerShell 模块，并从 Azure Stack 工具下载这些工具。

1. 此存储库以 zip 格式下载或克隆存储库：  
[https://github.com/mattbriggs/azure-stack-powershell](https://github.com/mattbriggs/azure-stack-powershell)

2. 从你的终端中打开存储库文件夹。

3. 在您的存储库和类型中打开一个命令行接口：

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. 生成映像后，可以启动交互式容器。 键入：

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. 在 shell 便可供你 cmdlet。

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. 连接到 Azure Stack 使用服务主体。 现在在 Docker 中使用 PowerShell 提示符。 

    ```Powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell 将返回你帐户的对象：

    ```PowerShell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. 通过在 Azure Stack 中创建资源组中测试你的连接。

    ```PowerShell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>后续步骤

-  阅读的概述[Azure Stack 上的 Azure Stack PowerShell](azure-stack-powershell-overview.md)。
- 阅读有关[API 配置文件适用于 PowerShell](azure-stack-version-profiles.md) Azure Stack 上。
- [安装 Azure Stack Powershell](../operator/azure-stack-powershell-install.md)。
- 了解如何创建[Azure 资源管理器模板](azure-stack-develop-templates.md)云保持一致。