---
title: 在 Azure Stack Hub 中设置开发环境
description: 开始开发适用于 Azure Stack Hub 的应用程序。
author: mattbriggs
ms.topic: overview
ms.date: 10/16/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/16/2020
ms.openlocfilehash: aaf6e48a728659ee6e756cee674edb423362db67
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546117"
---
# <a name="set-up-a-development-environment-in-azure-stack-hub"></a>在 Azure Stack Hub 中设置开发环境 

可以使用 Windows 10、Linux 或 macOS 工作站开发适用于 Azure Stack Hub 的应用程序。 本文介绍： 

- 应用在 Azure Stack Hub 中运行时所处的各种上下文。 
- 使用 Windows 10、Linux 或 macOS 工作站进行设置时可以遵循的步骤。 
- 在 Azure Stack Hub 中创建资源并将其部署到应用的步骤。 

## <a name="azure-stack-hub-context-and-your-code"></a>Azure Stack Hub 上下文和代码 

可以编写脚本和应用，以在 Azure Stack Hub 中完成多种任务。 但是，将范围限制为以下三种模式很有帮助： 

1. 在第一种模式下，可以使用 Azure 资源管理器模板创建应用，用于在 Azure Stack Hub 中预配资源。 例如，可以编写一个可以构造 Azure 资源管理器模板的脚本，而该模板又可以创建用于托管应用的虚拟网络和 VM。 

2. 在第二种模式下，可以使用 REST API 和 REST 客户端直接处理在代码中创建的终结点。 在此模式下，可编写一个脚本，以通过将请求发送到 API 来创建虚拟网络和 VM。 

3. 在第三种模式下，可以使用代码创建托管在 Azure Stack Hub 中的应用。 在 Azure Stack Hub 中创建用于托管应用的基础结构后，将应用部署到该基础结构。 一般情况下，你会先准备好环境，然后将应用部署到该环境。 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>基础结构即服务和平台即服务 

作为一款云平台产品，Azure Stack Hub 支持： 

- 基础结构即服务 (IaaS) 
- 平台即服务 (PaaS) 

IaaS 和 PaaS 都会告知如何设置开发计算机。 

IaaS 是数据中心内的网络设备、网络和服务器等部件的虚拟化。 将应用部署到托管 Web 服务器的 VM 时，将在 IaaS 模型中操作。 在此模型中，Azure Stack Hub 管理虚拟设备，而应用位于虚拟服务器上。 Azure Stack Hub 资源提供程序支持网络组件和虚拟服务器。 

PaaS 将基础结构层抽象化，因此，你可以将应用部署到随后会运行该应用的终结点。 在 PaaS 模型中，可以使用容器来托管应用，然后将容器化的应用部署到运行容器的服务。 或者，可以直接将应用推送到运行应用的服务。 可以使用 Azure Stack Hub 来运行 Azure 应用服务和 Kubernetes。 

### <a name="azure-stack-hub-resource-manager"></a>Azure Stack Hub 资源管理器 

上述三种模式以及 PaaS 或 IaaS 由 Azure Stack Hub 版的 Azure 资源管理器启用。 使用此管理框架可以部署、管理和监视 Azure Stack Hub 资源。 该框架允许在单个操作中以组的形式使用资源。 有关使用 Azure Stack Hub 资源管理器的详细信息，请参阅[管理 Azure Stack Hub 中的 API 版本配置文件](azure-stack-version-profiles.md)。 

### <a name="azure-stack-hub-sdks"></a>Azure Stack Hub SDK 

Azure Stack Hub 使用 Azure Stack Hub 版的 Azure 资源管理器。 为了帮助你通过所选代码使用 Azure Stack Hub 资源管理器，我们提供了许多 SDK，包括： 

- [.NET/C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)
- [Node.js](azure-stack-version-profile-nodejs.md)

## <a name="before-you-start"></a>开始之前 

在开始设置环境之前，需要： 

- Azure Stack Hub 用户门户的访问权限。 
- 租户的名称。 
- 确定是使用 Azure Active Directory (Azure AD) 还是 Active Directory 联合身份验证服务 (AD FS) 作为标识管理器。 

如果遇到 Azure Stack Hub 方面的任何问题，请与云运营商联系。 

## <a name="windows-10"></a>Windows 10 

在 Windows 10 计算机上，可以使用 PowerShell 5.0 和 Visual Studio。 如果使用的是 Azure Stack 开发工具包 (ASDK)，请通过 VPN 连接环境。 

### <a name="set-up-your-tools"></a>设置工具 

1. 使用 PowerShell 进行设置。 有关说明，请参阅[安装 Azure Stack Hub PowerShell](../operator/powershell-install-az-module.md)。 

2. 下载 Azure Stack Hub 工具。 有关说明，请参阅[从 GitHub 下载 Azure Stack Hub 工具](../operator/azure-stack-powershell-download.md)。 

3. 如果使用的是 ASDK，请安装并配置 [Azure Stack Hub 的 VPN 连接](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn)。 

4. 安装并配置 Azure CLI。 有关说明，请参阅[在 Azure Stack Hub 中将 API 版本配置文件与 Azure CLI 配合使用](azure-stack-version-profiles-azurecli2.md)。 

5. 安装并配置设置 Azure 存储资源管理器。 存储资源管理器是可用于处理 Azure Stack Hub 存储数据的独立应用。 有关说明，请参阅[将存储资源管理器连接到 Azure Stack Hub 订阅或存储帐户](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成开发环境 

1. 根据代码基和偏好安装集成开发环境 (IDE)。 

     - Visual Studio Code（Python、Go、NodeJS）。 从 [code.visualstudio.com](https://code.visualstudio.com/Download) 下载适用于你的计算机的 Visual Studio Code。 
     - Visual Studio (.NET/C#)。 从 [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/) 下载 Visual Studio Community Edition。 
     - Eclipse (Java)。 从 [eclipse.org](https://www.eclipse.org/downloads/) 下载 Eclipse。 

2. 为代码安装 SDK： 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

在 Linux 计算机上，可以使用 Azure CLI 和 Visual Studio Code，或自己偏好的集成开发环境。 

> [!NOTE]   
> 如果使用包含 ASDK 的 Linux 计算机，则远程计算机需位于 ASDK 所在的同一网络。 无法使用虚拟专用网连接进行连接。 

### <a name="set-up-your-tools"></a>设置工具 

1. 安装并配置 Azure CLI。 有关说明，请参阅[在 Azure Stack Hub 中将 API 版本配置文件与 Azure CLI 配合使用](azure-stack-version-profiles-azurecli2.md)。 

2. 安装并配置设置 Azure 存储资源管理器。 存储资源管理器是可用于处理 Azure Stack Hub 存储数据的独立应用。 有关说明，请参阅[将存储资源管理器连接到 Azure Stack Hub 订阅或存储帐户](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成开发环境 

1. 根据代码基和偏好安装集成开发环境 (IDE)。 

     - Visual Studio Code（Python、Go、NodeJS）。 从 [code.visualstudio.com](https://code.visualstudio.com/Download) 下载适用于你的计算机的 Visual Studio Code。 
     - Visual Studio (.NET/C#)。 从 [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/) 下载 Visual Studio Community Edition。 
     - Eclipse (Java)。 从 [eclipse.org](https://www.eclipse.org/downloads/) 下载 Eclipse。 

2. 为代码安装 SDK： 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

在 macOS 计算机中可以使用 Azure CLI 和 Visual Studio Code，或自己偏好的集成开发环境。 

> [!NOTE]   
> 如果使用包含 ASDK 的 macOS 计算机，则远程计算机需位于 ASDK 所在的同一网络。 无法使用虚拟专用网连接进行连接。 

### <a name="set-up-your-tools"></a>设置工具 

1. 安装并配置 Azure CLI。 有关说明，请参阅[在 Azure Stack Hub 中将 API 版本配置文件与 Azure CLI 配合使用](azure-stack-version-profiles-azurecli2.md)。 

2. 安装并配置设置 Azure 存储资源管理器。 存储资源管理器是可用于处理 Azure Stack Hub 存储数据的独立应用。 有关说明，请参阅[将存储资源管理器连接到 Azure Stack Hub 订阅或存储帐户](azure-stack-storage-connect-se.md)。 

### <a name="install-your-integrated-development-environment"></a>安装集成开发环境 

1. 根据代码基和偏好安装集成开发环境 (IDE)。 

     - Visual Studio Code（Python、Go、NodeJS）。 从 [code.visualstudio.com](https://code.visualstudio.com/Download) 下载适用于你的计算机的 Visual Studio Code。 
     - Visual Studio (.NET/C#)。 从 [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/) 下载 Visual Studio Community Edition。 
     - Eclipse (Java)。 从 [eclipse.org](https://www.eclipse.org/downloads/) 下载 Eclipse。 

2. 为代码安装 SDK： 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md)
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>后续步骤 

若要将应用部署到 Azure Stack Hub 中的资源，请参阅 [Azure Stack Hub 的常见部署](azure-stack-dev-start-deploy-app.md)。
