---
title: 管理 Azure Stack Hub 中的 API 版本配置文件
description: 了解 Azure Stack Hub 中的 API 版本配置文件。
author: sethmanheim
ms.topic: article
ms.date: 05/04/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 07/24/2019
ms.openlocfilehash: f72f0e79fe7b890c65c630ff00ec3870d4756c5e
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836217"
---
# <a name="manage-api-version-profiles-in-azure-stack-hub"></a>管理 Azure Stack Hub 中的 API 版本配置文件

API 配置文件指定 Azure 资源提供程序和 Azure REST 终结点的 API 版本。 可以使用 API 配置文件以不同的语言创建自定义客户端。 每个客户端都使用 API 配置文件来与 Azure Stack Hub 的正确的资源提供程序和 API 版本通信。

你可以创建一个应用来使用 Azure 资源提供程序，而无需确切知道每个资源提供程序 API 与 Azure Stack 中心兼容的版本。 只需将应用对应到某个配置文件，SDK 就能还原到正确的 API 版本。

本主题帮助读者了解：

- Azure Stack Hub 的 API 配置文件。
- 了解如何使用 API 配置文件来开发解决方案。
- 查看在何处查找代码特定的指导。

## <a name="summary-of-api-profiles"></a>API 配置文件的摘要

- API 配置文件用于表示一组 Azure 资源提供程序及其 API 版本。
- API 配置文件是为了让你创建跨多个 Azure 云的模板而创建的。 配置文件提供兼容且稳定的接口。
- 配置文件每年发布四次。
- 使用了以下三个配置文件命名约定：
  - **latest**  
        包含全球 Azure 中发布的最新 API 版本。
  - **yyyy-mm-dd-hybrid**  
    每两年发布一次，此版本注重于跨多个云的一致性和稳定性。 此配置文件以实现最佳 Azure Stack Hub 兼容性为目标。
  - **yyyy-mm-dd-profile** <br>
    在最佳稳定性和最新功能之间进行均衡。

## <a name="azure-api-profiles-and-azure-stack-hub-compatibility"></a>Azure API 配置文件和 Azure Stack Hub 兼容性

最新 Azure API 配置文件与 Azure Stack Hub 不兼容。 使用以下命名约定来标识要将哪些配置文件用于 Azure Stack Hub 解决方案：

**最新**  
此配置文件包含可在全球 Azure 中找到的最新 API 版本，这些版本在 Azure Stack Hub 中不起作用。 **Latest** 包含的重大更改最多。 此配置文件不考虑稳定性以及与其他云的兼容性。 如果尝试使用最新的 API 版本，则应当使用 **Latest** 配置文件。

**Yyyy-mm-dd-hybrid**  
此配置文件在每年的三月份和九月份发布。 它具有最佳的稳定性以及与各种云的兼容性，面向全球 Azure 和 Azure Stack Hub。 此配置文件中列出的 Azure API 版本将与 Azure Stack Hub 中列出的相同。 使用此配置文件为混合云解决方案开发代码。

**yyyy-mm-dd-profile**  
此配置文件在六月份和九月份针对全球 Azure 发布。 它不适用于 Azure Stack Hub，并且通常会有许多中断性变更。 虽然它在最佳稳定性与最新功能之间进行了均衡，但**最新**配置文件与此配置文件之间的区别是**最新**配置文件始终包含最新的 API 版本，无论 API 是何时发布的。 例如，如果明天为“计算 API”创建一个新的 API 版本，则该 API 版本将列在**最新**配置文件中，但不会列在 **yyyy-mm-dd-profile** 中，因为此配置文件已经存在。 **yyyy-mm-dd-profile** 涵盖了六月份或九月份之前发布的最新版本。

## <a name="azure-resource-manager-api-profiles"></a>Azure 资源管理器 API 配置文件

Azure Stack Hub 不使用全球 Azure 中提供的最新版 API。 在创建解决方案时，必须为每个 Azure 资源提供程序查找与 Azure Stack Hub 兼容的 API 版本。

你可以使用 API 配置文件，而无需研究 Azure Stack Hub 支持的每个资源提供程序和特定版本。 配置文件会指定一组资源提供程序和 API 版本。 SDK 或者使用 SDK 生成的工具将还原到配置文件中指定的目标 `api-version`。 使用 API 配置文件，可以指定应用于整个模板的配置文件版本。 在运行时，Azure 资源管理器会选择正确的资源版本。

API 配置文件可与使用 Azure 资源管理器的工具（例如 PowerShell、Azure CLI、SDK 中提供的代码，以及 Microsoft Visual Studio）配合运行。 工具和 SDK 可以使用配置文件来读取生成应用时要包含的模块和库的版本。

例如，如果通过 PowerShell 使用支持 **api-version** 2016-03-30 的 **Microsoft.Storage** 资源提供程序创建存储帐户，并使用 **api-version** 为 2015-12-01 的 **Microsoft.Compute** 资源提供程序创建 VM，则必须查看哪个 PowerShell 模块支持将 2016-03-30 用于存储，以及哪个模块支持将 2015-02-01 用于计算，然后安装这两个模块。 可以改用配置文件。 使用 cmdlet `Install-Profile <profilename>`，然后 PowerShell 会加载正确的模块版本。

同样，在使用 Python SDK 生成基于 Python 的应用时，可以指定配置文件。 SDK 将为脚本中指定的资源提供程序加载正确的模块。

开发人员可以专注于编写解决方案。 无需研究哪些 API 版本、资源提供程序和云可一起运行，而可以使用配置文件，确定代码可跨所有支持该配置文件的云运行。

## <a name="api-profile-code-samples"></a>API 配置文件代码示例

可以借助代码示例，通过配置文件将采用偏好语言的解决方案与 Azure Stack Hub 相集成。 目前可以找到以下语言的指导和示例：

- **.NET** <br>
使用 .NET API 配置文件获取资源提供程序包中每个资源类型的最新且最稳定的版本。 有关详细信息，请参阅[在 Azure Stack Hub 中将 API 版本配置文件与 .NET 配合使用](azure-stack-version-profiles-net.md)。
- **PowerShell**  
使用通过 PowerShell 库提供的 **AzureRM.Bootstrapper** 模块来获取使用 API 版本配置文件所需的 PowerShell cmdlet。 有关信息，请参阅[使用适用于 PowerShell 的 API 版本配置文件](azure-stack-version-profiles-powershell.md)。
- **Azure CLI**  
将环境配置更新为使用 Azure Stack Hub 特定的 API 版本配置文件。 有关信息，请参阅[使用适用于 Azure CLI 的 API 版本配置文件](azure-stack-version-profiles-azurecli2.md)。
- **Go**  
在 Go SDK 中，配置文件结合了不同服务的不同版本的不同资源类型。 配置文件在 profiles/路径下提供，其版本采用 **YYYY-MM-DD** 格式。 有关信息，请参阅[使用适用于 Go 的 API 版本配置文件](azure-stack-version-profiles-go.md)。
- **Ruby**  
用于 Azure Stack Hub 资源管理器的 Ruby SDK 提供了相关工具来帮助构建和管理基础结构。 该 SDK 中的资源提供程序包括了采用 Ruby 语言的计算、虚拟网络和存储提供程序。 有关信息，请参阅[将 API 版本配置文件与 Ruby 配合使用](azure-stack-version-profiles-ruby.md)。
- **Python**  
Python SDK 支持 API 版本配置文件将不同的云平台（例如 Azure Stack Hub 和全球 Azure）用作目标。 使用 API 配置文件为混合云创建解决方案。 有关信息，请参阅[将 API 版本配置文件与 Python 配合使用](azure-stack-version-profiles-python.md)。
- **Node.js**  
用于 Azure Stack Hub 资源管理器的 Node.js SDK 提供了相关工具来帮助构建和管理基础结构。 有关详细信息，请参阅[将 API 版本配置文件与 Node.js 配合使用](azure-stack-version-profile-nodejs.md)。

## <a name="next-steps"></a>后续步骤

- [安装适用于 Azure Stack Hub 的 PowerShell](../operator/azure-stack-powershell-install.md)
- [配置 Azure Stack Hub 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)
- [查看配置文件支持的资源提供程序 API 版本的详细信息](azure-stack-profiles-azure-resource-manager-versions.md)。
