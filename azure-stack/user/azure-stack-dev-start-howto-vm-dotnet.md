---
title: 部署C#ASP.NET web 应用程序到 Azure Stack 中的虚拟机 |Microsoft Docs
description: 部署C#ASP.NET web 应用到 Azure Stack 中的 VM。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 14baf5d5ca411e7c32cbfcf4a6138193a2215b0a
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783082"
---
# <a name="deploy-a-c-aspnet-web-app-to-a-vm-in-azure-stack"></a>部署C#ASP.NET web 应用到 Azure Stack 中的 VM

可以创建虚拟机 (VM) 以托管应用C#在 Azure Stack 中的 ASP.NET web 应用。 本文讨论在设置您的服务器时应遵循的说明将其配置为承载在C#ASP.NET web 应用，然后将部署应用程序直接从 Visual Studio。

本文使用C#6.0 使用 ASP.NET Core 2.2 Windows 2016 服务器上运行的应用。

## <a name="create-a-vm"></a>创建 VM

1. 创建[Windows Server VM](azure-stack-quick-windows-portal.md)。

1. 若要在 VM 上安装的 （使用管理控制台） 的 IIS 和 ASP.NET 4.6 组件，请运行以下脚本：

    ```PowerShell  
    # Install IIS (with Management Console)
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Install ASP.NET 4.6
    Install-WindowsFeature Web-Asp-Net45
    
    # Install Web Management Service
    Install-WindowsFeature -Name Web-Mgmt-Service
    ```

1. 下载[Web 部署 v3.6](https://www.microsoft.com/download/details.aspx?id=43717)。 从 MSI 文件安装它，然后启用的所有功能。

1. 在服务器上安装.NET Core 2.2 托管捆绑包。 有关说明，请参阅[.NET Core 安装程序](https://dotnet.microsoft.com/download/dotnet-core/2.2)。 请确保在开发计算机和目标服务器上使用相同版本的.NET Core。

1. 在 Azure Stack 门户中，打开你的 VM 的网络设置中列出的端口。

    a. 打开你的租户 Azure Stack 门户。

    b. 搜索你的 VM。 你可能具有固定 VM 到你的仪表板，或者，可以搜索中为其**搜索资源**框。

    c. 选择“网络”。

    d. 选择**添加入站的端口规则**VM 下。

    e. 添加以下端口的入站的安全规则：

    | Port | Protocol | 描述 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议 (HTTP) 是用于从服务器提供网页的协议。 客户端通过 HTTP 进行连接的 DNS 名称或 IP 地址。 |
    | 443 | HTTPS | 超文本传输协议安全 (HTTPS) 是 HTTP 的一个安全的需要的安全证书，并允许加密传输信息版本。  |
    | 22 | SSH | 安全外壳 (SSH) 是安全通信的加密的网络协议。 要将此连接用于 SSH 客户端将 VM 配置和部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议允许使用图形用户界面的远程桌面连接你的计算机。   |
    | 8080 | 自定义 | Apache Tomcat 服务的默认端口为 8080。 对于生产服务器，要在通过路由流量 80 和 443。 |

    对于每个端口：

    a. 有关**源**，选择**任何**。

    b. 有关**源端口范围**，键入一个星号 (**\***)。

    c. 有关**目标**，选择**任何**。

    d. 有关**目标端口范围**，添加你想要打开的端口。

    e. 有关**协议**，选择**任何**。

    f. 对于“操作”，请选择“允许”。

    g. 有关**优先级**，保留默认选择。

    h. 输入**名称**并**说明**以帮助您记起为什么的端口已打开。

    i. 选择 **添加** 。

1.  在中**网络**VM 在 Azure Stack 中，设置创建你的服务器的 DNS 名称。 用户可以连接到你的网站使用的 URL。

    a. 打开你的租户 Azure Stack 门户。

    b. 搜索你的 VM。 你可能具有固定 VM 到你的仪表板，或者，可以搜索中为其**搜索资源**框。

    c. 选择“概述”。

    d. 下**VM**，选择**配置**。

    e. 有关**赋值**，选择**动态**。

    f. 输入 DNS 名称标签，如**mywebapp**，以便将完整 URL 将变为*mywebapp.local.cloudapp.azurestack.external*。

## <a name="create-an-app"></a>创建应用 

可以使用 web 应用或处的示例[将 ASP.NET Core 应用发布到 Azure 中使用 Visual Studio](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-2.2&tabs=visual-studio
)。 本文介绍如何创建和使用 Visual Studio 2017 中的 Azure 虚拟机发布功能将 ASP.NET web 应用发布到 Azure 虚拟机。 已安装，并确保您的应用程序在本地运行后，将更新发布目标到 Windows VM 使用，在 Azure Stack 实例中。

## <a name="deploy-and-run-the-app"></a>部署和运行应用

在 Azure Stack 中创建发布目标到 VM。

1. 在中**解决方案资源管理器**，右键单击项目，并选择**发布**。

    ![将 ASP.NET web 应用部署到 Azure Stack 发布](media/azure-stack-dev-start-howto-vm-dotnet/deploy-app-to-azure-stack.png)

1. 在中**发布**窗口中，选择**新的配置文件**。
1. 选择**IIS**， **FTP**，依次类推。
1. 选择“发布”。
1. 有关**发布方法**，选择**Web 部署**。
1. 有关**服务器**输入之前，定义的 DNS 名称如*w21902.local.cloudapp.azurestack.external*。
1. 有关**站点名称**，输入**Default Web Site**。
1. 有关**用户名**，输入在计算机的用户名称。
1. 有关**密码**，输入在计算机的密码。
1. 有关**目标 URL**，输入站点 URL，如*mywebapp.local.cloudapp.azurestack.external*。

    ![部署 ASP.NET web 应用-配置 Web 部署](media/azure-stack-dev-start-howto-vm-dotnet/configure-web-deploy.png)

1. 若要验证你 web 部署配置中，选择**验证连接**，然后选择**下一步**。
1. 设置**配置**作为**发行**。
1. 设置**框架为目标**作为**netcoreapp2.2**。
1. 设置**面向运行时**作为**可移植**。
1. 选择“保存”。
1. 选择“发布”。
1. 请转到新服务器。 应会看到正在运行的 web 应用程序。

    ```http  
        mywebapp.local.cloudapp.azurestack.external
    ```

## <a name="next-steps"></a>后续步骤

- 了解如何[设置 Azure Stack 中的开发环境](azure-stack-dev-start.md)。
- 了解如何[IaaS 作为 Azure Stack 的常见部署](azure-stack-dev-start-deploy-app.md)。
- 若要了解C#编程语言和查找的其他资源C#，请参阅[C#指南](https://docs.microsoft.com/dotnet/csharp/)
