---
title: 在 Azure Stack 上的 PowerShell |Microsoft Docs
description: Azure Stack 上的 PowerShell 具有模块和上下文的数量。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: b28a90ceca59e5cd072018b217d81d101c1b0853
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490046"
---
# <a name="get-started-with-powershell-on-azure-stack"></a>开始使用 Azure Stack 上的 PowerShell

PowerShell 用于从命令行管理资源。 当你想要生成使用 Azure 资源管理器模型的自动化的工具时，可以使用 PowerShell。 一个 PowerShell 模块，可以定义为一组分组来管理的特定区域的所有方面的 PowerShell 函数。 若要使用 Azure Stack，将需要能够同时处理不同的 PowerShell cmdlet 集。

本文可帮助你自行获取定向到 Azure Stack 上使用这些不同的 PowerShell 模块。 有四个组的 Api，你可以在 Azure Stack 上使用 PowerShell 时与交互。

| API | PowerShell 参考 | REST 参考 |
| --- | --- | --- |
| 1.全局 Azure 资源管理器 | [Azure PowerShell 模块](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [REST API 浏览器](https://docs.microsoft.com/rest/api/) |
| 2.Azure Stack Resource Manager | [在 Azure Stack 中管理 API 版本配置文件](azure-stack-version-profiles.md) | [在 Azure Stack 中管理 API 版本配置文件](azure-stack-version-profiles.md) |
| 3.Azure Stack 管理员终结点 | [Azure Stack 管理模块](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [REST API 浏览器-Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| 4.Azure Stack 特权终结点 | [在 Azure Stack 中使用特权终结点](../operator/azure-stack-privileged-endpoint.md) | |

每个接口联系全局 Azure 或 Azure Stack 中的资源提供程序。 资源提供程序启用 Azure 功能。 例如，Azure 计算资源提供程序为您以编程方式访问创建和管理虚拟机和其支持的资源。

资源提供程序提供功能，但还为管理和配置资源的控件。 可以以编程方式访问使用 Azure 资源管理器，资源提供程序和接口，提供用于 PowerShell、 Azure CLI 和 REST 客户端的面。

## <a name="where-to-find-azure-stack-powershell"></a>在哪里可以找到 Azure Stack PowerShell

以下块图显示了 PowerShell 模块的每个集的关系。 从你的计算机，可以加载 PowerShell 模块和管理全局 Azure 和 Azure Stack。

![Azure Stack Powershell](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>全球 Azure

Azure PowerShell 提供了一组用于管理 Azure 资源使用 Azure 资源管理器模型的当前版本的 cmdlet。 Azure PowerShell 使用了 .NET Standard，这使得它可用于 Windows、macOS 和 Linux。 还可以在 Azure Cloud Shell 中使用 Azure PowerShell。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manager

Azure Stack PowerShell 提供了一组在 Azure Stack 中使用早期版本的 Azure 资源管理器资源提供程序与兼容的 cmdlet。 在 Azure Stack 中的每个资源提供程序使用全球 Azure 中找到的提供程序的较旧版本。 为了帮助你协调 Azure Stack 支持的每个提供程序的版本，可以使用 API 配置文件。 Azure Stack PowerShell 使用 PowerShell 5.1，并在 Windows 上才可用。 有关详细信息，请参阅[在 Azure Stack 中的管理 API 版本配置文件](azure-stack-version-profiles.md)。

### <a name="azure-stack-administrator"></a>Azure Stack 管理员

Azure Stack 公开资源提供程序以便安装和维护 Azure Stack 云操作员到一的组。 全球 Azure 中这种交互是从用户抽象出来，并作为 Azure 的一部分在后台处理。 Azure Stack，但是，提供企业支持私有云的能力。 若要执行这些任务，该运算符与 Azure Stack 管理员 Api 进行交互。 有关详细信息，请参阅[安装适用于 Azure Stack 的 PowerShell](../operator/azure-stack-powershell-install.md)。

### <a name="azure-stack-privileged-endpoint"></a>Azure Stack 特权终结点

运算符活动在 Azure Stack 中，例如测试安装和访问日志，运算符可以进行交互的特权终结点 (PEP)。 PEP 是预配置的远程 PowerShell 控制台，为操作员提供了只是足够的访问权限来帮助执行特定任务。 终结点使用 PowerShell JEA (Just Enough Administration) 来公开一组受限的 cmdlet。 有关详细信息，请参阅[在 Azure Stack 中使用特权终结点](../operator/azure-stack-privileged-endpoint.md)。

### <a name="azurestack-tools"></a>AzureStack-Tools

此外，Azure Stack 提供的脚本和 GitHub 存储库，Azure Stack 工具中可用的其他 cmdlet。 Azure Stack 工具托管管理以及将资源部署到 Azure Stack 的 PowerShell 模块。 如果你打算建立 VPN 连接，则可将这些 PowerShell 模块下载到 Azure Stack 开发工具包或基于 Windows 的外部客户端。 有关详细信息，请参阅[Azurestack-tools](https://github.com/Azure/AzureStack-Tools)。

## <a name="working-with-powershell-on-azure-stack"></a>使用 Azure Stack 上的 PowerShell

PowerShell 提供了使用 Azure 资源管理器进行交互的编程方法。 可以使用交互式命令提示，也可以编写脚本自动任务。

如果您花太多时间来处理 Azure Stack PowerShell，您会发现自己安装和重新安装这些模块。 如果您正在使用全球 Azure 同时由于您将需要卸载并重新安装模块，具体取决于你的目标，这极具挑战性。 可以使用 Docker 容器来隔离每个版本的 PowerShell 在本地计算机上。 [使用 Docker 来运行 PowerShell](azure-stack-powershell-user-docker.md)探讨，以便您可以切换设置为 PowerShell 模块集的 PowerShell 模块中使用 Docker 容器。


## <a name="next-steps"></a>后续步骤

- 阅读有关[API 配置文件适用于 PowerShell](azure-stack-version-profiles.md) Azure Stack 上。
- [安装 Azure Stack Powershell](../operator/azure-stack-powershell-install.md)
- 了解如何创建[Azure 资源管理器模板](azure-stack-develop-templates.md)云保持一致。