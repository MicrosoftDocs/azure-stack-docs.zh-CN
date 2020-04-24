---
title: 从 GitHub 下载 Azure Stack Hub 工具
description: 了解如何下载操作 Azure Stack Hub 时所需的工具。
author: mattbriggs
ms.topic: article
ms.date: 4/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 4/22/2020
ms.openlocfilehash: 9faa562f9b8ff339016473f1b3c0df0512c90988
ms.sourcegitcommit: 7b8e067cb449e67ca9c2935580684d78840ad495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2020
ms.locfileid: "82106918"
---
# <a name="download-azure-stack-hub-tools-from-github"></a>从 GitHub 下载 Azure Stack Hub 工具

**AzureStack-Tools** 是托管 PowerShell 模块的 [GitHub 存储库](https://github.com/Azure/AzureStack-Tools)，可用于管理资源并将其部署到 Azure Stack Hub。 如果你打算建立 VPN 连接，则可将这些 PowerShell 模块下载到 Azure Stack 开发工具包 (ASDK) 或基于 Windows 的外部客户端。 

## <a name="get-tools-for-azure-stack-hub-azurerm-module"></a>获取 Azure Stack Hub AzureRM 模块的工具

若要获取这些工具，请从`master`分支克隆 GitHub 存储库，或通过运行以下脚本来下载**test-azurestack**文件夹：

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```
有关使用 Azure Stack 集线器的 AzureRM 模块的详细信息，请参阅[安装适用于 Azure Stack 中心的 PowerShell AzureRM 模块](azure-stack-powershell-install.md)

## <a name="get-tools-for-azure-stack-hub-az-preview-module"></a>获取 Azure Stack 集线器 Az （预览版）模块的工具

若要获取这些工具，请从`az`分支克隆 GitHub 存储库，或通过运行以下脚本来下载**test-azurestack**文件夹：

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/az.zip `
  -OutFile az.zip

# Expand the downloaded files.
expand-archive az.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
 cd AzureStack-Tools-az

```

有关将 Az module 用于 Azure Stack 中心的详细信息，请参阅[安装适用于 Azure Stack 中心的 PowerShell Az preview module](powershell-install-az-module.md)。

## <a name="functionality-provided-by-the-modules"></a>模块提供的功能

**Test-azurestack**存储库包含支持以下 Azure Stack 中心功能的 PowerShell 模块：  

| 功能 | 说明 | 谁可以使用此模块？ |
| --- | --- | --- |
| [云功能](../user/azure-stack-validate-templates.md) | 使用此模块可获取云的云功能。 例如，可以获取 API 版本和 Azure 资源管理器资源等云功能。 还可以获取 Azure Stack 中心和 Azure 云的 VM 扩展。 | 云操作员和用户 |
| [Azure Stack 中心的资源管理器策略](../user/azure-stack-policy-module.md) | 使用此模块配置与 Azure Stack 中心具有相同的版本和服务可用性的 Azure 订阅或 Azure 资源组。 | 云操作员和用户 |
| [注册到 Azure](azure-stack-registration.md ) | 使用此模块可将 ASDK 实例注册到 Azure。 注册后，可以下载 Azure Marketplace 项在 Azure Stack Hub 中使用它们。 | 云操作员 |
| [Azure Stack 中心部署](../asdk/asdk-install.md) | 使用此模块可通过使用 Azure Stack 中心虚拟硬盘（VHD）映像准备要部署和重新部署 Azure Stack 中心主机。 | 云操作员|
| [连接到 Azure Stack 集线器](azure-stack-powershell-install.md) | 使用此模块配置与 Azure Stack 集线器的 VPN 连接。 | 云操作员和用户 |
| [模板验证程序](../user/azure-stack-validate-templates.md) | 使用此模块来验证现有模板还是新模板可以部署到 Azure Stack 中心。 | 云操作员和用户|

## <a name="next-steps"></a>后续步骤

- [Azure Stack 集线器上的 PowerShell 入门](../user/azure-stack-powershell-overview.md)。
- [配置 Azure Stack 集线器用户的 PowerShell 环境](../user/azure-stack-powershell-configure-user.md)。
- [通过 VPN 连接到 Azure Stack 开发工具包](../asdk/asdk-connect.md)。
