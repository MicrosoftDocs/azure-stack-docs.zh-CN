---
title: 部署C#到 Azure Stack 中的虚拟机的 ASP.Net web 应用 |Microsoft Docs
description: 部署C#ASP.net web 应用到 Azure Stack 中的 VM。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 3f925d7c6a7f08257dbcb054044403e1488d38cb
ms.sourcegitcommit: 41927cb812e6a705d8e414c5f605654da1fc6952
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2019
ms.locfileid: "64482123"
---
# <a name="how-to-deploy-a-c-aspnet-web-app-to-a-vm-in-azure-stack"></a>如何部署C#ASP.net web 应用到 Azure Stack 中的 VM

可以创建一个到主机的虚拟机在C#在 Azure Stack 中的 (ASP.NET) Web 应用。 本文探讨您需要遵循在服务器上，将设置配置服务器到主机的步骤在C#(ASP.NET) web 应用，并将部署您的应用程序直接从 Visual Studio。

C#是通用的多模式编程语言包含强类型化、 从词法上限定了作用域、 命令式、 声明性、 功能、 泛型、 面向对象的和面向组件的编程领域。 若要了解C#编程语言和查找的其他资源C#，请参阅[C#指南](https://docs.microsoft.com/dotnet/csharp/)。

本文将使用C#6.0 应用程序使用 Windows 2016 服务器上运行的 ASP.NET Core 2.2。

## <a name="create-a-vm"></a>创建 VM

1. 创建[Windows Server VM](azure-stack-quick-windows-portal.md)。

2. 运行以下脚本以在 VM 上安装组件。 脚本：
      - 安装 IIS （使用管理控制台）。
      - 安装 ASP.NET 4.6。

        ```PowerShell  
        # Install IIS (with Management Console)
        Install-WindowsFeature -name Web-Server -IncludeManagementTools
        
        # Install ASP.NET 4.6
        Install-WindowsFeature Web-Asp-Net45
        
        # Install Web Management Service
        Install-WindowsFeature -Name Web-Mgmt-Service
        ```

3. 下载[MSI 用于 Web 部署 3.6](https://www.microsoft.com/download/details.aspx?id=43717)。 从 MSI 安装，然后启用上的所有功能。

4. 在服务器上安装.NET Core 托管捆绑包为 2.2。 有关步骤，请参阅[.NET Core 安装程序](https://dotnet.microsoft.com/download/dotnet-core/2.2)。 请确保在开发计算机和目标服务器上使用相同版本的.NET Core。

5. 返回到 Azure Stack 门户中，并在你的 VM 网络设置中打开的端口。

    1. 打开你的租户 Azure Stack 门户。
    2. 查找你的 VM。 你可能具有固定 VM 到你的仪表板，或者，可以搜索中的 vm**搜索资源**框。
    3. 选择“网络”。
    4. 选择**添加入站的端口规则**VM 下。
    1. 添加以下端口的入站的安全规则：

    | 端口 | Protocol | 描述 |
    | --- | --- | --- |
    | 80 | HTTP | 超文本传输协议 (HTTP) 是分布式、 协作式和超媒体信息系统应用程序协议。 客户端将连接到 web 应用使用的公共 IP 或 DNS 名称的 VM。 |
    | 443 | HTTPS | 安全超文本传输协议 (HTTPS) 是一个扩展的超文本传输协议 (HTTP)。 它用于通过计算机网络的安全通信。 客户端将连接到 web 应用使用的公共 IP 或 DNS 名称的 VM。 |
    | 22 | SSH | 安全外壳 (SSH) 是加密的网络协议进行安全地运行网络服务，通过不安全的网络。 要将此连接用于 SSH 客户端将 VM 配置和部署应用。 |
    | 3389 | RDP | 可选。 远程桌面协议允许使用图形用户界面的远程桌面连接你的计算机。   |

    对于每个端口：

    1. 选择**任何**源。
    1. 类型`*`针对源端口范围。
    1. 选择**任何**有关**目标**。
    1. 添加你想要打开的端口**目标端口范围**。
    1. 选择**任何**有关**协议**。
    1. 选择**允许**有关**操作**。
    1. 保留为默认值**优先级**。
    1. **名称**，并提供**说明**，以便可以记下打开该端口的原因。
    1. 选择添加。

5.  在中**网络**VM 在 Azure Stack 中，设置创建你的服务器的 DNS 名称。 用户可以连接到您使用的 URL 的网站。

    1. 打开你的租户 Azure Stack 门户。
    1. 查找你的 VM。 你可能具有固定 VM 到你的仪表板，或者，可以搜索中的 vm**搜索资源**框。
    1. 选择“概述”。
    1. 选择**配置**VM 下。
    1. 选择**动态**有关**分配**。
    1. 键入 DNS 名称标签，如`mywebapp`，以便你的完整 URL 将类似于： `mywebapp.local.cloudapp.azurestack.external`。

## <a name="create-an-app"></a>创建应用 

可以使用 Web 应用程序也可以使用在示例[将 ASP.NET Core 应用发布到 Azure 中使用 Visual Studio](https://docs.microsoft.com/aspnet/core/tutorials/razor-pages/razor-pages-start?view=aspnetcore-2.2&tabs=visual-studio
)。

本文介绍如何创建和发布到 Azure 虚拟机 (VM) 在 Visual Studio 2017 中使用 Microsoft Azure 虚拟机发布功能的 ASP.NET web 应用程序。 已安装并确保您的应用程序在本地运行后，你将在 Azure Sack 到 Windows VM 更新发布目标。

## <a name="deploy-and-run-the-app"></a>部署和运行应用

在 Azure Stack 中创建发布目标到 VM。

1. 右键单击解决方案资源管理器中的项目并选择**发布**。

    ![ASP.NET web 应用部署到 Azure Stack 发布](media/azure-stack-dev-start-howto-vm-dotnet/deploy-app-to-azure-stack.png)

2.  在发布窗口中，选择新的配置文件。
3. 选择 IIS、 FTP 等。
4. 选择发布。

5.  选择**Webdeploy**有关**发布方法**。
6.  有关**Server**输入前面定义的 DNS 名称如 `w21902.local.cloudapp.azurestack.external`
7.  有关**站点名称**类型`Default Web Site`。
8.  有关**用户名**键入计算机的用户名。
9.  有关**密码**，键入计算机的密码。
10. 有关**目标 URL**键入站点的 URL，例如`mywebapp.local.cloudapp.azurestack.external`。

    ![部署 ASP.NET web 应用-配置 Web 部署](media/azure-stack-dev-start-howto-vm-dotnet/configure-web-deploy.png)

9. 选择**验证连接**来验证你的 web 部署配置。 然后单击**下一步**。
10. 设置你**配置**作为**发行**。
11. 设置**框架为目标**作为**netcoreapp2.2**。
12. 设置**面向运行时**作为**可移植**。
13. 选择“保存”。
14. 选择“发布”。
15. 导航到新服务器。 应会看到正在运行的 web 应用程序。

```HTTP  
    mywebapp.local.cloudapp.azurestack.external
```

## <a name="next-steps"></a>后续步骤

- 了解有关如何对[开发适用于 Azure Stack](azure-stack-dev-start.md)
- 了解如何[IaaS 作为 Azure Stack 的常见部署](azure-stack-dev-start-deploy-app.md)。