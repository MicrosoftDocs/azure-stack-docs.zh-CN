---
title: Azure Stack 服务策略 | Microsoft Docs
description: 了解 Azure Stack 服务策略，以及如何使集成系统保持在受支持的状态。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: dbfce7138d76892c7f66e2cf6f33883668e55395
ms.sourcegitcommit: 5703255b4647ff0ebec23658a3f5c25d67f076a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70749941"
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack 服务策略

本文介绍 Azure Stack 集成系统的服务策略，以及必须如何做才能使系统保持在受支持的状态。

## <a name="download-update-packages-for-integrated-systems"></a>下载集成系统的更新包

Microsoft 同时发布每月更新包和修补程序包来解决特定问题。

每月更新包托管在安全的 Azure 终结点中。 可以使用 [Azure Stack 更新下载程序工具](https://aka.ms/azurestackupdatedownload)手动下载它们。 如果缩放单元已连接，更新会以“有可用更新”消息的形式自动显示在管理员门户中。 完整的每月更新包在每次发布时都会详细地进行记录。 有关每次发布的详细信息，可以单击本文[更新包发布频率](#update-package-release-cadence)部分的任何发布。

修补程序更新包托管在同一个安全的 Azure 终结点中。 可以使用每篇相应的修补程序知识库文章（例如，[Azure Stack 修补程序 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114)）中的嵌入式链接下载它们。 Azure Stack 操作员可以按照[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)中的过程下载 .xml、.bin 和 .exe 文件并将其导入，这与操作完整的每月更新包类似。 使用已连接缩放单元的 Azure Stack 操作员会看到修补程序自动出现在管理员门户中，并会看到消息“有可用更新”。

如果在缩放单元未连接的情况下希望获得每个修补程序版本的通知，请订阅每个版本中注明的 [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) 或 [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) 源。

## <a name="update-package-types"></a>更新包类型

集成系统有两种类型的更新包：

- **Microsoft 软件更新**。 Microsoft 会负责 Microsoft 软件更新包的端到端服务生命周期。 这些包可以包括最新的 Windows Server 安全更新、非安全更新和 Azure Stack 功能更新。 可以直接从 Microsoft 下载这些更新包。

- **OEM 硬件供应商提供的更新**。 Azure Stack 硬件合作伙伴负责硬件相关固件和驱动程序更新包的端到端服务生命周期（包括指导）。 此外，对于硬件生命周期主机上的所有软件和硬件，Azure Stack 硬件合作伙伴拥有并维护指导。 OEM 硬件供应商在自己的下载站点上托管这些更新包。

## <a name="update-package-release-cadence"></a>更新包发布频率

Microsoft 预期每月发布软件更新包。 但是，可能一个月内发布多个更新或没有任何更新。 OEM 硬件供应商会根据需要发布更新。

在[管理更新概述](azure-stack-updates.md)文档中，可以了解如何规划和管理更新，以及如何确定当前版本。

有关特定更新（包括其下载方法）的信息，请参阅该更新的发行说明：

- [Azure Stack 1908 更新](azure-stack-release-notes-1908.md)
- [Azure Stack 1907 更新](azure-stack-release-notes-1907.md)
- [Azure Stack 1906 更新](azure-stack-release-notes-1906.md)
- [Azure Stack 1905 更新](azure-stack-release-notes-1905.md)

## <a name="hotfixes"></a>修补程序

适用范围：*Azure Stack 集成系统*

Microsoft 偶尔会提供 Azure Stack 的修补程序（通常是预防性或时效性的程序）来解决具体的问题。  发布的每个修补程序都附带相应的 Microsoft 知识库文章，其中详细描述了问题、原因和解决方法。

可以像下载和安装普通的 Azure Stack 完整更新包一样下载和安装修补程序。 但是，与完整更新不同，修补程序在几分钟内即可完成安装。 我们建议 Azure Stack 操作员在安装修补程序时设置维护时段。 修补程序会更新 Azure Stack 云的版本，使你可以轻松确定是否已应用该修补程序。 对于仍在支持期内的每个 Azure Stack 版本，将单独提供修补程序。 特定迭代的每个修复程序是累积性的，包含该同一版本的以往更新。 可以在相应的知识库文章中详细了解特定修补程序的适用性。 请参阅上一部分中的发行说明链接。

有关当前可用的修补程序的信息，请参阅该更新的发行说明：

- [Azure Stack 1908 修补程序](azure-stack-release-notes-1908.md#hotfixes)
- [Azure Stack 1907 修补程序](azure-stack-release-notes-1907.md#hotfixes)
- [Azure Stack 1906 修补程序](azure-stack-release-notes-1906.md#hotfixes)
- [Azure Stack 1905 修补程序](azure-stack-release-notes-1905.md#hotfixes)

## <a name="keep-your-system-under-support"></a>保持系统受支持

要使 Azure Stack 实例保持在受支持状态，实例必须运行最近发布的更新版本，或者运行上述两个更新版本之一。

修补程序不属于主要更新版本。 如果 Azure Stack 实例超过*两个更新*，则会将其视为不符合。 必须至少更新到支持的最低版本。

例如，如果最新发布的更新版本为 1904，在此之前的两个更新包为版本 1903 和 1902，则 1902 和 1903 仍受支持， 但 1901 不受支持。 即使最近一到两个月没有发布任何版本，此策略也有效。 例如，如果最新版本为 1807，但没有版本 1806，则此前的两个更新包（1805 和 1804）仍受支持。

Microsoft 软件更新包是非累积性的，其先决条件是需要前一个更新包或修补程序。 如果决定延后一个或多个更新，如果想要获取最新版本，请考虑整体运行时。

## <a name="get-support"></a>获取支持

Azure Stack 遵循与 Azure 相同的支持过程。 企业客户可以遵循[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)中所述的过程。 如果你是云服务提供商 (CSP) 的客户，请联系 CSP 获得支持。 有关详细信息，请参阅 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack 中管理更新](azure-stack-updates.md)
