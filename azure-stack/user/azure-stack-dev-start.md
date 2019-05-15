---
title: 设置 Azure Stack 中的开发环境 |Microsoft Docs
description: 开始开发适用于 Azure Stack 的应用程序。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 06c462ab46ce6bbae8d5c3bd6fcb757e14417edf
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617632"
---
# <a name="set-up-a-development-environment-in-azure-stack"></a>设置 Azure Stack 中的开发环境 

可以通过使用 Windows 10、 Linux 或 macOS 工作站开发适用于 Azure Stack 的应用程序。 本文介绍： 

- 在 Azure Stack 中运行您的应用程序的各种上下文。 
- 使用 Windows 10、 Linux 或 macOS 工作站，为你要遵循的步骤设置。 
- 在 Azure Stack 中创建资源的步骤并将其部署到应用程序。 

## <a name="azure-stack-context-and-your-code"></a>Azure Stack 上下文和你的代码 

您可以编写脚本和应用程序完成在 Azure Stack 中的许多任务。 但是，很有帮助你将范围限制为以下三种模式： 

1. 在第一个模式下，你可以创建通过使用 Azure 资源管理器模板设置 Azure Stack 中的资源的应用。 例如，可以编写构造又创建虚拟网络和将承载您的应用程序的 Vm 的 Azure 资源管理器模板的脚本。 

2. 在第二个模式下，可以直接使用终结点通过使用 REST API 和 REST 客户端在代码中创建的。 在此模式下，会编写这个脚本可通过 api 发送请求来创建虚拟网络和 Vm。 

3. 在第三个模式下，可以使用你的代码在 Azure Stack 中创建托管的应用。 用于承载您的应用程序在 Azure Stack 中创建基础结构后，您将您的应用程序部署到基础结构。 正常情况下，将准备您的环境，然后将您的应用程序部署到它。 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>基础结构即服务和平台即服务 

作为一种云平台产品，Azure Stack 支持： 

- 基础结构即服务 (IaaS) 
- 平台即服务 (PaaS) 

IaaS 和 PaaS 通知如何设置开发计算机。 

IaaS 是来自网络设备、 网络和服务器的部分的数据中心虚拟化。 当应用部署到托管的 web 服务器的 VM 时，您在 IaaS 模型中工作。 在此模型中，Azure Stack 管理虚拟齿轮，并在虚拟服务器上为您的应用程序。 Azure Stack 资源提供程序支持的网络组件和虚拟服务器。 

PaaS 抽象化基础结构层，以便将您的应用程序部署到的终结点，然后运行该应用程序。 在 PaaS 模型，您可以使用容器托管你的应用，然后将容器化的应用部署到运行容器的服务。 或者，可能会将您的应用程序推送直接到运行的应用程序的服务。 Azure Stack 可用于运行 Azure 应用服务和 Kubernetes。 

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manager 

这三个以前提到过模式，以及 PaaS 或 IaaS，启用 Azure Stack 版本的 Azure 资源管理器。 此管理框架，可部署、 管理和监视 Azure Stack 资源。 它允许您处理的资源作为单个操作中的组。 有关使用 Azure Stack 资源管理器的详细信息，请参阅[在 Azure Stack 中的管理 API 版本配置文件](azure-stack-version-profiles.md)。 

### <a name="azure-stack-sdks"></a>Azure Stack Sdk 

Azure Stack 使用 Azure 资源管理器的 Azure Stack 版本。 为了帮助你通过使用所选的代码中处理与 Azure Stack 资源管理器中，我们提供了大量的 Sdk，包括： 

- [.NET/C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)

## <a name="before-you-start"></a>开始之前 

在开始启动你的环境设置之前，需要： 

- 对 Azure Stack 用户门户的访问。 
- 租户的名称。 
- 若要确定您是否在使用 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 作为标识管理器。 

如果您有关于 Azure Stack 的任何问题，请联系云操作员。 

## <a name="windows-10"></a>Windows 10 

如果您使用的 Windows 10 计算机，可以使用 PowerShell 5.0 和 Visual Studio。 如果您正在使用 Azure Stack 开发工具包 (ASDK)，您可以连接到您的 VPN 连接的环境。 

### <a name="set-up-your-tools"></a>设置你的工具 

1. 获取设置使用 PowerShell。 有关说明，请参阅[安装 Azure Stack Powershell](../operator/azure-stack-powershell-install.md)。 

2. 下载 Azure Stack 工具。 有关说明，请参阅[从 GitHub 下载 Azure Stack 工具](../operator/azure-stack-powershell-download.md)。 

3. 如果使用 ASDK，安装和配置[到 Azure Stack 的 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)。 

4. 安装和配置 Azure CLI。 有关说明，请参阅[使用 Azure CLI 在 Azure Stack 中使用 API 版本配置文件](azure-stack-version-profiles-azurecli2.md)。 

5. 安装和配置 Azure 存储资源管理器。 存储资源管理器是一款独立应用，可用于处理 Azure Stack 存储数据。 有关说明，请参阅[连接存储资源管理器到 Azure Stack 订阅或存储帐户](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成的开发环境 

1. 安装集成的开发环境 (IDE)，具体取决于你的基本代码和首选项。 

     - Visual Studio Code （Python、 Go、 NodeJS）。 通过在计算机下载 Visual Studio Code [code.visualstudio.com](https://code.visualstudio.com/Download)。 
     - Visual Studio (.NET /C#)。 下载 Visual Studio Community edition [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)。 
     - Eclipse (Java)。 下载从 Eclipse [eclipse.org](https://www.eclipse.org/downloads/)。 

2. 安装适用于你的代码的 SDK: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

如果使用 Linux 计算机，可以使用 Azure CLI、 Visual Studio Code 中或你自己青睐的集成的开发环境。 

> [!Note]   
> 如果与 ASDK 配合使用的 Linux 计算机，在远程计算机必须是与 ASDK 位于同一网络中。 你将无法使用虚拟专用网络连接进行连接。 

### <a name="set-up-your-tools"></a>设置你的工具 

1. 安装和配置 Azure CLI。 有关说明，请参阅[使用 Azure CLI 在 Azure Stack 中使用 API 版本配置文件](azure-stack-version-profiles-azurecli2.md)。 

2. 安装和配置 Azure 存储资源管理器。 存储资源管理器是一款独立应用，可用于处理 Azure Stack 存储数据。 有关说明，请参阅[连接存储资源管理器到 Azure Stack 订阅或存储帐户](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成的开发环境 

1. 安装集成的开发环境 (IDE)，具体取决于你的基本代码和首选项。 

     - Visual Studio Code （Python、 Go、 NodeJS）。 通过在计算机下载 Visual Studio Code [code.visualstudio.com](https://code.visualstudio.com/Download)。 
     - Visual Studio (.NET /C#)。 下载 Visual Studio Community edition [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)。 
     - Eclipse (Java)。 下载从 Eclipse [eclipse.org](https://www.eclipse.org/downloads/)。 

2. 安装适用于你的代码的 SDK: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

MacOS 计算机将使您可以使用 Azure CLI 和 Visual Studio Code 中，或你自己首选集成开发环境。 

> [!Note]   
> 如果与 ASDK 配合使用 macOS 计算机，在远程计算机必须是与 ASDK 位于同一网络中。 你将无法使用虚拟专用网络连接进行连接。 

### <a name="set-up-your-tools"></a>设置你的工具 

1. 安装和配置 Azure CLI。 有关说明，请参阅[使用 Azure CLI 在 Azure Stack 中使用 API 版本配置文件](azure-stack-version-profiles-azurecli2.md)。 

2. 安装和配置 Azure 存储资源管理器。 存储资源管理器是一款独立应用，可用于处理 Azure Stack 存储数据。 有关说明，请参阅[连接存储资源管理器到 Azure Stack 订阅或存储帐户](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成的开发环境 

1. 安装集成的开发环境 (IDE)，具体取决于你的基本代码和首选项。 

     - Visual Studio Code （Python、 Go、 NodeJS）。 通过在计算机下载 Visual Studio Code [code.visualstudio.com](https://code.visualstudio.com/Download)。 
     - Visual Studio (.NET /C#)。 下载 Visual Studio Community edition [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)。 
     - Eclipse (Java)。 下载从 Eclipse [eclipse.org](https://www.eclipse.org/downloads/)。 

2. 安装适用于你的代码的 SDK: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md)
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>后续步骤 

若要将应用部署到 Azure Stack 中的资源，请参阅[适用于 Azure Stack 的常见部署](azure-stack-dev-start-deploy-app.md)。
