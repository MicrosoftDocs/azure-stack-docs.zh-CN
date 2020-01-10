---
title: Azure Stack 集线器诊断日志收集概述 |Microsoft Docs
description: 介绍 Azure Stack 中心帮助 + 支持（包括按需和自动日志收集）中的诊断日志收集。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 197d39feb91edc8c93d747471f30aff8f56b03ac
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75811662"
---
# <a name="overview-of-azure-stack-hub-diagnostic-log-collection"></a>Azure Stack 集线器诊断日志收集概述 

*适用于： Azure Stack 集线器集成系统*

Azure Stack 中心是一大集合组件，它们协同工作，彼此交互。 所有这些组件都生成其自己的唯一日志。 这会使诊断问题成为一项挑战性的任务，尤其是对于来自多个 Azure Stack 集线器组件的多个交互的错误。 为了应对这一挑战，我们设计了诊断日志收集体验。 

在1907之前，使用[test-azurestack](azure-stack-diagnostic-test.md)中的诊断体验来验证系统运行状况，并使用[特权终结点（PEP）](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)收集日志以便进行故障排除。 

从1907版本开始，"**帮助和支持**" 页使用**诊断日志集合**添加了更简单的体验。 
**诊断日志收集**是一项不断投入的投资，用来改善 Azure Stack 中心运营商在故障排除过程中的体验。 通过这些改进，操作员可以通过 Microsoft 客户支持服务（CSS）快速收集和共享诊断日志。 日志可以存储在 Azure 中的 blob 容器中，可以根据需要对其进行自定义。    
   
**诊断日志收集**通过两种不同的方式工作：

- **自动收集**：如果启用（推荐），则日志收集由特定的运行状况警报自动触发，并存储在 Azure 存储帐户中
- **立即收集日志**：这是一个按需选项，可在其中选择从过去七天的1-4 小时滑动窗口收集日志

![诊断日志收集选项的屏幕截图](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

**诊断日志收集**具有简单的用户界面，不需要 PowerShell。 即使基础结构服务出现故障，也可以可靠地收集日志。
如果策略允许使用 CSS 共享诊断日志，则从1907版本开始，建议使用**诊断日志收集**方法。 如果 "帮助和支持" 中的**诊断日志收集**不可用，只应使用[PEP](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)收集日志。

## <a name="automatic-diagnostic-log-collection"></a>自动诊断日志收集 

当[特定的运行状况警报](azure-stack-configure-automatic-diagnostic-log-collection.md#automatic-diagnostic-log-collection-alerts)处于活动状态时，自动诊断日志收集会启动从 Azure Stack 中心到 Azure 中的存储 blob 的诊断日志，并将其与 CSS 共享诊断日志所需的时间大大减少。 诊断日志仅在发出警报时收集。  

有关自动日志收集的详细信息，请参阅[配置自动 Azure Stack 集线器诊断日志收集](azure-stack-configure-automatic-diagnostic-log-collection.md)。

## <a name="on-demand-diagnostic-log-collection"></a>按需诊断日志收集

对于按需收集，当 Azure Stack 中心操作员手动触发集合时，诊断日志将从 Azure Stack 集线器上传到 Azure 中的存储 blob。
CSS 将为 CSS 拥有的存储 blob 提供共享访问签名（SAS） URL。 Azure Stack 中心操作员可以单击 "**立即收集日志**"，然后输入 SAS URL。 然后，诊断日志将直接上传到 CSS blob，而无需中间共享。 

有关按需收集日志的详细信息，请参阅[立即收集 Azure Stack 集线器诊断日志](azure-stack-configure-on-demand-diagnostic-log-collection.md)。

## <a name="bandwidth-considerations"></a>带宽注意事项

诊断日志集合的平均大小取决于它是按需运行还是自动运行。 自动日志收集的平均大小约为 2 GB，而按需日志收集大小取决于收集的小时数。 

下表列出了对 Azure 进行有限连接或按流量计费的环境的注意事项。

| 网络连接 | 影响 |
|--------------------|--------|
| 低带宽/高延迟连接 | 日志上传需要较长的时间才能完成 | 
| 共享连接 | 上传还可能会影响共享网络连接的其他应用程序/用户 |
| 按流量计费的连接 | 你的 ISP 可能会额外收取额外的网络使用量 |

有关详细信息，请参阅[自动 Azure Stack 中心日志收集的最佳实践](azure-stack-best-practices-automatic-diagnostic-log-collection.md)。

## <a name="see-also"></a>另请参阅

[Azure Stack 中心日志和客户数据处理](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[使用共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[自动 Azure Stack 中心日志收集的最佳实践](azure-stack-best-practices-automatic-diagnostic-log-collection.md)
