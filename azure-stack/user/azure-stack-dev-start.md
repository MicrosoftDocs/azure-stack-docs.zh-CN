---
title: 设置 Azure Stack 上的开发环境 |Microsoft Docs
description: 开始开发适用于 Azure Stack 的应用程序。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 9b3d58e67d7c6ca7016b3ecb51c09171aae7c06a
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490019"
---
# <a name="set-up-a-development-environment-on-azure-stack"></a>设置 Azure Stack 上的开发环境 

你可以开发适用于 Windows 10、 Linux 或 macOS 工作站使用 Azure Stack 的应用程序。 在本文中我们介绍： 

- 在 Azure Stack 运行您的应用程序的各种上下文。 
- 若要获取你可以遵循的步骤设置与 Windows 10、 Linux 或 macOS 计算机工作站。 
- 若要在 Azure Stack 中创建资源的步骤并部署的应用。 

## <a name="azure-stack-context-and-your-code"></a>Azure Stack 上下文和你的代码 

您可以编写脚本和应用程序可以完成 Azure Stack 中的所有任务。 但是，可能会限制你尝试完成到三种不同模式很有帮助。 

1. 在第一个模式下，你可以创建将预配使用 Azure 资源管理器模板的 Azure Stack 中的资源的应用。 例如，可以创建一个脚本，它构造将创建虚拟网络和将承载您的应用程序的 Vm 的 Azure 资源管理器模板。 

2. 在第二个模式下，使用直接使用 REST API 和 REST 客户端在代码中创建的终结点。 在此模式下，会创建通过 api 发送请求来创建虚拟网络和 Vm 的脚本。 

3. 在第三个模式下，可以使用你的代码在 Azure Stack 中创建托管的应用。 一次在 Azure Stack，若要承载您的应用程序，请将您的应用程序部署到基础结构中具有在基础结构。 通常情况下，将准备您的环境，然后将您的应用程序部署到该环境。 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>基础结构即服务和平台即服务 

为云，Azure Stack 支持： 

- 基础结构即服务 (IaaS) 
- 平台即服务 (PaaS) 

IaaS 和 PaaS 会通知你希望如何设置开发计算机。 

IaaS 是从网络设备、 网络和服务器的数据中心的各个部分的虚拟化。 当应用部署到托管的 web 服务器的 VM 时，您正处于 IaaS 范例。 在此模式，Azure Stack 管理虚拟齿轮和您的应用程序是在虚拟服务器。 Azure Stack 资源提供程序支持的网络组件和虚拟服务器。 

PaaS 抽象化基础结构层，以便将您的应用程序部署到的终结点，然后运行您的应用程序。 PaaS 模式中您可能使用容器来承载您的应用程序，然后将容器化应用程序部署到运行该容器的服务或者您可能会将您的应用程序推送到运行您的应用程序的服务直接。 Azure 堆栈可用于运行 Azure 应用服务和 Kubernetes。 

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manager 

通过 Azure Stack 版本的 Azure 资源管理器启用这些三种模式，以及 PaaS 或 IaaS。 管理框架，可部署、 管理和监视 Azure Stack 资源。 管理器允许您使用的这些项作为单个操作中的组。 详细了解有关使用 Azure Stack 资源管理器，请参阅[在 Azure Stack 中的管理 API 版本配置文件](azure-stack-version-profiles.md)。 

### <a name="azure-stack-development-kits"></a>Azure Stack 开发工具包 

Azure Stack 使用 Azure Stack 版本的 Azure 资源管理器。  为了帮助您处理与 Azure Stack 资源管理器使用所选的代码，我们提供了大量的软件开发工具包。 其中包括： 

- [.Net/C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)

## <a name="before-you-start"></a>开始之前 

将需要以下项： 

- 对 Azure Stack 用户门户的访问。 
- 租户的名称。 
- 若要了解是否使用 Azure Active directory (Azure AD) 或 Active Directory 联合身份验证服务 (AD FS) 作为标识管理器。 

如果你在 Azure Stack 有任何疑问，请联系云操作员。 

## <a name="windows-10"></a>Windows 10 

Windows 10 计算机便可以使用 PowerShell 5.0 中，Visual Studio 中，如果你正在使用 ASDK 连接到您的 VPN 连接的环境。 

### <a name="set-up-your-tools"></a>设置你的工具 

1. 获取设置使用 PowerShell。 有关说明，请按照中的步骤[安装 Azure Stack Powershell](../operator/azure-stack-powershell-install.md)。 

2. 下载 Azure Stack 工具。 有关说明，请按照中的步骤[从 GitHub 下载 Azure Stack 工具](../operator/azure-stack-powershell-download.md)。 

3. 如果使用 ASDK，安装和配置[到 Azure Stack 的 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn)。 

4. 安装和配置 Azure CLI。 有关说明，请按照中的步骤[使用 Azure CLI 在 Azure Stack 中使用 API 版本配置文件](azure-stack-version-profiles-azurecli2.md)。 

5. 安装和配置 Azure 存储资源管理器。 Azure 存储资源管理器是一款独立应用，可用于处理 Azure Stack 存储数据。  有关说明，请按照中的步骤[连接到 Azure Stack 订阅或存储帐户的存储资源管理器](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成的开发环境 

1. 安装集成的开发环境 (IDE)，具体取决于你的基本代码和首选项。 

     - Visual Studio Code （Python、 Go、 NodeJS）。 通过在计算机下载 Visual Studio Code [code.visualstudio.com](https://code.visualstudio.com/Download)。 
     - Visual Studio (.Net /C#)。 下载 Visual Studio Community edition [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)。 
     - Eclipse (Java)。 下载从 Eclipse [eclipse.org](https://www.eclipse.org/downloads/)。 

2. 安装软件开发工具包 (SDK) 为你的代码。 

     - [.Net/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Linux 计算机将允许您可以使用 Azure CLI 和 Visual Studio Code 中或你自己青睐的集成的开发环境。 

> [!Note]   
> 如果与 ASDK 配合使用的 Linux 计算机，在远程计算机需要与 ASDK 位于同一网络中。 你将无法再使用虚拟专用网络连接进行连接。 

### <a name="set-up-your-tools"></a>设置你的工具 

1. 安装和配置 Azure CLI。 有关说明，请按照中的步骤[使用 Azure CLI 在 Azure Stack 中使用 API 版本配置文件](azure-stack-version-profiles-azurecli2.md)。 

2. 安装和配置 Azure 存储资源管理器。 Azure 存储资源管理器是一款独立应用，可用于处理 Azure Stack 存储数据。  有关说明，请按照中的步骤[连接到 Azure Stack 订阅或存储帐户的存储资源管理器](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成的开发环境 

1. 安装集成的开发环境 (IDE)，具体取决于你的基本代码和首选项。 

     - Visual Studio Code （Python、 Go、 NodeJS）。 通过在计算机下载 Visual Studio Code [code.visualstudio.com](https://code.visualstudio.com/Download)。 
     - Visual Studio (.Net /C#)。 下载 Visual Studio Community edition [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)。 
     - Eclipse (Java)。 下载从 Eclipse [eclipse.org](https://www.eclipse.org/downloads/)。 

2. 安装软件开发工具包 (SDK) 为你的代码。 

     - [.Net/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOs 

MacOS 计算机将允许您可以使用 Azure CLI 和 Visual Studio Code 中或你自己青睐的集成的开发环境。 

> [!Note]   
> 如果与 ASDK 配合使用 macOS 计算机，在远程计算机需要与 ASDK 位于同一网络中。 你将无法再使用虚拟专用网络连接进行连接。 

### <a name="set-up-your-tools"></a>设置你的工具 

1. 安装和配置 Azure CLI。 有关说明，请按照中的步骤[使用 Azure CLI 在 Azure Stack 中使用 API 版本配置文件](azure-stack-version-profiles-azurecli2.md)。 

2. 安装和配置 Azure 存储资源管理器。 Azure 存储资源管理器是一款独立应用，可用于处理 Azure Stack 存储数据。 有关说明，请按照中的步骤[连接到 Azure Stack 订阅或存储帐户的存储资源管理器](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成的开发环境 

1. 安装集成的开发环境 (IDE)，具体取决于你的基本代码和首选项。 

     - Visual Studio Code （Python、 Go、 NodeJS）。 通过在计算机下载 Visual Studio Code [code.visualstudio.com](https://code.visualstudio.com/Download)。 
     - Visual Studio (.Net /C#)。 下载 Visual Studio Community edition [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)。 
     - Eclipse (Java)。 下载从 Eclipse [eclipse.org](https://www.eclipse.org/downloads/)。 

2. 安装软件开发工具包 (SDK) 为你的代码。 

     - [.Net/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>后续步骤 

将应用部署到 Azure Stack 中的资源。 您可以发现中的步骤[适用于 Azure Stack 的常见部署](azure-stack-dev-start-deploy-app.md)。