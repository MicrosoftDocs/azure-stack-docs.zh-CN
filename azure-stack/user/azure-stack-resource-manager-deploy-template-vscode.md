---
title: 使用 Visual Studio Code 部署到 Azure Stack Hub
description: 在 Visual Studio Code 中创建 Azure 资源管理器模板，并使用部署架构准备与我的 Azure Stack 中心版本兼容的模板。
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 72652f1a681ed630bdc67745f24c6e472101915f
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546848"
---
# <a name="deploy-with-visual-studio-code-to-azure-stack-hub"></a>使用 Visual Studio Code 部署到 Azure Stack Hub

可以使用 Visual Studio Code 和 Azure 资源管理器工具扩展来创建和编辑适用于你的 Azure Stack Hub 版本的 Azure 资源管理器模板。 可以在 Visual Studio Code 中不使用扩展创建资源管理器模板，但是该扩展提供自动完成选项，可以简化模板开发。 此外，还可以指定部署架构，以帮助了解 Azure Stack Hub 上可用的资源。

在本文中，你将部署一个 Windows 虚拟机。

## <a name="concepts-for-azure-stack-hub-resource-manager"></a>Azure Stack Hub 资源管理器的概念

### <a name="azure-stack-hub-resource-manager"></a>Azure Stack Hub 资源管理器

若要了解在 Azure Stack Hub 中部署和管理 Azure 解决方案的相关概念，请参阅[在 Azure Stack Hub 中使用 Azure 资源管理器模板](azure-stack-arm-templates.md)。

### <a name="api-profiles"></a>API 配置文件
若要了解在 Azure Stack Hub 上协调资源提供程序的相关概念，请参阅[在 Azure Stack Hub 中管理 API 版本配置文件](azure-stack-version-profiles.md)。

### <a name="the-deployment-schema"></a>部署架构

Azure Stack Hub 部署架构通过 Visual Studio Code 中的 Azure 资源管理器模板支持混合配置文件。 可以更改 JSON 模板中的一行来引用该架构，然后可使用 IntelliSense 来查看 Azure 兼容的资源。 使用架构查看你的 Azure Stack Hub 版本支持的资源提供程序、类型和 API 版本。 该架构依赖于使用 API 配置文件在 Azure Stack Hub 版本支持的资源提供程序中检索 API 终结点的特定版本。 可对类型和 apiVersion 使用字词填写功能，然后，只能使用 API 配置文件中可用的 apiVersion 和资源类型。

## <a name="prerequisites"></a>必备条件

- [Visual Studio Code](https://code.visualstudio.com/)
- 有权访问 Azure Stack Hub
- 已在可访问管理终结点的计算机上[安装 Azure Stack Hub PowerShell](../operator/powershell-install-az-module.md?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json)

## <a name="install-resource-manager-tools-extension"></a>安装资源管理器工具扩展

若要安装资源管理器工具扩展，请使用以下步骤：

1. 打开 Visual Studio Code。
2. 按 CTRL+SHIFT+X 打开“扩展”窗格
3. 搜索 `Azure Resource Manager Tools`，并选择“安装”  。
4. 选择“重新加载”完成扩展安装  。

## <a name="get-a-template"></a>获取模板

无需从头开始创建模板，可以通过 AzureStack-QuickStart-Templates (https://github.com/Azure/AzureStack-QuickStart-Templates) 打开一个模板。 AzureStack-QuickStart-Templates 是资源管理器模板的存储库，这些模板可将资源部署到 Azure Stack Hub。 

本文中的模板名为 `101-vm-windows-create`。 该模板定义 Windows VM 在 Azure Stack Hub 中的基本部署。  此模板还会部署虚拟网络（使用 DNS）、网络安全组和网络接口。

1. 打开 Visual Studio Code 并导航到计算机上的工作文件夹。
2. 在 Visual Studio Code 中打开 Git bash 终端。
3. 运行以下命令以检索 Azure Stack Hub 快速入门存储库。
    ```bash  
    Git clone https://github.com/Azure/AzureStack-QuickStart-Templates.git
    ```
4. 打开包含该存储库的目录。
    ```bash  
    CD AzureStack-QuickStart-Templates
    ```
5. 选择“打开”以打开存储库中位于 `/101-vm-windows-create/azuredeploy.json` 处的文件。
6. 将该文件保存到自己的工作区中；如果已创建存储库的分支，则可以在原位操作。
7. 在文件仍保持打开的情况下，将 `$Schema` 字段更改为 `https://schema.management.azure.com/schemas/2019-03-01-hybrid/deploymentTemplate.json#`。
8. 可以通过清除 apiProfile 字段值来检查部署架构是否正常运行。
    ```JSON  
    "apiProfile": ""
    ```
9. 将光标置于空引号之间，然后按 CTRL + 空格键。 可以从 Azure Stack Hub 部署架构中有效的 API 配置文件进行选择。 可对模板中的每个资源提供程序执行此操作。

    ![Azure Stack Hub 资源管理器部署架构](./media/azure-stack-resource-manager-deploy-template-vscode/azure-stack-resource-manager-vscode-schema.png)

10. 准备就绪后，可以使用 PowerShell 部署模板。 根据[使用 PowerShell 进行部署](azure-stack-deploy-template-powershell.md)中的说明操作。 在脚本中指定模板的位置。
11. 部署 Windows VM 后，导航到 Azure Stack Hub 门户并找到资源组。 若要从 Azure Stack Hub 中清除本练习的结果，请删除该资源组。

## <a name="next-steps"></a>后续步骤

- 详细了解 [Azure Stack Hub 资源管理器模板](azure-stack-arm-templates.md)。  
- 详细了解 [Azure Stack Hub 中的 API 配置文件](azure-stack-version-profiles.md)。
