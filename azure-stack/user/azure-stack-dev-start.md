---
title: 在 Azure Stack 中设置开发环境 |Microsoft Docs
description: 开始开发适用于 Azure Stack 的应用程序。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 07/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 07/30/2019
ms.openlocfilehash: 404f6b889f921e547f8c44c7b4219b232819c322
ms.sourcegitcommit: 7961fda0bfcdd3db8cf94a8c405b5c23a23643af
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68616460"
---
# <a name="set-up-a-development-environment-in-azure-stack"></a>在 Azure Stack 中设置开发环境 

可以使用 Windows 10、Linux 或 macOS 工作站开发 Azure Stack 的应用程序。 本文介绍： 

- 在 Azure Stack 中运行应用的各种上下文。 
- 为您设置 Windows 10、Linux 或 macOS 工作站所要遵循的步骤。 
- 在 Azure Stack 中创建资源并将其部署到应用的步骤。 

## <a name="azure-stack-context-and-your-code"></a>Azure Stack 上下文和代码 

你可以编写脚本和应用来完成 Azure Stack 中的多个任务。 但是, 将范围限制为以下三种模式会很有帮助: 

1. 在第一种模式中, 可以使用 Azure 资源管理器模板创建在 Azure Stack 中预配资源的应用。 例如, 你可以编写一个脚本来构造 Azure 资源管理器模板, 该模板将创建一个虚拟网络和承载你的应用程序的 Vm。 

2. 在第二种模式下, 你可以使用 REST API 和在代码中创建的 REST 客户端直接使用终结点。 在此模式下, 你将编写一个脚本, 用于通过将请求发送到 Api 来创建虚拟网络和 Vm。 

3. 在第三种模式下, 你可以使用代码创建在 Azure Stack 中托管的应用。 在 Azure Stack 中创建用于托管应用程序的基础结构后, 将应用部署到基础结构。 通常, 你将准备好环境, 然后将你的应用部署到该环境中。 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>基础结构即服务和平台即服务 

作为云平台产品, Azure Stack 支持两者: 

- 基础结构即服务 (IaaS) 
- 平台即服务 (PaaS) 

IaaS 和 PaaS 都通知如何设置开发计算机。 

IaaS 是来自网络设备、网络和服务器的数据中心部分的虚拟化。 当你将应用程序部署到承载 web 服务器的 VM 时, 你正在使用 IaaS 模型。 在此模型中, Azure Stack 管理虚拟设备, 你的应用程序位于虚拟服务器上。 Azure Stack 资源提供程序支持网络组件和虚拟服务器。 

PaaS 将基础结构层抽象化, 以便将应用部署到运行该应用的终结点。 在 PaaS 模型中, 你可以使用容器来托管应用, 然后将容器化应用部署到运行容器的服务。 或者, 你可以将应用程序直接推送到运行该应用程序的服务。 您可以使用 Azure Stack 运行 Azure App Service 和 Kubernetes。 

### <a name="azure-stack-resource-manager"></a>Azure Stack 资源管理器 

前面提到的三种模式以及 PaaS 或 IaaS 均由 Azure 资源管理器 Azure Stack 版本启用。 此管理框架允许您部署、管理和监视 Azure Stack 资源。 它使你可以在单个操作中以组的形式处理资源。 有关使用 Azure Stack 资源管理器的详细信息, 请参阅[Azure Stack 中的管理 API 版本配置文件](azure-stack-version-profiles.md)。 

### <a name="azure-stack-sdks"></a>Azure Stack Sdk 

Azure Stack 使用 Azure 资源管理器的 Azure Stack 版本。 为了帮助你使用你的选择代码来处理 Azure Stack 资源管理器, 我们提供了许多 Sdk, 其中包括: 

- [.NET/C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)
- [Node.js](azure-stack-version-profile-nodejs.md)

## <a name="before-you-start"></a>开始之前 

在开始设置环境之前, 需要: 

- 访问 Azure Stack 用户门户。 
- 租户的名称。 
- 若要确定是否使用 Azure Active Directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 作为标识管理器。 

如果对 Azure Stack 有任何疑问, 请与云操作员联系。 

## <a name="windows-10"></a>Windows 10 

如果使用的是 Windows 10 计算机, 则可使用 PowerShell 5.0 和 Visual Studio。 如果使用的是 Azure Stack 开发工具包 (ASDK), 则可以使用 VPN 连接来连接到环境。 

### <a name="set-up-your-tools"></a>设置工具 

1. 使用 PowerShell 进行设置。 有关说明, 请参阅[Install Azure Stack Powershell](../operator/azure-stack-powershell-install.md)。 

2. 下载 Azure Stack 工具。 有关说明, 请参阅[从 GitHub 下载 Azure Stack 工具](../operator/azure-stack-powershell-download.md)。 

3. 如果使用的是 ASDK, 请安装和配置与[Azure Stack 的 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)。 

4. 安装并配置 Azure CLI。 有关说明, 请参阅[在 Azure Stack 中使用 Azure CLI 的 API 版本配置文件](azure-stack-version-profiles-azurecli2.md)。 

5. 安装并配置 Azure 存储资源管理器。 存储资源管理器是一款独立应用, 可用于处理 Azure Stack 存储数据。 有关说明, 请参阅[将存储资源管理器连接到 Azure Stack 订阅或存储帐户](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成开发环境 

1. 根据代码基和偏好安装集成开发环境 (IDE)。 

     - Visual Studio Code（Python、Go、NodeJS）。 从 [code.visualstudio.com](https://code.visualstudio.com/Download) 下载适用于你的计算机的 Visual Studio Code。 
     - Visual Studio (.NET/C#)。 从 [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/) 下载 Visual Studio Community Edition。 
     - Eclipse (Java)。 从 [eclipse.org](https://www.eclipse.org/downloads/) 下载 Eclipse。 

2. 为代码安装 SDK: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

如果你使用的是 Linux 计算机, 则可以使用 Azure CLI、Visual Studio Code 或你自己的首选集成开发环境。 

> [!Note]   
> 如果你使用的是具有 ASDK 的 Linux 计算机, 则远程计算机必须与 ASDK 在同一网络中。 你将无法使用虚拟专用网络连接进行连接。 

### <a name="set-up-your-tools"></a>设置工具 

1. 安装并配置 Azure CLI。 有关说明, 请参阅[在 Azure Stack 中使用 Azure CLI 的 API 版本配置文件](azure-stack-version-profiles-azurecli2.md)。 

2. 安装并配置 Azure 存储资源管理器。 存储资源管理器是一款独立应用, 可用于处理 Azure Stack 存储数据。 有关说明, 请参阅[将存储资源管理器连接到 Azure Stack 订阅或存储帐户](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成开发环境 

1. 根据代码基和偏好安装集成开发环境 (IDE)。 

     - Visual Studio Code（Python、Go、NodeJS）。 从 [code.visualstudio.com](https://code.visualstudio.com/Download) 下载适用于你的计算机的 Visual Studio Code。 
     - Visual Studio (.NET/C#)。 从 [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/) 下载 Visual Studio Community Edition。 
     - Eclipse (Java)。 从 [eclipse.org](https://www.eclipse.org/downloads/) 下载 Eclipse。 

2. 为代码安装 SDK: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

使用 macOS 计算机, 你可以使用 Azure CLI、Visual Studio Code 或你自己的首选集成开发环境。 

> [!Note]   
> 如果要将 macOS 计算机与 ASDK 配合使用, 则远程计算机需要与 ASDK 位于同一网络中。 你将无法使用虚拟专用网络连接进行连接。 

### <a name="set-up-your-tools"></a>设置工具 

1. 安装并配置 Azure CLI。 有关说明, 请参阅[在 Azure Stack 中使用 Azure CLI 的 API 版本配置文件](azure-stack-version-profiles-azurecli2.md)。 

2. 安装并配置 Azure 存储资源管理器。 存储资源管理器是一款独立应用, 可用于处理 Azure Stack 存储数据。 有关说明, 请参阅[将存储资源管理器连接到 Azure Stack 订阅或存储帐户](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成开发环境 

1. 根据代码基和偏好安装集成开发环境 (IDE)。 

     - Visual Studio Code（Python、Go、NodeJS）。 从 [code.visualstudio.com](https://code.visualstudio.com/Download) 下载适用于你的计算机的 Visual Studio Code。 
     - Visual Studio (.NET/C#)。 从 [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/) 下载 Visual Studio Community Edition。 
     - Eclipse (Java)。 从 [eclipse.org](https://www.eclipse.org/downloads/) 下载 Eclipse。 

2. 为代码安装 SDK: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md)
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>后续步骤 

若要将应用部署到 Azure Stack 中的资源, 请参阅[Azure Stack 的常见部署](azure-stack-dev-start-deploy-app.md)。
