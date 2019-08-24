---
title: 将原始设备制造商 (OEM) 更新应用到 Azure Stack |Microsoft Docs
description: 了解如何将原始设备制造商 (OEM) 更新应用到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2019
ms.author: mabrigg
ms.lastreviewed: 08/15/2019
ms.reviewer: ppacent
ms.openlocfilehash: b8031afd05ba03086cfa748614b58d9b0c5248e9
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70010161"
---
# <a name="azure-stack-servicing-policy"></a>Azure Stack 服务策略

适用范围：*Azure Stack 集成系统*

本文介绍 Azure Stack 集成系统的服务策略, 你必须执行哪些操作来使系统处于受支持的状态, 以及如何获取支持。

## <a name="keep-your-system-under-support"></a>保持系统受支持

若要继续获得支持, 你必须将更新保存在当前 Azure Stack 上。

要使 Azure Stack 实例保持在受支持状态, 实例必须运行最近发布的更新版本, 或者运行上述两个更新版本之一。

修补程序不属于主要更新版本。 如果 Azure Stack 实例超过*两个更新*, 则会将其视为不符合。 必须至少更新到支持的最低版本。

例如，如果最新发布的更新版本为 1904，在此之前的两个更新包为版本 1903 和 1902，则 1902 和 1903 仍受支持， 但 1901 不受支持。 即使最近一到两个月没有发布任何版本，此策略也有效。 例如，如果最新版本为 1807，但没有版本 1806，则此前的两个更新包（1805 和 1804）仍受支持。

Microsoft 软件更新包是非累积性的，其先决条件是需要前一个更新包或修补程序。 如果决定延后一个或多个更新，如果想要获取最新版本，请考虑整体运行时。

## <a name="get-support"></a>获取支持

Azure Stack 遵循与 Azure 相同的支持过程。 企业客户可以遵循[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)中所述的过程。 如果你是云服务提供商 (CSP) 的客户，请联系 CSP 获得支持。 有关详细信息，请参阅 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

# <a name="next-steps"></a>后续步骤

[在 Azure Stack 中管理更新](azure-stack-updates.md)
