---
title: 管理 Azure Stack 中的 API 版本配置文件 | Microsoft Docs
description: 了解 Azure Stack 中的 API 版本配置文件。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 9d0597d4e2687c6563c365470be57c1603bbe616
ms.sourcegitcommit: 72d45bb935db0db172d4d7c37d8e48e79e25af64
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68376746"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>管理 Azure Stack 中的 API 版本配置文件

适用对象：*Azure Stack 集成系统和 Azure Stack 开发工具包*

API 配置文件指定 Azure 资源提供程序和 Azure REST 终结点的 API 版本。 可以使用 API 配置文件以不同的语言创建自定义客户端。 每个客户端使用一个 API 配置文件来联系 Azure Stack 的正确资源提供程序和 API 版本。

可以创建一个应用来与 Azure 资源提供程序配合运行，而无需明确区分与 Azure Stack 兼容的每个资源提供程序 API 的版本。 只需将应用程序与配置文件对齐, SDK 就会恢复到正确的 API 版本。

本主题帮助读者了解：

 - Azure Stack 的 API 配置文件。
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
    每两年发布一次，此版本注重于跨多个云的一致性和稳定性。 此配置文件以实现最佳 Azure Stack 兼容性为目标。
    - **yyyy-mm-dd-profile** <br>
    平衡最佳稳定性和最新功能。

## <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Azure API 配置文件和 Azure Stack 兼容性

最新的 Azure API 配置文件与 Azure Stack 不兼容。 使用以下命名约定来确定要用于 Azure Stack 解决方案的配置文件:

**最新**  
此配置文件具有在全球 Azure 中找到的最新 API 版本, 不适用于 Azure Stack。 **Latest** 包含的重大更改最多。 此配置文件不考虑稳定性以及与其他云的兼容性。 如果尝试使用最新的 API 版本，则应当使用 **Latest** 配置文件。

**Yyyy-mm-dd-hybrid**  
此配置文件在每年的三月份和九月份发布。 它具有最佳的稳定性以及与各种云的兼容性。 **Yyyy-mm-dd-hybrid** 是以全球 Azure 和 Azure Stack 作为应用目标而设计的。 此配置文件中列出的 Azure API 版本将与 Azure Stack 中列出的版本相同。 使用此配置文件开发混合云解决方案的代码。

**yyyy-mm-dd-profile**  
此配置文件在六月份和九月份针对全球 Azure 发布。 它不能与 Azure Stack 一起使用, 并且通常会有很多重大更改。 尽管它会平衡最佳稳定性和最新功能, 但最新与此配置**文件之间的**区别是**最新**始终包含最新的 api 版本, 而不考虑何时发布 API。 例如，如果明天为“计算 API”创建一个新的 API 版本，则该 API 版本将列在**最新**配置文件中，但不会列在 **yyyy-mm-dd-profile** 中，因为此配置文件已经存在。 **yyyy-mm-dd-profile** 涵盖了六月份或九月份之前发布的最新版本。

## <a name="azure-resource-manager-api-profiles"></a>Azure 资源管理器 API 配置文件

Azure Stack 不使用在全球 Azure 中找到的最新版本的 API 版本。 在创建解决方案时，必须为每个 Azure 资源提供程序查找与 Azure Stack 兼容的 API 版本。

你可以使用 API 配置文件，而无需研究 Azure Stack 支持的每个资源提供程序和特定版本。 配置文件会指定一组资源提供程序和 API 版本。 SDK 或者使用 SDK 生成的工具将还原到配置文件中指定的目标 `api-version`。 使用 API 配置文件, 你可以指定应用于整个模板的配置文件版本。 在运行时, Azure 资源管理器会选择正确的资源版本。

API 配置文件可与使用 Azure 资源管理器的工具（例如 PowerShell、Azure CLI、SDK 中提供的代码，以及 Microsoft Visual Studio）配合运行。 工具和 Sdk 可以使用配置文件来读取在生成应用时要包括的模块和库的版本。

例如，如果通过 PowerShell 使用支持 **api-version** 2016-03-30 的 **Microsoft.Storage** 资源提供程序创建存储帐户，并使用 **api-version** 为 2015-12-01 的 **Microsoft.Compute** 资源提供程序创建 VM，则必须查看哪个 PowerShell 模块支持将 2016-03-30 用于存储，以及哪个模块支持将 2015-02-01 用于计算，然后安装这两个模块。 可以改用配置文件。 使用 cmdlet `Install-Profile <profilename>`，然后 PowerShell 会加载正确的模块版本。

同样, 使用 Python SDK 生成基于 Python 的应用时, 可以指定该配置文件。 SDK 会为您在脚本中指定的资源提供程序加载正确的模块。

开发人员可以专注于编写解决方案。 无需研究哪些 API 版本、资源提供程序和云可一起运行，而可以使用配置文件，确定代码可跨所有支持该配置文件的云运行。

## <a name="api-profile-code-samples"></a>API 配置文件代码示例

可以借助代码示例，通过配置文件将采用偏好语言的解决方案与 Azure Stack 相集成。 目前可以找到以下语言的指导和示例：

- **.NET** <br>
使用 .NET API 配置文件获取资源提供程序包中每种资源类型的最新、最稳定的版本。 有关详细信息，请参阅[在 Azure Stack 中将 API 版本配置文件与 .NET 配合使用](azure-stack-version-profiles-net.md)。
- **PowerShell**  
使用通过 PowerShell 库提供的**AzureRM**模块来获取使用 API 版本配置文件所需的 PowerShell cmdlet。 有关信息，请参阅[使用适用于 PowerShell 的 API 版本配置文件](azure-stack-version-profiles-powershell.md)。
- **Azure CLI**  
将环境配置更新为使用 Azure Stack 特定的 API 版本配置文件。 有关信息，请参阅[使用适用于 Azure CLI 的 API 版本配置文件](azure-stack-version-profiles-azurecli2.md)。
- **Go**  
在 Go SDK 中，配置文件结合了不同服务的不同版本的不同资源类型。 配置文件在配置文件/路径下提供, 其版本采用**yyyy-mm-dd**格式。 有关信息，请参阅[使用适用于 Go 的 API 版本配置文件](azure-stack-version-profiles-go.md)。
- **Ruby**  
用于 Azure Stack 资源管理器的 Ruby SDK 提供了相关工具来帮助构建和管理基础结构。 该 SDK 中的资源提供程序包括了采用 Ruby 语言的计算、虚拟网络和存储提供程序。 有关信息, 请参阅[在 Ruby 中使用 API 版本配置文件](azure-stack-version-profiles-ruby.md)。
- **Python**  
Python SDK 支持 API 版本配置文件将不同的云平台（例如 Azure Stack 和 Azure 公有云）用作目标。 使用 API 配置文件创建适用于混合云的解决方案。 有关信息, 请参阅[将 API 版本配置文件与 Python 配合使用](azure-stack-version-profiles-python.md)。

## <a name="next-steps"></a>后续步骤

* [安装适用于 Azure Stack 的 PowerShell](../operator/azure-stack-powershell-install.md)
* [配置 Azure Stack 用户的 PowerShell 环境](azure-stack-powershell-configure-user.md)
* [查看配置文件支持的资源提供程序 API 版本的详细信息](azure-stack-profiles-azure-resource-manager-versions.md)。
