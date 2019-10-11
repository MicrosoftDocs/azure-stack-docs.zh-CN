---
title: Azure Stack 中的更新疑难解答 |Microsoft Docs
description: 作为 Azure Stack 运算符，了解如何解决更新的问题，以便 Azure Stack 可以尽快返回到生产环境。
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
ms.date: 09/23/2019
ms.author: mabrigg
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: d5606e7904fe311a54d792a18e5d4029c709b33c
ms.sourcegitcommit: 0866555e0ed240a65595052899ef1b836dd07fbc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2019
ms.locfileid: "72257745"
---
# <a name="troubleshooting-patch-and-update-issues-for-azure-stack"></a>解决 Azure Stack 的修补程序和更新问题

适用范围：*Azure Stack 集成系统*

你可以使用本文中的指南来解决更新 Azure Stack 时遇到的问题。

## <a name="preparationfailed"></a>PreparationFailed

**适用**：此问题适用于所有支持的版本。

**原因**：尝试安装 Azure Stack 更新时，更新的状态可能会失败，并将状态更改为 `PreparationFailed`。 对于连接到 internet 的系统，这通常表明由于 internet 连接薄弱，更新包无法正确下载。 

**修正**：可以通过单击 "**立即安装**" 来解决此问题。 如果此问题仍然存在，建议使用 "[安装更新](azure-stack-apply-updates.md?#install-updates-and-monitor-progress)" 部分手动上载更新包。

**出现次数**：常见

## <a name="next-steps"></a>后续步骤

- [更新 Azure Stack](azure-stack-updates.md)  
- [Microsoft Azure Stack 帮助和支持](azure-stack-help-and-support-overview.md)
