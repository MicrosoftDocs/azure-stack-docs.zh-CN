---
title: Azure Stack Windows Server 相关常见问题解答-MDC |Microsoft Docs
description: 在模块化数据中心 (MDC) 操作时，请阅读适用于 Windows Server 的 Azure Stack Marketplace 常见问题解答列表。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 64ccefb0a6644d2cd613a39d8beaaf1f82ee9fc0
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910782"
---
# <a name="windows-server-in-azure-stack-marketplace-faq---modular-data-center-mdc"></a>Azure Stack marketplace 中的 Windows Server 常见问题-模块化数据中心 (MDC) 

本文解答了有关 [Azure Stack 中心 marketplace](../../operator/azure-stack-marketplace.md)中的 Windows Server 映像的常见问题。

## <a name="faqs"></a>常见问题解答

**Azure Stack 上的 Windows Server 市场映像有哪些许可选项？**

Azure Stack 中心耐用和 MDC 的用户有权免费使用 Windows Server 作为来宾操作系统。

Microsoft 通过 Azure Stack marketplace 提供了两个版本的 Windows Server 映像。 在任何给定的 Azure Stack 环境中，只可使用此映像的一个版本。

- 即 **用即付**：这些映像不应在 Azure Stack 集线器耐用或 MDC 上使用。

- **自带许可证 (BYOL)**：可在 Azure Stack Hub 耐用和 MDC 上使用这些映像。

**使用 Windows Server 的其他 Vm （如 SQL Server）呢？**

Windows Server 软件许可证仅适用于 Windows 操作系统，而不适用于 SQL 之类的分层产品，要求你自带许可证。

**如何更新到较新的 Windows 映像？**

首先，请确定是否有任何 Azure 资源管理器模板引用了特定的版本。 如果有，请更新这些模板，或保留旧的映像版本。 最好是使用 **version: latest**。

接下来，如果任何虚拟机规模集引用特定版本，则应考虑是否会在以后对其进行缩放，并决定是否保留旧版本。 如果上述两个条件都不适用，请先在市场中删除旧映像，然后下载新映像。 如果原始映像是使用“市场管理”下载的，请使用“市场管理”将其删除。 然后下载新版本。

**如果我下载了错误的版本来提供用户怎么办？**

请先通过“市场管理”删除错误的版本。 等待删除操作完成（请查看完成通知，而不要查看“市场管理”边栏选项卡）。 然后下载正确的版本。

如果下载了两个版本的映像，则最终客户在市场库中只能看到最新版本。

**如何激活 Windows Server 虚拟机？**

若要在 Azure Stack 上激活 Windows Server 虚拟机，必须满足以下条件：

- Windows Server 2012 R2 和 Windows Server 2016 必须使用[自动虚拟机激活](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))。 Azure Stack 不支持密钥管理服务 (KMS) 和其他激活服务。

**如何验证虚拟机是否已激活？**

在提升的命令提示符下运行以下命令：

```shell
slmgr /dlv
```

如果它已正确激活，则会看到这种情况清楚，并显示在 "slmgr.vbs 输出" 中。 请不要依赖于显示画面中的水印，因为它们可能不是最新的，或者显示你的虚拟机后面的其他虚拟机的状态。

**我的 VM 未设置为使用 AVMA，如何解决此问题？**

在提升的命令提示符下运行以下命令：

```shell
slmgr /ipk <AVMA key>
```

请参阅[自动虚拟机激活](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))一文，获取映像使用的密钥。

**我自行创建了 Windows Server 映像，如何确保它们使用 AVMA？**

建议你在 `slmgr /ipk` 运行该命令之前使用适当的密钥来执行该命令 `sysprep` 。 或者，将 AVMA 密钥包含在任何 Unattend.exe 安装文件中。

**我正在尝试使用自己在 Azure 上创建的 Windows Server 2016 映像，但它无法激活或者正在使用 KMS 激活。**

运行 `slmgr /ipk` 命令。 Azure 映像可能无法正确回退到 AVMA，但如果它们可以访问 Azure KMS 系统，则会激活。 请确保将这些 VM 设置为使用 AVMA。

**我已执行上述所有步骤，但虚拟机仍无法激活。**

请联系 Microsoft 支持部门，验证是否安装了正确的 BIOS 标记。

**对于早期版本的 Windows Server，如何激活？**

在 2012 R2 之前的 Windows Server 版本中不支持[自动虚拟机激活](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn303421(v=ws.11))。 必须使用 MAK 密钥手动激活 Vm。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下文章：

- [Azure Stack 市场概述](../../operator/azure-stack-marketplace.md)
- [将市场项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item-tca.md)
