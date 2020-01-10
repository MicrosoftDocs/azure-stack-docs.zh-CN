---
title: 从 GitHub 下载 Azure Stack 中心工具 |Microsoft Docs
description: 了解如何下载使用 Azure Stack 中心所需的工具。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: bb72aeac211dcf714407a022531e899c5509f639
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75810233"
---
# <a name="download-azure-stack-hub-tools-from-github"></a>从 GitHub 下载 Azure Stack 中心工具

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

**Test-azurestack**是托管 PowerShell 模块的[GitHub 存储库](https://github.com/Azure/AzureStack-Tools)，用于管理资源并将资源部署到 Azure Stack 中心。 如果打算建立 VPN 连接，则可将这些 PowerShell 模块下载到 Azure Stack 开发工具包（ASDK）或基于 Windows 的外部客户端。 若要获取这些工具，请克隆 GitHub 存储库，或通过运行以下脚本来下载**test-azurestack**文件夹：

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

## <a name="functionality-provided-by-the-modules"></a>模块提供的功能

**Test-azurestack**存储库包含支持以下 Azure Stack 中心功能的 PowerShell 模块：  

| 功能 | Description | 谁可以使用此模块？ |
| --- | --- | --- |
| [云功能](../user/azure-stack-validate-templates.md) | 使用此模块获取云的云功能。 例如，你可以获取 API 版本和 Azure 资源管理器资源等云功能。 还可以获取 Azure Stack 中心和 Azure 云的 VM 扩展。 | 云操作员和用户 |
| [Azure Stack 中心的资源管理器策略](../user/azure-stack-policy-module.md) | 使用此模块配置与 Azure Stack 中心具有相同的版本和服务可用性的 Azure 订阅或 Azure 资源组。 | 云操作员和用户 |
| [注册到 Azure](azure-stack-registration.md ) | 使用此模块将 ASDK 实例注册到 Azure。 注册后，可以下载 Azure Marketplace 项在 Azure Stack Hub 中使用它们。 | 云操作员 |
| [Azure Stack 中心部署](../asdk/asdk-install.md) | 使用此模块可通过使用 Azure Stack 中心虚拟硬盘（VHD）映像准备要部署和重新部署 Azure Stack 中心主机。 | 云操作员|
| [连接到 Azure Stack 集线器](azure-stack-powershell-install.md) | 使用此模块配置与 Azure Stack 集线器的 VPN 连接。 | 云操作员和用户 |
| [模板验证程序](../user/azure-stack-validate-templates.md) | 使用此模块来验证现有模板还是新模板可以部署到 Azure Stack 中心。 | 云操作员和用户|

## <a name="next-steps"></a>后续步骤

- [Azure Stack 集线器上的 PowerShell 入门](../user/azure-stack-powershell-overview.md)。
- [配置 Azure Stack 集线器用户的 PowerShell 环境](../user/azure-stack-powershell-configure-user.md)。
- [通过 VPN 连接到 Azure Stack 开发工具包](../asdk/asdk-connect.md)。
