---
title: 通过 Visual Studio Code 部署到 Azure Stack |Microsoft Docs
description: 作为用户，我想要在 Visual Studio Code 中创建 Azure 资源管理器模板，并使用部署架构来准备与我的 Azure Stack 版本兼容的模板。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 914e3e8db57009d58a14aa87d24ff86a8291e52b
ms.sourcegitcommit: e8aa26b078a9bab09c8fafd888a96785cc7abb4d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71711080"
---
# <a name="deploy-with-visual-studio-code-to-azure-stack"></a>将 Visual Studio Code 部署到 Azure Stack

你可以使用 Visual Studio Code 和 Azure 资源管理器工具扩展来创建和编辑 Azure 资源管理器模板，这些模板将适用于你的 Azure Stack 版本。 可以在 Visual Studio Code 中不使用扩展创建资源管理器模板，但是该扩展提供自动完成选项，可以简化模板开发。 此外，您还可以指定一个可帮助您了解 Azure Stack 上可用资源的部署架构。

本文介绍如何部署 Windows 虚拟机。

## <a name="concepts-for-azure-stack-resource-manager"></a>Azure Stack 的概念资源管理器

### <a name="azure-stack-resource-manager"></a>Azure Stack 资源管理器

若要了解有关在 Azure Stack 中部署和管理 Azure 解决方案的概念，请参阅[在 Azure Stack 中使用 azure 资源管理器模板](azure-stack-arm-templates.md)。

### <a name="api-profiles"></a>API 配置文件
要了解与协调资源提供程序相关的概念 Azure Stack 参阅[Azure Stack 中的 "管理 API 版本配置文件](azure-stack-version-profiles.md)"。

### <a name="the-deployment-schema"></a>部署架构

Azure Stack 部署架构通过 Visual Studio Code 中的 Azure 资源管理器模板支持混合配置文件。 可以在 JSON 模板中更改一行以引用架构，然后可以使用 IntelliSense 查看 Azure 兼容资源。 在架构中，查看 Azure Stack 的版本中支持的资源提供程序、类型和 API 版本。 该架构依赖于 API 配置文件来检索 Azure Stack 的版本中支持的资源提供程序中 API 终结点的特定版本。 你可以使用类型和 apiVersion 的单词完成，然后将限制为可用于 API 配置文件的 apiVersion 和资源类型。

## <a name="prerequisites"></a>先决条件

- [Visual Studio Code](https://code.visualstudio.com/)
- 访问 Azure Stack
- 在到达管理终结点的计算机上[安装 Azure Stack PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json)

## <a name="install-resource-manager-tools-extension"></a>安装资源管理器工具扩展

若要安装资源管理器工具扩展，请使用以下步骤：

1. 打开 Visual Studio Code。
2. 按 CTRL + SHIFT + X 打开 "扩展" 窗格
3. 搜索 `Azure Resource Manager Tools`，并选择“安装”。
4. 选择“重新加载”完成扩展安装。

## <a name="get-a-template"></a>获取模板

无需从头开始创建模板，只需从 Test-azurestack （ https://github.com/Azure/AzureStack-QuickStart-Templates) ）打开模板。 Test-azurestack-模板是用于将资源部署到 Azure Stack 资源管理器模板的存储库。 

本文中的模板称为 `101-vm-windows-create`。 该模板定义要 Azure Stack 的 Windows VM 的基本部署。  此模板还会部署一个虚拟网络（使用 DNS）、网络安全组和一个网络接口。

1. 打开 Visual Studio Code 并导航到计算机上的工作文件夹。
2. 在 Visual Studio Code 中打开 Git bash 终端。
3. 运行以下命令以检索 Azure Stack 快速入门存储库。
    ```bash  
    Git clone https://github.com/Azure/AzureStack-QuickStart-Templates.git
    ```
4. 打开包含该存储库的目录。
    ```bash  
    CD AzureStack-QuickStart-Templates
    ```
5. 选择 "**打开**" 以在存储库中的 `/101-vm-windows-create/azuredeploy.json` 打开文件。
6. 将该文件保存到你自己的工作区中，或者，如果你已创建了可就地工作的存储库分支。
7. 文件仍处于打开状态时，将 `$Schema` 字段更改为 `https://schema.management.azure.com/schemas/2019-03-01-hybrid/deploymentTemplate.json#`。
8. 可以通过清除 apiProfile 字段的值来检查部署架构是否正常工作。
    ```JSON  
    "apiProfile": ""
    ```
9. 将光标置于空引号之间，并按 CTRL + 空格键。 可以从 Azure Stack 的部署架构中的有效 API 配置文件中进行选择。 你可以对模板中的每个资源提供程序执行此操作。

    ![Azure Stack 资源管理器部署架构](./media/azure-stack-resource-manager-deploy-template-vscode/azure-stack-resource-manager-vscode-schema.png)

10. 准备就绪后，可以使用 PowerShell 部署模板。 按照部署时的[PowerShell](azure-stack-deploy-template-powershell.md)中的说明进行操作。 在脚本中指定模板的位置。
11. 部署 Windows VM 后，请导航到 Azure Stack 门户，并找到资源组。 如果要从 Azure Stack 中清除此练习的结果，请删除资源组。

## <a name="next-steps"></a>后续步骤

- 了解[Azure Stack 资源管理器模板](azure-stack-arm-templates.md)的详细信息。  
- 详细了解[Azure Stack 中的 API 配置文件](azure-stack-version-profiles.md)。