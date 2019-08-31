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
ms.date: 08/01/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 1022ab056157ea1a9bc925d3992a99bd0b395a35
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188080"
---
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

Microsoft 偶尔会提供 Azure Stack 的修补程序（通常是预防性或时效性的程序）来解决具体的问题。  发布的每个修补程序都附带相应的 Microsoft 知识库文章，其中详细描述了问题、原因和解决方法。

适用范围：*Azure Stack 集成系统*

本文介绍 Azure Stack 集成系统的服务策略, 你必须执行哪些操作来使系统处于受支持的状态, 以及如何获取支持。

## <a name="keep-your-system-under-support"></a>保持系统受支持

要使 Azure Stack 实例保持在受支持状态, 实例必须运行最近发布的更新版本, 或者运行上述两个更新版本之一。

修补程序不属于主要更新版本。 如果 Azure Stack 实例超过*两个更新*, 则会将其视为不符合。 必须至少更新到支持的最低版本。

例如，如果最新发布的更新版本为 1904，在此之前的两个更新包为版本 1903 和 1902，则 1902 和 1903 仍受支持， 但 1901 不受支持。 即使最近一到两个月没有发布任何版本，此策略也有效。 例如，如果最新版本为 1807，但没有版本 1806，则此前的两个更新包（1805 和 1804）仍受支持。

Microsoft 软件更新包是非累积性的，其先决条件是需要前一个更新包或修补程序。 如果决定延后一个或多个更新，如果想要获取最新版本，请考虑整体运行时。

## <a name="get-support"></a>获取支持

Azure Stack 遵循与 Azure 相同的支持过程。 企业客户可以遵循[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)中所述的过程。 如果你是云服务提供商 (CSP) 的客户，请联系 CSP 获得支持。 有关详细信息，请参阅 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤

- [在 Azure Stack 中管理更新](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
