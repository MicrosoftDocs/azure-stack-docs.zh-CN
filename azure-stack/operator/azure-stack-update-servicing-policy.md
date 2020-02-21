---
title: 将原始设备制造商（OEM）更新应用到 Azure Stack 中心
description: 了解如何将原始设备制造商（OEM）更新应用到 Azure Stack 中心。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: d75813bf5383d7fa231261057d891a2ab7aa87e2
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77508900"
---
# <a name="azure-stack-hub-servicing-policy"></a>Azure Stack 中心服务策略

本文介绍 Azure Stack 集线器集成系统的服务策略，你必须执行哪些操作来使系统处于受支持的状态，以及如何获取支持。

## <a name="keep-your-system-under-support"></a>使系统保持受支持

若要继续获得支持，你必须将更新保存在 Azure Stack 中心最新。

要使 Azure Stack 中心系统保持支持状态，实例必须运行最近发布的更新版本，或者运行上述两个更新版本中的任何一个。

修补程序不被视为主要更新版本。 如果 Azure Stack 集线器系统已超过两个以上的*更新*，则将其视为不符合。 必须至少更新到支持的最低版本。

例如，如果最新可用的更新版本为1904，并且之前的两个更新包的版本为1903和1902，则1902和1903仍支持。 但1901不支持。 如果有一个或两个月没有发布，则该策略为 true。 例如，如果当前版本为1807，并且没有1806版本，则之前的两个1805和1804更新包将保持支持。

Microsoft 软件更新包是非累积性的，需要以前的更新包或修补程序作为必备组件。 如果你决定推迟一个或多个更新，请考虑整个运行时，如果你想要获取最新版本。

## <a name="get-support"></a>获取支持

Azure Stack 中心遵循与 Azure 相同的支持过程。 企业客户可以遵循[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)中所述的过程。 如果你是云解决方案提供商（CSP）的客户，请联系你的 CSP 以获得支持。 有关详细信息，请参阅 [Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。

## <a name="next-steps"></a>后续步骤

[管理 Azure Stack 集线器中的更新](azure-stack-updates.md)
