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
ms.openlocfilehash: 76c6c21c2a728004d2a33c04a02b905ec674b972
ms.sourcegitcommit: d967cf8cae320fa09f1e97eeb888e3db5b6e7972
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301222"
---
# <a name="troubleshooting-patch-and-update-issues-for-azure-stack"></a>解决 Azure Stack 的修补程序和更新问题

适用范围：*Azure Stack 集成系统*

你可以使用本文中的指南来解决更新 Azure Stack 时遇到的问题。

## <a name="preparationfailed"></a>PreparationFailed

**适用**：此问题适用于所有支持的版本。

**原因**：尝试安装 Azure Stack 更新时，更新的状态可能会失败，并将状态更改为`PreparationFailed`。 这是因为更新资源提供程序 (URP) 无法正确将文件从存储容器传输到内部基础结构共享进行处理。

**修正**：从1901版（1.1901.0.95）开始，你可以通过单击 "立即**更新**" （不**继续**）来解决此问题。 然后，URP 会清理上次尝试更新时下载的文件，并重新开始下载。 如果此问题仍然存在，建议使用 "[安装更新](azure-stack-apply-updates.md?#install-updates-and-monitor-progress)" 部分手动上载更新包。

**出现次数**：通用

## <a name="next-steps"></a>后续步骤

- [更新 Azure Stack](azure-stack-updates.md)  
- [Microsoft Azure Stack 帮助和支持](azure-stack-help-and-support-overview.md)
