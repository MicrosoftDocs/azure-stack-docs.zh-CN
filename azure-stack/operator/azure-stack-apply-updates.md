---
title: 安装 Azure Stack 集线器更新 |Microsoft Docs
description: 了解如何安装 Azure Stack 集线器更新。
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
ms.date: 09/10/2019
ms.author: mabrigg
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 337f5bfccc7deccf7214d452546c1e0ba3296555
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882889"
---
# <a name="install-azure-stack-hub-updates"></a>安装 Azure Stack 中心更新

你可以使用 Azure Stack 集线器中的**更新**边栏选项卡安装更新包。 本文将指导你完成更新、监视和解决更新过程的步骤。 使用 "更新" 边栏选项卡可以查看更新信息、安装更新、监视更新进度、查看更新历史记录，以及查看当前 Azure Stack 中心和 OEM 包版本。

你可以从管理员门户管理更新，并使用仪表板的 "**更新**" 部分执行以下操作：

- 查看重要信息，例如当前版本。
- 安装更新和监视进度。
- 查看以前安装的更新的更新历史记录。
- 查看云当前的 OEM 包版本。

## <a name="determine-the-current-version"></a>确定当前版本

您可以在 "**更新**" 边栏选项卡中查看 Azure Stack 集线器的当前版本。 若要打开：

1.  打开 Azure Stack 中心管理员门户。

2.  选择 "**仪表板**"。 在 "**更新**" 边栏选项卡中列出了最新版本。

    ![在默认仪表板上更新磁贴](./media/azure-stack-update-apply/image1.png)

    例如，在此图中，版本为1.1903.0.35。

## <a name="install-updates-and-monitor-progress"></a>安装更新和监视进度

1. 打开 Azure Stack 中心管理员门户。

2. 选择 "**仪表板**"。 选择“更新”。

3. 选择要安装的可用更新。 如果没有标记为**可用**的更新，则需要[准备更新包](azure-stack-update-prepare-package.md)

4. 选择 "**立即更新**"。

    ![Azure Stack 中心更新运行详细信息](./media/azure-stack-update-apply/image2.png)

5. 你可以查看高级状态，因为更新过程会循环访问 Azure Stack 集线器中的各个子系统。 示例子系统包括物理主机、Service Fabric、基础结构虚拟机和提供管理员和用户门户的服务。 在整个更新过程中，更新资源提供程序会报告有关更新的其他详细信息，例如已成功完成的步骤数和正在进行的编号。

6. 从 "更新运行详细信息" 边栏选项卡中选择**下载摘要**，下载完整日志。

    如果在监视更新时遇到问题，可以使用[特权终结点](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint)监视 Azure Stack 中心更新运行的进度。 如果 Azure Stack 集线器门户不可用，还可以使用特权终结点从上一个成功的步骤中恢复失败的更新运行。 有关说明，请参阅[使用 PowerShell 监视 Azure Stack 集线器中的更新](azure-stack-update-monitor.md)。

    ![Azure Stack 中心更新运行详细信息](./media/azure-stack-update-apply/image3.png)

7. 完成后，更新资源提供程序会提供**成功**的确认消息，以表明更新过程已完成，需要多长时间。 在此处，你可以查看有关所有更新、可用更新或使用筛选器安装的更新的信息。

    ![azure stack-更新-应用](./media/azure-stack-update-apply/image4.png)

    如果更新失败，**更新**边栏选项卡会报告**需要注意**。 使用 "**下载完整日志**" 选项可获得更新失败的高级别状态。 Azure Stack 中心日志收集有助于诊断和故障排除。

## <a name="review-update-history"></a>查看更新历史记录

1. 打开管理员门户。

2. 选择 "**仪表板**"。 选择“更新”。

3. 选择 "**更新历史记录**"。

    ![Azure Stack 中心更新历史记录](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>后续步骤

-   [在 Azure Stack 中心管理更新概述](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)  
-   [Azure Stack 中心服务策略](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy)  
