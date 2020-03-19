---
title: Azure Stack 中心服务策略
titleSuffix: Azure Stack Hub
description: 了解 Azure Stack 中心服务策略，以及如何使集成系统处于受支持的状态。
author: sethmanheim
ms.topic: article
ms.date: 02/07/2020
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 9b1459fbb5e484e3fc0588e8c9717f8e09b6e309
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2020
ms.locfileid: "79512395"
---
# <a name="azure-stack-hub-servicing-policy"></a>Azure Stack 中心服务策略

本文介绍 Azure Stack 集线器集成系统的服务策略，以及必须执行哪些操作才能使系统处于受支持状态。

## <a name="download-update-packages-for-integrated-systems"></a>下载集成系统的更新包

Microsoft 同时发布每月更新包和修补程序包来解决特定问题。

每月更新包托管在一个安全的 Azure 终结点中。 你可以使用[Azure Stack 中心更新下载程序工具](https://aka.ms/azurestackupdatedownload)手动下载它们。 如果缩放单位已连接，则更新会在管理员门户中自动显示为 "**可用更新**"。 每个版本中都有完整的月更新包记录。 有关每个版本的详细信息，可以单击本文[更新包版本步调](#update-package-release-cadence)部分中的任何版本。

修补程序更新包托管在同一安全 Azure 终结点中。 你可以使用每个相关修补程序知识库文章中的嵌入链接下载它们;例如， [Azure Stack 中心修补程序 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114)。 与完整的每月更新包一样，Azure Stack 中心操作员可以下载 .xml、bin 和 .exe 文件，并使用在[Azure Stack 中心应用更新中](azure-stack-apply-updates.md)的过程来导入它们。 具有连接的缩放单位的 Azure Stack 中心操作员会看到，这些修补程序会自动在管理员门户中显示，并**提供消息更新**。

如果缩放单位未连接，并且希望收到有关每个修补程序版本的通知，请订阅每个版本中所述的[RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)或[ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)馈送。

## <a name="update-package-types"></a>更新包类型

集成系统有两种类型的更新包：

- **Microsoft 软件更新**。 Microsoft 负责 Microsoft 软件更新包的端到端服务生命周期。 这些包可以包括最新的 Windows Server 安全更新、非安全更新和 Azure Stack 集线器功能更新。 你可以直接从 Microsoft 下载这些更新包。

- **OEM 硬件供应商提供的更新**。 Azure Stack 集线器硬件合作伙伴负责与硬件相关的固件和驱动程序更新包的端到端服务生命周期（包括指南）。 此外，Azure Stack 集线器硬件合作伙伴拥有和维护硬件生命周期主机上所有软件和硬件的指导。 OEM 硬件供应商在其自己的下载站点上托管这些更新包。

## <a name="update-package-release-cadence"></a>更新包发布节奏

Microsoft 期望每月发布一次软件更新包。 但是，在一个月中可以有多个或没有更新版本。 OEM 硬件供应商需要根据需要发布更新。

查找有关如何计划和管理更新的文档，以及如何在[管理更新概述](azure-stack-updates.md)中确定当前版本的文档。

有关特定更新（包括如何下载）的信息，请参阅该更新的发行说明：

- [Azure Stack 中心2002更新](/azure-stack/operator/release-notes?view=azs-2002)
- [Azure Stack 中心1910更新](/azure-stack/operator/release-notes?view=azs-1910)
- [Azure Stack 中心1908更新](/azure-stack/operator/release-notes?view=azs-1908)
- [Azure Stack 中心1907更新](/azure-stack/operator/release-notes?view=azs-1907)

## <a name="hotfixes"></a>修补程序

偶尔，Microsoft 为 Azure Stack 中心提供修补程序，用于解决通常是预防或区分时间的特定问题。 每个修补程序都附带了相应的 Microsoft 知识库文章，其中详细介绍了问题、原因和解决方法。

下载和安装修补程序的方式与 Azure Stack 中心的常规完整更新包一样。 但是，与完全更新不同，修补程序可以在几分钟内安装。 建议在安装修补程序时 Azure Stack 中心操作员设置维护时段。 修补程序更新 Azure Stack 中心云的版本，以便你可以轻松确定是否已应用此修补程序。 为仍在支持的 Azure Stack 集线器的每个版本提供单独的修补程序。 针对特定迭代的每个修复都是累积性的，并包括此相同版本的以前的更新。 可以在相应的知识库文章中详细了解特定修补程序的适用性。 请参阅上一节中的发行说明链接。

有关当前可用的修补程序的信息，请参阅该更新的发行说明：

- [Azure Stack 集线器2002修补程序](/azure-stack/operator/release-notes?view=azs-2002#hotfixes)
- [Azure Stack 集线器1910修补程序](/azure-stack/operator/release-notes?view=azs-1910#hotfixes-1)
- [Azure Stack 集线器1908修补程序](/azure-stack/operator/release-notes?view=azs-1908#hotfixes-2)
- [Azure Stack 集线器1907修补程序](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-3)

## <a name="keep-your-system-under-support"></a>使系统保持受支持

::: moniker range="azs-2002"

> [!IMPORTANT]  
> 在 Azure Stack 中心2002更新中，Microsoft 暂时扩展了我们的 Azure Stack 中心支持策略声明。 我们正在与世界各地的客户合作，他们对 COVID-19 作出回应，他们可能会作出有关其 Azure Stack 中心系统的重要决策，以及如何更新和管理这些系统，因此确保其数据中心业务运营继续正常运行。 为支持我们的客户，Microsoft 提供了临时支持策略更改扩展，以包括三个以前的更新版本。 因此，将支持新发布的2002更新以及三个以前的更新版本（例如1910、1908和1907）。

::: moniker-end

要使 Azure Stack 集线器实例保持在受支持的状态，实例必须运行最近发布的更新版本，或者运行上述两个更新版本中的任何一个。

修补程序不被视为主要更新版本。 如果 Azure Stack 集线器实例落后*于两*个以上的更新，则将其视为不符合。 必须至少更新到支持的最低版本。

例如，如果最新可用的更新版本为1904，并且之前的两个更新包的版本为1903和1902，则1902和1903仍支持。 但1901不支持。 如果有一个或两个月没有发布，则该策略为 true。 例如，如果当前版本为1807，并且没有1806版本，则之前的两个1805和1804更新包将保持支持。

Microsoft 软件更新包是非累积性的，需要以前的更新包或修补程序作为必备组件。 如果你决定推迟一个或多个更新，请考虑整个运行时，如果你想要获取最新版本。

## <a name="get-support"></a>获取支持

Azure Stack 中心遵循与 Azure 相同的支持过程。 企业客户可以遵循[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)中所述的过程。 如果你是云解决方案提供商（CSP）的客户，请联系你的 CSP 以获得支持。 有关详细信息，请参阅 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

若要帮助解决更新问题，请参阅[解决 Azure Stack 集线器修补程序和更新问题时的最佳做法](azure-stack-updates-troubleshoot.md)。

## <a name="next-steps"></a>后续步骤

- [管理 Azure Stack 集线器中的更新](azure-stack-updates.md)
- [Azure Stack 中心修补程序和更新问题进行故障排除的最佳实践](azure-stack-updates-troubleshoot.md)
