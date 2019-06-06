---
title: 在 Azure Stack 中管理更新概述 | Microsoft Docs
description: 了解 Azure Stack 集成系统的更新管理。
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
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: 5fa84271c02ebc749871116badb3c767812a48ec
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691551"
---
# <a name="manage-updates-in-azure-stack-overview"></a>在 Azure Stack 中管理更新概述

*适用于：Azure Stack 集成系统*

Azure Stack 集成的系统通常发布每个月的 Microsoft 更新包。 有关原始设备制造商 (OEM) 为确保更新通知到达你的组织而采取的具体通知流程，请咨询原始设备制造商。 还可以查看位于“概述” > “发行说明”下的此文档库来了解有关处于有效支持状态的发行版的信息。  

每次发布的 Microsoft 软件更新均打包为单个更新包。 Azure Stack 操作员可以导入、 安装和监视 Azure Stack 管理门户中的更新包的安装进度。

OEM 供应商还会发布更新，例如驱动程序和固件更新。 虽然这些更新作为单独的包由供应商进行传递，一些是导入、 安装和更新包为 Microsoft 从相同的方式管理。

若要保持系统受支持，必须始终将 Azure Stack 更新为特定版本级别。 请务必查看 [Azure Stack 服务策略](azure-stack-servicing-policy.md)。

> [!NOTE]
> 不能将 Azure Stack 更新包应用到 Azure Stack 开发工具包 (ASDK)。 更新包专为集成系统所设计。 有关信息，请参阅[重新部署 ASDK](../asdk/asdk-redeploy.md)。

## <a name="the-update-resource-provider"></a>更新资源提供程序

Azure Stack 包含的更新资源提供程序处理的 Microsoft 软件更新的应用程序。 此提供程序将检查所有物理主机、Service Fabric 应用程序和运行时以及所有基础结构虚拟机及其关联的服务上是否应用了更新。

当更新安装时，由于更新进程以 Azure Stack 中的各种子系统（例如，物理主机和基础结构虚拟机）为目标，因此你可以查看高级状态。

## <a name="plan-for-updates"></a>规划更新

我们强烈建议你向用户通知任何维护操作，并尽可能将正常维护时段安排在非工作时间。 维护操作可能会同时影响租户工作负荷和门户操作。

规划维护时段时，一定要查看特定类型的相应的发行说明中所述发布从 Microsoft 更新包。 除了偶尔修补程序，每个更新包将具有相应的类型，**完整**或**Express**。 完整的更新包更新中的缩放单位物理主机的操作系统，而且需要更大的维护时段。 快速更新包的作用域，而不更新基础物理主机操作系统。

在开始安装此更新之前，请使用以下参数运行 [Test-AzureStack](azure-stack-diagnostic-test.md)，以验证 Azure Stack 的状态并解决发现的所有操作问题，包括所有警告和故障。 另外，请查看活动警报，并解决所有需要采取措施的警报。  

```powershell
Test-AzureStack -Group UpdateReadiness
```

## <a name="using-the-update-tile-to-manage-updates"></a>使用“更新”磁贴管理更新

从管理员门户中管理更新。 Azure Stack 操作员可以使用**更新**磁贴到仪表板中：

- 查看重要信息，如最新版本。
- 安装更新并监视进度。
- 查看以前安装的更新的更新历史记录。
- 查看云的当前 OEM 包版本。

## <a name="determine-the-current-version"></a>确定当前版本

您可以查看 Azure Stack 中的当前版本**更新**磁贴。 若要打开该磁贴：

1. 打开 Azure Stack 管理员门户。
2. 选择“仪表板”。  “更新”磁贴中会列出当前版本。 

    ![默认仪表板上的“更新”磁贴](./media/azure-stack-updates/image1.png)

    例如，在此映像版本是 1.1903.0.35。

## <a name="install-updates-and-monitor-progress"></a>安装更新并监视进度

1. 打开 Azure Stack 管理员门户。
2. 选择“仪表板”。  选择“更新”磁贴。 
3. 选择“立即更新”。 

    ![Azure Stack 更新运行详细信息](media/azure-stack-updates/azure-stack-update-button.png)

4. 可以查看当更新进程循环访问 Azure Stack 中的各个子系统时的概要状态。 示例子系统包括物理主机、Service Fabric、基础结构虚拟机，以及提供管理员用户和用户门户的服务。 在整个更新过程中，更新资源提供程序的其他详细信息报告的更新，如已成功的步骤数，以及正在进行中的数字。

5. 选择**下载完整日志**从更新运行详细信息边栏选项卡中下载完整日志。

    ![Azure Stack 更新运行详细信息](media/azure-stack-updates/update-run-details.png)

6. 完成后，更新资源提供程序提供了**Succeeded**确认以指示是否已完成更新过程和所用的时间。 在此处，可以使用筛选器查看有关所有更新、可用更新或已安装更新的信息。

    ![Azure Stack 更新运行详细信息 - 成功](media/azure-stack-updates/update-success.png)

   如果更新失败，**更新**磁贴的报表**需要关注**。 使用**下载完整日志**选项，若要获取的更新失败的高级状态。 Azure Stack 日志收集有助于简化诊断和故障排除。

## <a name="next-steps"></a>后续步骤

- [Azure Stack 服务策略](azure-stack-servicing-policy.md) 
- [Azure Stack 中的区域管理](azure-stack-region-management.md)
