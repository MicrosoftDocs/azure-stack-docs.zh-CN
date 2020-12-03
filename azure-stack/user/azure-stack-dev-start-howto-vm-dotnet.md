---
title: '在 Azure Stack 集线器中将 c # ASP.NET web 应用部署到 VM'
description: 将 C# ASP.NET Web 应用部署到 Azure Stack Hub 中的 VM。
author: mattbriggs
ms.topic: overview
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 1ff00f354f9ab451bbf9557c1d63b01980d78875
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525400"
---
# <a name="deploy-a-c-aspnet-web-app-to-a-vm-in-azure-stack-hub"></a>将 C# ASP.NET Web 应用部署到 Azure Stack Hub 中的 VM

可以创建一个虚拟机 (VM) 来托管 Azure Stack Hub 中的 C# ASP.NET Web 应用。 本文介绍在设置服务器、将服务器配置为托管 C# ASP.NET Web 应用，然后直接从 Visual Studio 部署应用时可以遵照的说明。

本文使用一个 C# 6.0 应用，该应用使用在 Windows 2016 服务器上运行的 ASP.NET Core 2.2。

## <a name="create-a-vm"></a>创建 VM

1. 创建 [Windows Server VM](azure-stack-quick-windows-portal.md)。

1. 若要在 VM 上安装 IIS（通过管理控制台）和 ASP.NET 4.6 组件，请运行以下脚本：

    ```PowerShell  
    # Install IIS (with Management Console)
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Install ASP.NET 4.6
    Install-WindowsFeature Web-Asp-Net45
    
    # Install Web Management Service
    Install-WindowsFeature -Name Web-Mgmt-Service
    ```

1. 下载 [Web 部署 v3.6](https://www.microsoft.com/download/details.aspx?id=43717)。 从 MSI 文件完成安装，然后启用所有功能。

1. 在服务器上安装 .NET Core 2.2 Hosting Bundle。 有关说明，请参阅 [.NET Core 安装程序](https://dotnet.microsoft.com/download/dotnet-core/2.2)。 确保在开发计算机和目标服务器上使用相同版本的 .NET Core。

1. 在 Azure Stack Hub 门户中，打开 VM 的网络设置中列出的端口。

    a. 打开租户的 Azure Stack Hub 门户。

    b. 搜索你的 VM。 你可能已将 VM 固定到仪表板；或者，可以在“搜索资源”框中搜索该 VM。 

    c. 选择“网络”  。

    d. 选择 VM 下的“添加入站端口规则”。 

    e. 为以下端口添加入站安全规则：

    | 端口 | 协议 | 说明 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议 (HTTP) 是用于从服务器传递网页的协议。 客户端使用 DNS 名称或 IP 地址通过 HTTP 进行连接。 |
    | 443 | HTTPS | 安全超文本传输协议 (HTTPS) 是 HTTP 的安全版本，它需要一个安全证书，并允许对信息进行加密传输。  |
    | 22 | SSH | 安全外壳 (SSH) 是一种用于安全通信的加密网络协议。 你将在 SSH 客户端上使用此连接来配置 VM 并部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议允许远程桌面连接使用计算机的图形用户界面。   |
    | 8172 | 自定义 | WebDeploy 使用的端口。 |

    对于每个端口：

    a. 对于“源”，请选择“任何”。  

    b. 对于 " **源端口范围**"，请键入星号 ( * *\** _) 。

    c. 对于 _ * Destination * *，请选择 **任何**。

    d. 对于“目标端口范围”，请添加要打开的端口。 

    e. 对于“协议”，请选择“任何”。  

    f. 对于“操作”，请选择“允许”。  

    g. 对于“优先级”，请保留默认选项。 

    h. 输入 **名称** 和 **说明**，以帮助记住打开端口的原因。

    i. 选择 **添加** 。

1.  对于 Azure Stack Hub 中的 VM，在“网络”设置中创建服务器的 DNS 名称。  用户可以使用 URL 连接到你的网站。

    a. 打开租户的 Azure Stack Hub 门户。

    b. 搜索你的 VM。 你可能已将 VM 固定到仪表板；或者，可以在“搜索资源”框中搜索该 VM。 

    c. 选择“概述”。 

    d. 在“VM”下选择“配置”。  

    e. 对于“分配”，请选择“动态”。  

    f. 输入 DNS 名称标签（例如 **mywebapp**），因此，完整的 URL 将是 *mywebapp.local.cloudapp.azurestack.external*。

## <a name="create-an-app"></a>创建应用 

可以使用你自己的 Web 应用，或使用[使用 Visual Studio 将 ASP.NET Core 应用发布到 Azure](/aspnet/core/tutorials/razor-pages/razor-pages-start?tabs=visual-studio&view=aspnetcore-2.2) 中的示例。 本文将介绍如何使用 Visual Studio 2017 中的 Azure 虚拟机发布功能，创建一个 ASP.NET Web 应用并将其发布到 Azure 虚拟机。 安装应用并确保它在本地运行后，将发布目标更新为 Azure Stack Hub 实例中的 Windows VM。

## <a name="deploy-and-run-the-app"></a>部署和运行应用

为 Azure Stack Hub 中的 VM 创建发布目标。

1. 在“解决方案资源管理器”  中，右键单击项目，并选择“发布”  。

    ![将 ASP.NET Web 应用部署到 Azure Stack Hub 发布](media/azure-stack-dev-start-howto-vm-dotnet/deploy-app-to-azure-stack.png)

1. 在“发布”窗口中选择“新建配置文件”。  
1. 选择“IIS”、“FTP”等  。
1. 选择“发布”  。
1. 对于“发布方法”，请选择“Web 部署”。  
1. 对于“服务器”，请输入前面定义的 DNS 名称，例如 *w21902.local.cloudapp.azurestack.external*。
1. 对于“站点名称”，请输入“默认网站”。  
1. 对于“用户名”，请输入计算机的用户名。 
1. 对于“密码”，请输入计算机的密码。 
1. 对于“目标 URL”，请输入站点的 URL，例如 *mywebapp.local.cloudapp.azurestack.external*。

    ![部署 ASP.NET Web 应用 - 配置 Web 部署](media/azure-stack-dev-start-howto-vm-dotnet/configure-web-deploy.png)

1. 若要验证 Web 部署配置，请选择“验证连接”，然后选择“下一步”。  
1. 将“配置”设置为“发布”。  
1. 将“目标框架”设置为“netcoreapp2.2”。  
1. 将“目标运行时”设置为“便携式”。  
1. 选择“保存”。 
1. 选择“发布”  。
1. 转到新服务器。 应会看到你的 Web 应用程序正在运行。

    ```http  
        mywebapp.local.cloudapp.azurestack.external
    ```

## <a name="next-steps"></a>后续步骤

- 了解如何[在 Azure Stack Hub 中设置开发环境](azure-stack-dev-start.md)。
- 了解[用作 IaaS 的 Azure Stack Hub 的常见部署](azure-stack-dev-start-deploy-app.md)。
- 若要了解 C# 编程语言和查找有关 C# 的其他资源，请参阅 [C# 指南](/dotnet/csharp/)
