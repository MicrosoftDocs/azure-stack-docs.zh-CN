---
title: 在 Azure Stack 中心设置开发环境
description: 开始为 Azure Stack 集线器开发应用程序。
author: mattbriggs
ms.topic: overview
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: ab9ff25591b46df26fa68c33bcea237e57356667
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704091"
---
# <a name="set-up-a-development-environment-in-azure-stack-hub"></a>在 Azure Stack 中心设置开发环境 

可以使用 Windows 10、Linux 或 macOS 工作站开发 Azure Stack 中心应用程序。 本文介绍： 

- 应用在 Azure Stack 集线器中运行的各种上下文。 
- 为您设置 Windows 10、Linux 或 macOS 工作站所要遵循的步骤。 
- 在 Azure Stack 集线器中创建资源并将其部署到应用的步骤。 

## <a name="azure-stack-hub-context-and-your-code"></a>Azure Stack 中心上下文和你的代码 

您可以编写脚本和应用程序，以在 Azure Stack 中心完成很多任务。 但是，将范围限制为以下三种模式会很有帮助： 

1. 在第一种模式中，可以使用 Azure 资源管理器模板创建在 Azure Stack 集线器中预配资源的应用。 例如，你可以编写一个脚本来构造 Azure 资源管理器模板，该模板将创建一个虚拟网络和承载你的应用程序的 Vm。 

2. 在第二种模式下，你可以使用 REST API 和在代码中创建的 REST 客户端直接使用终结点。 在此模式下，你将编写一个脚本，用于通过将请求发送到 Api 来创建虚拟网络和 Vm。 

3. 在第三种模式下，你可以使用代码来创建在 Azure Stack Hub 中托管的应用。 在用于托管应用的 Azure Stack 集线器中创建基础结构后，将应用部署到基础结构。 通常，你将准备好环境，然后将你的应用部署到该环境中。 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>基础结构即服务和平台即服务 

作为云平台产品，Azure Stack 集线器同时支持这两种产品： 

- 基础结构即服务 (IaaS) 
- 平台即服务（PaaS） 

IaaS 和 PaaS 都通知如何设置开发计算机。 

IaaS 是来自网络设备、网络和服务器的数据中心部分的虚拟化。 当你将应用程序部署到承载 web 服务器的 VM 时，你正在使用 IaaS 模型。 在此模型中，Azure Stack 集线器管理虚拟设备，而你的应用程序在虚拟服务器上。 Azure Stack 集线器资源提供程序支持网络组件和虚拟服务器。 

PaaS 将基础结构层抽象化，以便将应用部署到运行该应用的终结点。 在 PaaS 模型中，你可以使用容器来托管应用，然后将容器化应用部署到运行容器的服务。 或者，你可以将应用程序直接推送到运行该应用程序的服务。 可以使用 Azure Stack 集线器运行 Azure App Service 和 Kubernetes。 

### <a name="azure-stack-hub-resource-manager"></a>Azure Stack 中心资源管理器 

前面提到的三种模式以及 PaaS 或 IaaS 均由 Azure 资源管理器 Azure Stack 集线器版本启用。 此管理框架允许您部署、管理和监视 Azure Stack 集线器资源。 它使你可以在单个操作中以组的形式处理资源。 有关使用 Azure Stack 中心资源管理器的详细信息，请参阅[在 Azure Stack 中心管理 API 版本配置文件](azure-stack-version-profiles.md)。 

### <a name="azure-stack-hub-sdks"></a>Azure Stack 集线器 Sdk 

Azure Stack 中心使用 Azure 资源管理器的 Azure Stack 中心版本。 为了帮助你使用你的选择代码来处理 Azure Stack 中心资源管理器，我们提供了许多 Sdk，其中包括： 

- [NETC#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)
- [Node.js](azure-stack-version-profile-nodejs.md)

## <a name="before-you-start"></a>开始之前 

在开始设置环境之前，需要： 

- 访问 Azure Stack 集线器用户门户。 
- 租户的名称。 
- 若要确定是否使用 Azure Active Directory （Azure AD）或 Active Directory 联合身份验证服务（AD FS）作为标识管理器。 

如果对 Azure Stack 中心有任何疑问，请与云操作员联系。 

## <a name="windows-10"></a>Windows 10 

如果使用的是 Windows 10 计算机，则可使用 PowerShell 5.0 和 Visual Studio。 如果使用的是 Azure Stack 开发工具包（ASDK），则可以使用 VPN 连接来连接到环境。 

### <a name="set-up-your-tools"></a>设置工具 

1. 通过 PowerShell 进行设置。 有关说明，请参阅[安装 Azure Stack Hub Powershell](../operator/azure-stack-powershell-install.md)。 

2. 下载 Azure Stack 中心工具。 有关说明，请参阅[从 GitHub 下载 Azure Stack 中心工具](../operator/azure-stack-powershell-download.md)。 

3. 如果使用的是 ASDK，请安装并配置[Azure Stack 集线器的 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn)。 

4. 安装并配置 Azure CLI。 有关说明，请参阅[将 API 版本配置文件与 Azure Stack 中心中的 Azure CLI 配合使用](azure-stack-version-profiles-azurecli2.md)。 

5. 安装并配置 Azure 存储资源管理器。 存储资源管理器是一款独立应用，可用于处理 Azure Stack 中心存储数据。 有关说明，请参阅[将存储资源管理器连接到 Azure Stack 中心订阅或存储帐户](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成开发环境 

1. 根据你的基本代码和首选项安装集成开发环境（IDE）。 

     - Visual Studio Code （Python，中转，NodeJS）。 从[code.visualstudio.com](https://code.visualstudio.com/Download)下载计算机 Visual Studio Code。 
     - Visual Studio （.NET/C#）。 从[visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)下载 Visual Studio 社区版。 
     - Eclipse （Java）。 从[eclipse.org](https://www.eclipse.org/downloads/)下载 Eclipse。 

2. 为代码安装 SDK： 

     - [NETC#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

如果你使用的是 Linux 计算机，则可以使用 Azure CLI、Visual Studio Code 或你自己的首选集成开发环境。 

> [!Note]   
> 如果你使用的是具有 ASDK 的 Linux 计算机，则远程计算机必须与 ASDK 在同一网络中。 你将无法使用虚拟专用网络连接进行连接。 

### <a name="set-up-your-tools"></a>设置工具 

1. 安装并配置 Azure CLI。 有关说明，请参阅[将 API 版本配置文件与 Azure Stack 中心中的 Azure CLI 配合使用](azure-stack-version-profiles-azurecli2.md)。 

2. 安装并配置 Azure 存储资源管理器。 存储资源管理器是一款独立应用，可用于处理 Azure Stack 中心存储数据。 有关说明，请参阅[将存储资源管理器连接到 Azure Stack 中心订阅或存储帐户](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成开发环境 

1. 根据你的基本代码和首选项安装集成开发环境（IDE）。 

     - Visual Studio Code （Python，中转，NodeJS）。 从[code.visualstudio.com](https://code.visualstudio.com/Download)下载计算机 Visual Studio Code。 
     - Visual Studio （.NET/C#）。 从[visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)下载 Visual Studio 社区版。 
     - Eclipse （Java）。 从[eclipse.org](https://www.eclipse.org/downloads/)下载 Eclipse。 

2. 为代码安装 SDK： 

     - [NETC#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

使用 macOS 计算机，你可以使用 Azure CLI、Visual Studio Code 或你自己的首选集成开发环境。 

> [!Note]   
> 如果要将 macOS 计算机与 ASDK 配合使用，则远程计算机需要与 ASDK 位于同一网络中。 你将无法使用虚拟专用网络连接进行连接。 

### <a name="set-up-your-tools"></a>设置工具 

1. 安装并配置 Azure CLI。 有关说明，请参阅[将 API 版本配置文件与 Azure Stack 中心中的 Azure CLI 配合使用](azure-stack-version-profiles-azurecli2.md)。 

2. 安装并配置 Azure 存储资源管理器。 存储资源管理器是一款独立应用，可用于处理 Azure Stack 中心存储数据。 有关说明，请参阅[将存储资源管理器连接到 Azure Stack 中心订阅或存储帐户](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成开发环境 

1. 根据你的基本代码和首选项安装集成开发环境（IDE）。 

     - Visual Studio Code （Python，中转，NodeJS）。 从[code.visualstudio.com](https://code.visualstudio.com/Download)下载计算机 Visual Studio Code。 
     - Visual Studio （.NET/C#）。 从[visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/)下载 Visual Studio 社区版。 
     - Eclipse （Java）。 从[eclipse.org](https://www.eclipse.org/downloads/)下载 Eclipse。 

2. 为代码安装 SDK： 

     - [NETC#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md)
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>后续步骤 

若要将应用部署到 Azure Stack 集线器中的资源，请参阅[Azure Stack 中心的常见部署](azure-stack-dev-start-deploy-app.md)。
