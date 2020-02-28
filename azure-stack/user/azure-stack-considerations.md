---
title: 使用服务和构建应用时 Azure Stack 中心与 Azure 之间的差异
description: 了解使用服务和构建应用时 Azure 与 Azure Stack 中心之间的差异。
author: sethmanheim
ms.topic: overview
ms.date: 01/06/2020
ms.author: sethm
ms.lastreviewed: 12/27/2019
ms.openlocfilehash: 7b3f8fe96ddd33550f28860c0eaa77fa8cfa8c84
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704346"
---
# <a name="differences-between-azure-stack-hub-and-azure-when-using-services-and-building-apps"></a>使用服务和构建应用时 Azure Stack 中心与 Azure 之间的差异

在为 Azure Stack 中心使用服务或生成应用之前，请务必了解 Azure Stack 中心与 Azure 之间的差异。 本文介绍了使用 Azure Stack 集线器作为混合云开发环境时的不同功能和关键注意事项。

## <a name="overview"></a>概述

Azure Stack 中心是一种混合云平台，可让你从公司或服务提供商的数据中心使用 Azure 服务。 可以在 Azure Stack 集线器上构建应用，然后将其部署到 Azure Stack 中心、Azure 或 Azure 混合云。

你的 Azure Stack 中心操作员告诉你哪些服务可供你使用，以及如何获取支持。 它们通过自定义计划和产品/服务提供这些服务。

[Azure 技术文档内容](/azure)假设正在为 azure 服务开发应用，而不是为 Azure Stack 集线器开发应用。 生成应用并将其部署到 Azure Stack 中心时，必须了解一些主要区别，例如：

* Azure Stack 中心提供 Azure 中可用的服务和功能的子集。
* 你的公司或服务提供商可以选择他们想要提供的服务。 可用的选项可能包括自定义的服务或应用程序。 它们可能会提供自己的自定义文档。
* 必须使用正确的 Azure Stack 集线器特定终结点（例如，门户地址和 Azure 资源管理器终结点的 Url）。
* 必须使用 Azure Stack 集线器支持的 PowerShell 和 API 版本。 使用受支持的版本可确保你的应用在 Azure Stack 中心和 Azure 中都能正常工作。

## <a name="cheat-sheet-high-level-differences"></a>备忘单：高级差异

下表介绍 Azure Stack 中心与 Azure 之间的高级别差异。 为 Azure Stack 中心进行开发或使用 Azure Stack 中心服务时，请记住这些差异：

| 区域 | Azure （全局） | Azure Stack 中心 |
| -------- | ------------- | ----------|
| 谁在运行？ | Microsoft | 你的组织或服务提供商。|
| 你要联系谁获得支持？ | Microsoft | 对于集成系统，请联系你的 Azure Stack 中心操作员（在你的组织或服务提供商处）以获得支持。<br><br>有关 Azure Stack 开发工具包（ASDK）支持，请访问[Microsoft 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack)。 由于开发工具包是一个评估环境，因此我们不会通过 Microsoft 客户支持服务（CSS）提供官方支持。
| 可用服务 | 请参阅[Azure 产品](https://azure.microsoft.com/services/?b=17.04b)列表。 可用服务因 Azure 区域而异。 | Azure Stack 中心支持部分 Azure 服务。 实际服务因你的组织或服务提供商选择提供的服务而异。
| Azure 资源管理器终结点 * | https://management.azure.com | 对于 Azure Stack 集线器集成系统，请使用 Azure Stack 中心操作员提供的终结点。<br><br>对于开发工具包，请使用： https://management.local.azurestack.external。
| 门户 URL * | [https://portal.azure.com](https://portal.azure.com) | 对于 Azure Stack 集线器集成系统，请使用 Azure Stack 中心操作员提供的 URL。<br><br>对于开发工具包，请使用： https://portal.local.azurestack.external。
| 区域 | 您可以选择要部署到的区域。 | 对于 Azure Stack 集线器集成系统，请使用系统中可用的区域。<br><br>对于 Azure Stack 开发工具包（ASDK），该区域将始终为 "**本地**"。
| 资源组 | 资源组可以跨区域。 | 对于集成系统和开发工具包，仅有一个区域。
|支持的命名空间、资源类型和 API 版本 | 尚未弃用的最新版本（或更早的版本）。 | Azure Stack 中心支持特定版本。 请参阅本文的[版本要求](#version-requirements)部分。
| | |

\* 如果你是 Azure Stack 中心运营商，请参阅[使用管理员门户](../operator/azure-stack-manage-portals.md)和[管理基础知识](../operator/azure-stack-manage-basics.md)获取详细信息。

## <a name="helpful-tools-and-best-practices"></a>有用的工具和最佳做法

Microsoft 提供的工具和指南可帮助你针对 Azure Stack 中心进行开发。

| 建议 | 参考 |
| -------- | ------------- |
| 在开发人员工作站上安装正确的工具。 | - [安装 PowerShell](../operator/azure-stack-powershell-install.md)<br>- [下载工具](../operator/azure-stack-powershell-download.md)<br>- [配置 PowerShell](azure-stack-powershell-configure-user.md)<br>- [安装 Visual Studio](azure-stack-install-visual-studio.md)
| 查看有关以下各项的信息：<br>-Azure 资源管理器模板注意事项。<br>-如何查找快速入门模板。<br>-使用策略模块帮助你使用 Azure 针对 Azure Stack 中心进行开发。 | [针对 Azure Stack 中心进行开发](azure-stack-developer.md) |
| 查看并遵循模板的最佳实践。 | [资源管理器快速入门模板](https://aka.ms/aa6yz42)
| | |

## <a name="version-requirements"></a>版本要求

Azure Stack 中心支持 Azure PowerShell 和 Azure 服务 Api 的特定版本。 使用受支持的版本，确保你的应用可以同时部署到 Azure Stack 中心和 Azure。

若要确保使用正确版本的 Azure PowerShell，请使用[API 版本配置文件](azure-stack-version-profiles.md)。 若要确定可以使用的最新 API 版本配置文件，请找出正在使用的 Azure Stack 集线器的内部版本。 可以从 Azure Stack 中心管理员那里获取此信息。

> [!NOTE]
> 如果你使用的是 Azure Stack 开发工具包并且具有管理访问权限，请参阅[确定当前版本](../operator/azure-stack-updates.md)部分以确定 Azure Stack 中心生成。

对于其他 Api，请运行以下 PowerShell 命令，以输出 Azure Stack 中心订阅支持的命名空间、资源类型和 API 版本（在属性级别可能仍存在差异）。 若要运行此命令，你必须已为 Azure Stack 中心环境[安装](../operator/azure-stack-powershell-install.md)并[配置](azure-stack-powershell-configure-user.md)了 PowerShell。 还必须拥有对 Azure Stack 中心产品/服务的订阅。

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

示例输出（已截断）： ![Register-azurermresourceprovider 命令的示例输出](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>后续步骤

有关服务级别差异的详细信息，请参阅：

* [Azure Stack 中心中的 Vm 的注意事项](azure-stack-vm-considerations.md)
* [Azure Stack 集线器中的存储注意事项](azure-stack-acs-differences.md)
* [Azure Stack 集线器网络的注意事项](azure-stack-network-differences.md)
