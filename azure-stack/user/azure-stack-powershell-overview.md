---
title: Azure Stack 中的 PowerShell |Microsoft Docs
description: PowerShell 在 Azure Stack 中的有许多模块和上下文。
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
ms.openlocfilehash: 8f5d97969c96e8f3546d37ffed28b6332d80dc05
ms.sourcegitcommit: 2ee75ded704e8cfb900d9ac302d269c54a5dd9a3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/30/2019
ms.locfileid: "66394426"
---
# <a name="get-started-with-powershell-in-azure-stack"></a>开始使用 Azure Stack 中的 PowerShell

PowerShell 用于从命令行管理资源。 当你想要生成使用 Azure 资源管理器模型的自动化的工具时，可以使用 PowerShell。 一个 PowerShell 模块，可以定义为一组分组来管理的特定区域的所有方面的 PowerShell 函数。 若要使用 Azure Stack，需要能够同时处理不同组的 PowerShell cmdlet。

本文可帮助你确定自己的 Azure Stack 中使用的 PowerShell 模块的不同方向。 当在 Azure Stack 中使用 PowerShell 时，可以交互使用任何以下四组 Api 下, 表中所示：

| API | PowerShell 参考 | REST 参考 |
| --- | --- | --- |
| 全局 Azure 资源管理器 | [Azure PowerShell 模块](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [REST API 浏览器](https://docs.microsoft.com/rest/api/) |
| Azure Stack Resource Manager | [在 Azure Stack 中管理 API 版本配置文件](azure-stack-version-profiles.md) | [在 Azure Stack 中管理 API 版本配置文件](azure-stack-version-profiles.md) |
| Azure Stack 管理员终结点 | [Azure Stack 管理模块](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [REST API 浏览器-Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| Azure Stack 特权终结点 | [在 Azure Stack 中使用特权终结点](../operator/azure-stack-privileged-endpoint.md) | |

每个接口联系全局 Azure 或 Azure Stack 中的资源提供程序。 资源提供程序启用 Azure 功能。 例如，Azure 计算资源提供程序为您以编程方式访问创建和管理虚拟机和其支持的资源。

资源提供程序提供的功能和控件用于管理和配置资源。 使用 Azure 资源管理器，可以以编程方式访问资源提供程序。 反过来，该接口提供用于 PowerShell、 Azure CLI 和 REST 客户端的面。

## <a name="where-to-find-azure-stack-powershell"></a>在哪里可以找到 Azure Stack PowerShell

以下块图显示了 PowerShell 模块在组之间的关系。 从你的计算机，可以加载 PowerShell 模块和管理全局 Azure 和 Azure Stack。

![Azure Stack Powershell](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>全球 Azure

Azure PowerShell 包含一组使用的当前版本的 Azure 资源管理器中使用 Azure 资源的 cmdlet。 Azure PowerShell 使用.NET Standard 版本，这意味着您可以使用 Windows、 macOS 和 Linux 使用 PowerShell 的版本。 还可以在 Azure Cloud Shell 中使用 Azure PowerShell。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

### <a name="azure-stack-resource-manager"></a>Azure Stack Resource Manager

Azure Stack PowerShell 提供了一组使用以前版本的 Azure 资源管理器中的 cmdlet。 这些 cmdlet 可以在 Azure Stack 中的资源提供程序与兼容。 在 Azure Stack 中的每个资源提供程序使用全球 Azure 中找到的提供程序的较旧版本。 为了帮助你协调 Azure Stack 支持每个提供程序的版本，可以使用 API 配置文件。 Azure Stack PowerShell 使用 PowerShell 5.1 并只能在 Windows 上。 有关详细信息，请参阅[在 Azure Stack 中的管理 API 版本配置文件](azure-stack-version-profiles.md)。

### <a name="azure-stack-administrator"></a>Azure Stack 管理员

Azure Stack，以便安装和维护 Azure Stack 操作员可以公开一组资源提供程序到云操作员。 在全球 Azure，这种交互是从用户抽象出来，作为 Azure 的一部分在后台处理。 使用 Azure Stack 中，但是，企业可以支持私有云。 若要执行这些任务，该运算符进行交互与 Azure Stack 管理员 Api。 有关详细信息，请参阅[安装适用于 Azure Stack 的 PowerShell](../operator/azure-stack-powershell-install.md)。

### <a name="azure-stack-privileged-endpoint"></a>Azure Stack 特权终结点

运算符活动在 Azure Stack 中，例如测试安装和访问日志，运算符可以进行交互的特权终结点 (PEP)。 PEP 是预配置的远程 PowerShell 控制台，为运算符提供足够的访问权限来执行特定任务。 终结点使用 PowerShell Just Enough Administration (JEA) 来公开一组受限的 cmdlet。 有关详细信息，请参阅[在 Azure Stack 中使用特权终结点](../operator/azure-stack-privileged-endpoint.md)。

### <a name="azure-stack-tools"></a>Azure Stack 工具

Azure Stack 使脚本和其他 cmdlet 可在 GitHub 存储库*Azurestack-tools*。 Azure Stack 工具托管管理以及将资源部署到 Azure Stack 的 PowerShell 模块。 如果您打算建立 VPN 连接，您可以下载这些 PowerShell 模块，到 Azure Stack 开发工具包或基于 Windows 的外部客户端。 有关详细信息，请转到[Azurestack-tools](https://github.com/Azure/AzureStack-Tools)页。

## <a name="work-with-powershell-in-azure-stack"></a>适用于 Azure Stack 中的 PowerShell

PowerShell 提供了使用 Azure 资源管理器进行交互的编程方法。 您可以使用交互式命令提示，或者，如果要自动执行任务，您可以编写脚本。

如果您花太多时间来处理 Azure Stack PowerShell，您会发现自己安装和重新安装这些模块。 如果在同一时间使用全球 Azure，此例程很难，因为你将需要卸载并重新安装模块，具体取决于你的目标。 

可以使用 Docker 容器来隔离每个版本的 PowerShell 在本地计算机上。 若要使用 Docker 容器，以便可以从 PowerShell 模块设置为 PowerShell 模块将切换，请参阅[使用 Docker 来运行 PowerShell](azure-stack-powershell-user-docker.md)。


## <a name="next-steps"></a>后续步骤

- 阅读有关[API 配置文件适用于 PowerShell](azure-stack-version-profiles.md) Azure Stack 中。
- 安装[Azure Stack Powershell](../operator/azure-stack-powershell-install.md)。
- 了解如何创建[Azure 资源管理器模板](azure-stack-develop-templates.md)云保持一致。
