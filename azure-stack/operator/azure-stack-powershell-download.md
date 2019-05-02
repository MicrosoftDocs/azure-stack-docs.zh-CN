---
title: 从 GitHub 下载 Azure Stack 工具 | Microsoft Docs
description: 了解如何下载操作 Azure Stack 时所需的工具。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
origin.date: 01/14/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: e7c24431a508e45fea0a61025b62f9bc3443d97b
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64293835"
---
# <a name="download-azure-stack-tools-from-github"></a>从 GitHub 下载 Azure Stack 工具

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

**AzureStack-Tools** 是托管 PowerShell 模块的 [GitHub 存储库](https://github.com/Azure/AzureStack-Tools)，可用于管理资源并将其部署到 Azure Stack。 如果你打算建立 VPN 连接，则可将这些 PowerShell 模块下载到 Azure Stack 开发工具包或基于 Windows 的外部客户端。 若要获取这些工具，请克隆 GitHub 存储库，或运行以下脚本来下载 **AzureStack-Tools** 文件夹：

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

**AzureStack-Tools** 存储库包含支持以下 Azure Stack 功能的 PowerShell 模块：  

| 功能 | 描述 | 谁可以使用此模块？ |
| --- | --- | --- |
| [云功能](../user/azure-stack-validate-templates.md) | 使用此模块可获取云的云功能。 例如，可以使用此模块来获取 API 版本和 Azure 资源管理器资源等云功能。 还可以使用此模块获取 Azure Stack 和 Azure 云的 VM 扩展。 | 云操作员和用户 |
| [Azure Stack 的资源管理器策略](../user/azure-stack-policy-module.md) | 使用此模块可以配置版本和服务可用性与 Azure Stack 相同的 Azure 订阅或 Azure 资源组。 | 云操作员和用户 |
| [注册到 Azure](azure-stack-registration.md ) | 使用此模块可将开发工具包实例注册到 Azure。 注册后，可从 Azure 下载市场项，并在 Azure Stack 中使用它们。 | 云操作员 |
| [Azure Stack 部署](../asdk/asdk-install.md) | 使用此模块可通过 Azure Stack 虚拟硬盘 (VHD) 映像来准备用于部署和重新部署的 Azure Stack 主计算机。 | 云操作员|
| [连接到 Azure Stack](azure-stack-powershell-install.md) | 使用此模块配置与 Azure Stack 的 VPN 连接。 | 云操作员和用户 |
| [模板验证程序](../user/azure-stack-validate-templates.md) | 使用此模块可以验证是否可将现有或新的模板部署到 Azure Stack。 | 云操作员和用户|


## <a name="next-steps"></a>后续步骤
* [配置 Azure Stack 用户的 PowerShell 环境](../user/azure-stack-powershell-configure-user.md)   
* [通过 VPN 连接到 Azure Stack 开发工具包](../asdk/asdk-connect.md)  
