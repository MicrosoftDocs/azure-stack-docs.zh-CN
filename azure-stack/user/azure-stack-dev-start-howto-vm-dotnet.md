---
title: 将C# ASP.NET web 应用部署到 Azure Stack 集线器中的虚拟机
description: 将C# ASP.NET web 应用部署到 Azure Stack 集线器中的 VM。
author: mattbriggs
ms.topic: overview
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 8222d102b22b05a3d5d1fd1a7530aeae62cc276d
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884918"
---
# <a name="deploy-a-c-aspnet-web-app-to-a-vm-in-azure-stack-hub"></a>将C# ASP.NET web 应用部署到 Azure Stack 集线器中的 VM

你可以创建虚拟机（VM）以在 Azure Stack 中心C#内托管 ASP.NET web 应用。 本文介绍了在设置服务器时应遵循的说明，将其配置为托管C# ASP.NET web 应用，然后从 Visual Studio 部署应用。

本文使用C# 6.0 应用，该应用使用在 Windows 2016 服务器上运行 ASP.NET Core 2.2。

## <a name="create-a-vm"></a>创建 VM

1. 创建[Windows SERVER VM](azure-stack-quick-windows-portal.md)。

1. 若要在 VM 上安装 IIS （带有管理控制台）和 ASP.NET 4.6 组件，请运行以下脚本：

    ```PowerShell  
    # Install IIS (with Management Console)
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Install ASP.NET 4.6
    Install-WindowsFeature Web-Asp-Net45
    
    # Install Web Management Service
    Install-WindowsFeature -Name Web-Mgmt-Service
    ```

1. 下载[Web 部署 3.6](https://www.microsoft.com/download/details.aspx?id=43717)版。 从 MSI 文件安装该文件，然后启用所有功能。

1. 在服务器上安装 .NET Core 2.2 托管捆绑包。 有关说明，请参阅[.Net Core 安装程序](https://dotnet.microsoft.com/download/dotnet-core/2.2)。 请确保在开发计算机和目标服务器上使用相同版本的 .NET Core。

1. 在 Azure Stack 集线器门户中，打开 VM 的 "网络设置" 中列出的端口。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 为租户打开 Azure Stack 中心门户。

    b.保留“数据库类型”设置，即设置为“共享”。 搜索你的 VM。 你可能已将 VM 固定到仪表板，或者可以在 "**搜索资源**" 框中搜索它。

    c. 选择“网络”。

    d.单击“下一步”。 选择 "在 VM 下**添加入站端口规则**"。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 为以下端口添加入站安全规则：

    | Port | 协议 | Description |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议（HTTP）是用于从服务器传递网页的协议。 客户端通过 HTTP 连接 DNS 名称或 IP 地址。 |
    | 443 | HTTPS | 超文本传输协议（HTTPS）是 HTTP 的安全版本，它需要安全证书，并允许加密传输信息。  |
    | 22 | SSH | 安全外壳（SSH）是一种用于安全通信的加密网络协议。 将使用此与 SSH 客户端的连接来配置 VM 和部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议允许远程桌面连接使用计算机的图形用户界面。   |
    | 8172 | 自定义 | WebDeploy 使用的端口。 |

    对于每个端口：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于 "**源**"，请选择**任何**。

    b.保留“数据库类型”设置，即设置为“共享”。 对于 "**源端口范围**"，键入一个星号（ **\*** ）。

    c. 对于 "**目标**"，请选择**任何**。

    d.单击“下一步”。 对于 "**目标端口范围**"，请添加要打开的端口。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 对于 "**协议**"，选择 "**任意**"。

    f. 对于“操作”，请选择“允许”。

    g. 对于 "**优先级**"，保留默认选择。

    h. 输入 "**名称**" 和 "**说明**"，以帮助你记住此端口处于打开状态的原因。

    i. 选择 **添加** 。

1.  在 Azure Stack Hub 中 VM 的**网络**设置中，为服务器创建 DNS 名称。 用户可以使用 URL 连接到你的网站。

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 为租户打开 Azure Stack 中心门户。

    b.保留“数据库类型”设置，即设置为“共享”。 搜索你的 VM。 你可能已将 VM 固定到仪表板，或者可以在 "**搜索资源**" 框中搜索它。

    c. 选择“概述”。

    d.单击“下一步”。 在 " **VM**" 下，选择 "**配置**"。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 对于 "**分配**"，选择 "**动态**"。

    f. 输入 DNS 名称标签（如**mywebapp**），以使完整 URL 成为 mywebapp. *p p. test-azurestack*。

## <a name="create-an-app"></a>创建应用 

你可以使用自己的 web 应用，也可以使用[Visual Studio 将 ASP.NET Core 应用发布到 Azure](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-2.2&tabs=visual-studio
)中的示例。 本文介绍如何使用 Visual Studio 2017 中的 Azure 虚拟机发布功能，创建 ASP.NET web 应用并将其发布到 Azure 虚拟机。 安装并确保你的应用在本地运行后，你需要将发布目标更新到 Azure Stack 中心实例中的 Windows VM。

## <a name="deploy-and-run-the-app"></a>部署和运行应用

在 Azure Stack 中心中的 VM 上创建一个发布目标。

1. 在**解决方案资源管理器**中，右键单击你的项目，然后选择 "**发布**"。

    ![将 ASP.NET web 应用部署到 Azure Stack 中心发布](media/azure-stack-dev-start-howto-vm-dotnet/deploy-app-to-azure-stack.png)

1. 在 "**发布**" 窗口中，选择 "**新建配置文件**"。
1. 选择 " **IIS"、"FTP" 等**。
1. 选择“发布”。
1. 对于 "**发布方法**"，选择**Web 部署**。
1. 对于 "**服务器**"，请输入前面定义的 DNS 名称，例如*w21902. p p. test-azurestack*。
1. 对于 "**网站名称**"，请输入 "**默认**网站"。
1. 对于 "**用户名**"，请输入计算机的用户名。
1. 对于 "**密码**"，请输入计算机的密码。
1. 对于 "**目标 url**"，请输入站点的 url，例如*mywebapp. p p. test-azurestack*。

    ![部署 ASP.NET web 应用-配置 Web 部署](media/azure-stack-dev-start-howto-vm-dotnet/configure-web-deploy.png)

1. 若要验证 web 部署配置，请选择 "**验证连接**"，然后选择 "**下一步**"。
1. 将**配置**设置为**Release**。
1. 将**目标框架**设置为**netcoreapp 2.2**。
1. 将**目标运行时**设置为**可移植**。
1. 选择“保存”。
1. 选择“发布”。
1. 中转到新服务器。 应会看到正在运行的 web 应用程序。

    ```http  
        mywebapp.local.cloudapp.azurestack.external
    ```

## <a name="next-steps"></a>后续步骤

- 了解如何[在 Azure Stack Hub 中设置开发环境](azure-stack-dev-start.md)。
- 了解[Azure Stack 集线器作为 IaaS 的常见部署](azure-stack-dev-start-deploy-app.md)。
- 若要了解C#编程语言并查找其他资源C#，请参阅[ C#指南](https://docs.microsoft.com/dotnet/csharp/)
