---
title: 使用服务和生成应用时 Azure Stack Hub 与 Azure 之间的差异
description: 了解使用服务和生成应用时 Azure 与 Azure Stack Hub 之间的差异。
author: sethmanheim
ms.topic: overview
ms.date: 11/20/2020
ms.author: sethm
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 8d8cd26bc53deef5b2e23955b349cb68a0eb51f0
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2020
ms.locfileid: "95516981"
---
# <a name="differences-between-azure-stack-hub-and-azure-when-using-services-and-building-apps"></a>使用服务和生成应用时 Azure Stack Hub 与 Azure 之间的差异

使用 Azure Stack Hub 的服务或开发适用于 Azure Stack Hub 的应用之前，必须了解 Azure Stack Hub 与 Azure 之间的差异。 本文阐述在将 Azure Stack Hub 用作混合云开发环境时的不同功能和重要注意事项。

## <a name="overview"></a>概述

Azure Stack Hub 是一个混合云平台，可用于通过公司或服务提供商的数据中心使用 Azure 服务。 可在 Azure Stack Hub 上开发应用，然后将其部署到 Azure Stack Hub、Azure 或 Azure 混合云。

Azure Stack Hub 运营商会告知有哪些服务可供你使用，以及如何获取支持。 他们通过自定义计划和套餐来提供这些服务。

[Azure 技术文档内容](/azure)假设应用是为 Azure 服务（而不是 Azure Stack Hub）开发的。 开发应用并将其部署到 Azure Stack Hub 时，必须了解一些主要差异，例如：

* Azure Stack Hub 提供 Azure 所提供的一部分服务和功能。
* 你的公司或服务提供商可以选择他们想要提供的服务。 可用的选项可能包括自定义的服务或应用程序。 他们可以提供自己的自定义文档。
* 使用正确的 Azure Stack Hub 特定终结点（例如，门户地址和 Azure 资源管理器终结点的 URL）。
* 必须使用 Azure Stack Hub 支持的 PowerShell 和 API 版本。 使用支持的版本可确保应用在 Azure Stack Hub 和 Azure 中运行。

## <a name="cheat-sheet-high-level-differences"></a>速查表：大致差异

下表描述了 Azure Stack Hub 与 Azure 之间的大致差异。 针对 Azure Stack Hub 进行开发或使用 Azure Stack Hub 服务时，请记住这些差异：

| 区域 | Azure（公有云） | Azure Stack Hub |
| -------- | ------------- | ----------|
| 由谁运营？ | Microsoft | 你的组织或服务提供商。|
| 寻求支持的联系对象是谁？ | Microsoft | 对于集成环境，请联系你的组织或服务提供商的 Azure Stack Hub 运营商来获取支持。<br><br>有关 Azure Stack 开发工具包 (ASDK) 支持，请访问 [Microsoft 论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack)。 由于开发工具包是一个评估环境，因此没有通过 Microsoft 支持部门提供的官方支持。
| 可用服务 | 参阅 [Azure 产品](https://azure.microsoft.com/services/?b=17.04b)列表。 可用服务因 Azure 区域而异。 | Azure Stack Hub 支持部分 Azure 服务。 实际服务因你的组织或服务提供商选择提供的服务而异。
| Azure 资源管理器终结点* | `https://management.azure.com` | 对于 Azure Stack Hub 集成系统，请使用 Azure Stack Hub 操作员提供的终结点。<br><br>对于开发工具包，请使用 `https://management.local.azurestack.external`。
| 门户 URL* | [https://portal.azure.com](https://portal.azure.com) | 对于 Azure Stack Hub 集成系统，请使用 Azure Stack Hub 操作员提供的 URL。<br><br>对于开发工具包，请使用 `https://portal.local.azurestack.external`。
| 区域 | 可以选择要部署到的区域。 | 对于 Azure Stack Hub 集成系统，请使用系统中可用的区域。<br><br>对于 Azure Stack 开发工具包 (ASDK)，该区域始终为“本地”。
| 资源组 | 资源组可以跨区域。 | 对于集成系统和开发工具包，只有一个区域。
|支持的命名空间、资源类型和 API 版本 | 最新版本（或未弃用的较低版本）。 | Azure Stack Hub 支持特定的版本。 请参阅本文的[版本要求](#version-requirements)部分。
| | |

*如果你是 Azure Stack Hub 操作员，请参阅[使用管理员门户](../operator/azure-stack-manage-portals.md)和[管理基础知识](../operator/azure-stack-manage-basics.md)来了解详细信息。

## <a name="helpful-tools-and-best-practices"></a>有用的工具和最佳做法

Microsoft 提供的工具和指南可帮助你针对 Azure Stack 中心进行开发。

| 建议 | 参考 |
| -------- | ------------- |
| 在开发人员工作站上安装正确的工具。 | - [安装 PowerShell](../operator/powershell-install-az-module.md)<br>- [下载工具](../operator/azure-stack-powershell-download.md)<br>- [配置 PowerShell](azure-stack-powershell-configure-user.md)<br>- [安装 Visual Studio](azure-stack-install-visual-studio.md)
| 查看有关以下各项的信息：<br>Azure 资源管理器模板注意事项。<br>- 如何查找快速入门模板。<br>- 借助策略模块使用 Azure 进行 Azure Stack Hub 方面的开发。 | [为 Azure Stack Hub 进行开发](azure-stack-developer.md) |
| 查看并遵循模板最佳做法。 | [资源管理器快速入门模板](https://aka.ms/aa6yz42)
| | |

## <a name="version-requirements"></a>版本要求

Azure Stack Hub 支持特定版本的 Azure PowerShell 和 Azure 服务 API。 使用支持的版本，确保可将应用部署到 Azure Stack Hub 和 Azure。

若要确保使用正确版本的 Azure PowerShell，请使用 [API 版本配置文件](azure-stack-version-profiles.md)。 若要确定可以使用的最新 API 版本配置文件，请找出使用的 Azure Stack Hub 内部版本。 可以从 Azure Stack Hub 管理员获取此信息。

> [!NOTE]
> 如果使用 Azure Stack 开发工具包，并且拥有管理访问权限，请参阅[确定当前版本](../operator/azure-stack-updates.md)部分来确定 Azure Stack Hub 内部版本。

对于其他 API，请运行以下 PowerShell 命令输出 Azure Stack Hub 订阅中支持的命名空间、资源类型和 API 版本（在属性级别上可能仍然存在差异）。 若要正常运行此命令，必须事先[安装](../operator/powershell-install-az-module.md)并[配置](azure-stack-powershell-configure-user.md)适用于 Azure Stack Hub 环境的 PowerShell。 此外，必须有 Azure Stack Hub 套餐的订阅。

### <a name="az-modules"></a>[Az 模块](#tab/az)

```powershell
Get-AzResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```
### <a name="azurerm-modules"></a>[AzureRM 模块](#tab/azurerm)

```powershell
Get-AzureRMResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

---




示例输出 (截断) ： ![ Get-AzResourceProvider 命令的示例输出](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>后续步骤

有关服务级别的差异的详细信息，请参阅：

* [Azure Stack Hub 中的 VM 注意事项](azure-stack-vm-considerations.md)
* [Azure Stack Hub 中的存储注意事项](azure-stack-acs-differences.md)
* [Azure Stack Hub 网络注意事项](azure-stack-network-differences.md)
* [Azure Stack Hub SQL 资源提供程序的注意事项](../operator/azure-stack-sql-resource-provider.md)
