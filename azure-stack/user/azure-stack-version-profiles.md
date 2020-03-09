---
title: 在 Azure Stack 中心管理 API 版本配置文件
description: 了解 Azure Stack 集线器中的 API 版本配置文件。
author: sethmanheim
ms.topic: article
ms.date: 01/06/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 07/24/2019
ms.openlocfilehash: 2e229c8add5b850384c7b46d05373c4ee19fd266
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364126"
---
# <a name="manage-api-version-profiles-in-azure-stack-hub"></a>在 Azure Stack 中心管理 API 版本配置文件

API 配置文件指定 Azure 资源提供程序和 Azure REST 终结点的 API 版本。 你可以使用 API 配置文件创建采用不同语言的自定义客户端。 每个客户端都使用 API 配置文件来联系 Azure Stack 中心正确的资源提供程序和 API 版本。

你可以创建一个应用来使用 Azure 资源提供程序，而无需确切地对每个资源提供程序 API 的哪个版本与 Azure Stack 中心兼容。 只需将应用程序与配置文件对齐，SDK 就会恢复到正确的 API 版本。

本主题将帮助你：

- 了解 Azure Stack 集线器的 API 配置文件。
- 了解如何使用 API 配置文件来开发解决方案。
- 请参阅在何处查找特定于代码的指南。

## <a name="summary-of-api-profiles"></a>API 配置文件摘要

- API 配置文件用于表示一组 Azure 资源提供程序及其 API 版本。
- 创建 API 配置文件是为了跨多个 Azure 云创建模板。 配置文件提供了一个兼容的稳定接口。
- 配置文件每年发布四次。
- 使用三个配置文件命名约定：
  - **最近**  
        包含在全球 Azure 中发布的最新 API 版本。
  - **yyyy-mm-dd-混合**  
    此版本每年发布，侧重于跨多个云的一致性和稳定性。 此配置文件面向最佳 Azure Stack 集线器兼容性。
  - **yyyy-mm-dd-配置文件** <br>
    平衡最佳稳定性和最新功能。

## <a name="azure-api-profiles-and-azure-stack-hub-compatibility"></a>Azure API 配置文件和 Azure Stack 集线器兼容性

最新的 Azure API 配置文件与 Azure Stack 中心不兼容。 使用以下命名约定来确定要用于 Azure Stack 中心解决方案的配置文件：

**最新**  
此配置文件具有在全球 Azure 中找到的最新 API 版本，这些版本在 Azure Stack Hub 中不起作用。 **最新**具有最大数量的重大更改。 此配置文件与其他云保持稳定性和兼容性。 如果你正在尝试使用最新的 API 版本，**最新**版本是你应使用的配置文件。

**Yyyy-mm-dd-混合**  
此配置文件每年3月和9月发布。 它具有与各种云的最佳稳定性和兼容性，旨在面向全球 Azure 和 Azure Stack 中心。 此配置文件中列出的 Azure API 版本将与 Azure Stack 集线器中列出的版本相同。 使用此配置文件开发混合云解决方案的代码。

**yyyy-mm-dd-配置文件**  
此配置文件在六月和十二月为全球 Azure 发布。 它无法与 Azure Stack 中心一起使用，并且通常会发生很多重大更改。 尽管它会平衡最佳稳定性和最新功能，但最**新与**此配置文件之间的区别是**最新**始终包含最新的 api 版本，而不考虑何时发布 API。 例如，如果为计算 API 创建了一个新的 API 版本明天，则该 API 版本将在**最新**版本中列出，而不是在**yyyy-mm-dd**配置文件中列出，因为此配置文件已存在。 **yyyy-mm-dd-profile**涵盖6月之前或之前发布的最新版本。

## <a name="azure-resource-manager-api-profiles"></a>Azure 资源管理器 API 配置文件

Azure Stack 中心不使用在全球 Azure 中找到的最新版本的 API 版本。 创建解决方案时，必须找到与 Azure Stack 中心兼容的每个 Azure 资源提供程序的 API 版本。

您可以使用 API 配置文件，而不是研究每个资源提供程序和 Azure Stack 中心支持的特定版本。 配置文件指定一组资源提供程序和 API 版本。 SDK （或使用 SDK 生成的工具）将还原到配置文件中指定的目标 `api-version`。 使用 API 配置文件，你可以指定应用于整个模板的配置文件版本。 在运行时，Azure 资源管理器会选择正确的资源版本。

API 配置文件适用于使用 Azure 资源管理器（例如 PowerShell、Azure CLI、SDK 中提供的代码和 Microsoft Visual Studio）的工具。 工具和 Sdk 可以使用配置文件来读取在生成应用时要包括的模块和库的版本。

例如，如果你使用 PowerShell 通过支持**api 版本**2016-03-30 的**microsoft. 存储** **资源提供**程序创建存储帐户，并使用**api 版本**2015-12-01 来支持 VM，则必须查找哪个 PowerShell 模块支持存储的2016-03-30 以及哪些模块支持用于计算的2015-02-01，然后安装它们。 可以改用配置文件。 使用 cmdlet `Install-Profile <profilename>`，PowerShell 将加载正确的模块版本。

同样，使用 Python SDK 生成基于 Python 的应用时，可以指定该配置文件。 SDK 会为您在脚本中指定的资源提供程序加载正确的模块。

作为开发人员，您可以集中精力编写您的解决方案。 你可以使用配置文件，而不是研究哪些 API 版本、资源提供程序和云一起工作，并了解你的代码在支持该配置文件的所有云上都有效。

## <a name="api-profile-code-samples"></a>API 配置文件代码示例

可以通过使用配置文件，查找代码示例，以帮助你将你的解决方案与 Azure Stack 中心集成在一起。 目前，可以找到以下语言的指南和示例：

- **.NET** <br>
使用 .NET API 配置文件获取资源提供程序包中每种资源类型的最新、最稳定的版本。 有关详细信息，请参阅[在 Azure Stack 集线器中将 API 版本配置文件与 .net 配合使用](azure-stack-version-profiles-net.md)。
- **PowerShell**  
使用通过 PowerShell 库提供的**AzureRM**模块来获取使用 API 版本配置文件所需的 PowerShell cmdlet。 有关信息，请参阅[使用 PowerShell 的 API 版本配置文件](azure-stack-version-profiles-powershell.md)。
- **Azure CLI**  
更新环境配置，以使用特定于 Azure Stack 集线器的 API 版本配置文件。 有关信息，请参阅[为 Azure CLI 使用 API 版本配置文件](azure-stack-version-profiles-azurecli2.md)。
- **Go**  
在中转 SDK 中，配置文件是不同资源类型与不同服务的不同版本的组合。 配置文件在配置文件/路径下提供，其版本采用**yyyy-mm-dd**格式。 有关信息，请参阅[使用 API 版本配置文件进行访问](azure-stack-version-profiles-go.md)。
- **Ruby**  
Azure Stack 中心的 Ruby SDK 资源管理器提供帮助你构建和管理基础结构的工具。 SDK 中的资源提供程序包括计算、虚拟网络和采用 Ruby 语言的存储。 有关信息，请参阅[在 Ruby 中使用 API 版本配置文件](azure-stack-version-profiles-ruby.md)。
- **Python**  
Python SDK 支持 API 版本配置文件以面向不同的云平台，如 Azure Stack 中心和全局 Azure。 使用 API 配置文件创建适用于混合云的解决方案。 有关信息，请参阅[将 API 版本配置文件与 Python 配合使用](azure-stack-version-profiles-python.md)。
- **Node.js**  
Azure Stack 中心的 node.js SDK 资源管理器提供帮助你构建和管理基础结构的工具。 有关详细信息，请参阅[将 API 版本配置文件与 node.js 配合使用](azure-stack-version-profile-nodejs.md)。

## <a name="next-steps"></a>后续步骤

- [为 Azure Stack 集线器安装 PowerShell](../operator/azure-stack-powershell-install.md)
- [配置 Azure Stack 集线器用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)
- [查看有关配置文件支持的资源提供程序 API 版本的详细信息](azure-stack-profiles-azure-resource-manager-versions.md)。
