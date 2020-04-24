---
title: Azure Stack Hub 中的 PowerShell
description: Azure Stack Hub 中的 PowerShell 具有一些模块和上下文。
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: cf9d17bda49c1fe5d401481d79b61b18442ee7ef
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77703802"
---
# <a name="get-started-with-powershell-in-azure-stack-hub"></a>Azure Stack Hub 中的 PowerShell 入门

PowerShell 用于从命令行管理资源。 若要生成使用 Azure 资源管理器模型的自动化工具，可以使用 PowerShell。 可将 PowerShell 模块定义为一组 PowerShell 函数，用于对特定的区域进行全方面的管理。 若要使用 Azure Stack Hub，需要能够处理不同的 PowerShell cmdlet 集。

本文可帮助你熟悉 Azure Stack Hub 中使用的各种 PowerShell 模块。 在 Azure Stack Hub 中使用 PowerShell 时，可与任意四组 API 交互，如下表所示：

| API | PowerShell 参考 | REST 参考 |
| --- | --- | --- |
| 全局 Azure 资源管理器 | [Azure PowerShell 模块](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [REST API 浏览器](https://docs.microsoft.com/rest/api/) |
| Azure Stack Hub 资源管理器 | [管理 Azure Stack Hub 中的 API 版本配置文件](azure-stack-version-profiles.md) | [管理 Azure Stack Hub 中的 API 版本配置文件](azure-stack-version-profiles.md) |
| Azure Stack Hub 管理员终结点 | [Azure Stack Hub 管理模块](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [REST API 浏览器 - Azure Stack Hub](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| Azure Stack Hub 特权终结点 | [使用 Azure Stack Hub 中的特权终结点](../operator/azure-stack-privileged-endpoint.md) | |

每个接口联系全局 Azure 或 Azure Stack 中心中的资源提供程序。 资源提供程序启用 Azure 功能。 例如，Azure 计算资源提供程序可让你以编程方式访问虚拟机及其支持资源的创建和管理。

资源提供程序不仅提供功能，而且还提供用于管理和配置资源的控件。 可以使用 Azure 资源管理器以编程方式访问资源提供程序。 然后，界面会提供适用于 PowerShell、Azure CLI 和你自己的 REST 客户端的图面。

## <a name="where-to-find-azure-stack-hub-powershell"></a>在何处可以找到 Azure Stack Hub PowerShell

以下框图显示了 PowerShell 模块集之间的关系。 在计算机上，可以加载 PowerShell 模块并管理全局 Azure 和 Azure Stack 中心。

![Azure Stack Hub PowerShell](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>全球 Azure

Azure PowerShell 包含一组使用最新版 Azure 资源管理器处理 Azure 资源的 cmdlet。 Azure PowerShell 使用 .NET Standard 版本，这意味着你可以使用 Windows、macOS 和 Linux 上的 PowerShell 版本。 还可以在 Azure Cloud Shell 中使用 Azure PowerShell。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)。

### <a name="azure-stack-hub-resource-manager"></a>Azure Stack 中心资源管理器

Azure Stack 集线器 PowerShell 提供一组使用以前版本的 Azure 资源管理器的 cmdlet。 这些 cmdlet 与 Azure Stack 集线器中的资源提供程序兼容。 Azure Stack 中心中的每个资源提供程序都使用在全球 Azure 中找到的较早版本的提供程序。 若要帮助协调 Azure Stack 中心支持的每个提供程序的版本，可以使用 API 配置文件。 Azure Stack 中心 PowerShell 使用 PowerShell 5.1，仅在 Windows 上可用。 有关详细信息，请参阅[在 Azure Stack Hub 中管理 API 版本配置文件](azure-stack-version-profiles.md)。

### <a name="azure-stack-hub-administrator"></a>Azure Stack 中心管理员

Azure Stack 中心向云操作员公开一组资源提供程序，以便操作员可以安装和维护 Azure Stack 中心。 在全球 Azure 中，这种交互是从用户抽象的，并在后台作为 Azure 的一部分进行处理。 但是，对于 Azure Stack 中心，企业可以支持私有云。 若要执行这些任务，操作员会与 Azure Stack 中心管理 Api 交互。 有关详细信息，请参阅为[Azure Stack 集线器安装 PowerShell](../operator/azure-stack-powershell-install.md)。

### <a name="azure-stack-hub-privileged-endpoint"></a>Azure Stack 中心特权终结点

对于 Azure Stack 集线器中的操作员活动，如测试安装和访问日志，操作员可以与特权终结点（PEP）交互。 PEP 是预先配置的远程 PowerShell 控制台，可为操作员提供刚好足够的访问权限来让他们完成特定的任务。 终结点使用 PowerShell Just Enough Administration (JEA) 公开一组受限的 cmdlet。 有关详细信息，请参阅[使用 Azure Stack 集线器中的特权终结点](../operator/azure-stack-privileged-endpoint.md)。

### <a name="azure-stack-hub-tools"></a>Azure Stack 中心工具

Azure Stack 集线器使脚本和其他 cmdlet 在 GitHub 存储库*test-azurestack*中可用。 Test-azurestack 是托管 PowerShell 模块，用于管理资源并将资源部署到 Azure Stack 中心。 如果你打算建立 VPN 连接，则可将这些 PowerShell 模块下载到 Azure Stack 开发工具包或基于 Windows 的外部客户端。 有关详细信息，请访问 [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) 页。

## <a name="work-with-powershell-in-azure-stack-hub"></a>在 Azure Stack 集线器中使用 PowerShell

PowerShell 提供编程的方式来与 Azure 资源管理器交互。 可以使用交互式命令提示符；若要将任务自动化，可以编写脚本。

如果花费了大量时间来处理 Azure Stack 集线器 PowerShell，会发现自己安装和重新安装这些模块。 如果你同时使用全局 Azure，此例程可能会很困难，因为你需要卸载并重新安装模块，具体取决于你的目标。 

可以使用 Docker 容器来隔离本地计算机上的每个 PowerShell 版本。 若要使用 Docker 容器以便在不同的 PowerShell 模块集之间切换，请参阅[使用 Docker 运行 PowerShell](azure-stack-powershell-user-docker.md)。


## <a name="next-steps"></a>后续步骤

- 在 Azure Stack Hub 中了解[PowerShell 的 API 配置文件](azure-stack-version-profiles.md)。
- 安装[Azure Stack Hub Powershell](../operator/azure-stack-powershell-install.md)。
- 了解如何创建 [Azure 资源管理器模板](azure-stack-develop-templates.md)以实现云一致性。
