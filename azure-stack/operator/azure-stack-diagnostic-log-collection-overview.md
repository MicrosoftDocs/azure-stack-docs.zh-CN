---
title: Azure Stack 诊断日志收集概述 |Microsoft Docs
description: 介绍 Azure Stack 帮助 + 支持 (包括按需和自动日志收集) 中的诊断日志收集。
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
ms.date: 07/25/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 17f68039382453312114dff8737072d1515a583d
ms.sourcegitcommit: b752f4e6733d9ebe56dbd171a14528dcb9a693fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2019
ms.locfileid: "68522003"
---
# <a name="overview-of-azure-stack-diagnostic-log-collection"></a>Azure Stack 诊断日志收集概述 

适用对象：*Azure Stack 集成系统*

Azure Stack 是一个大型集合，其中的组件可以一起工作并互相交互。 所有这些组件都生成其自己的唯一日志。 这样一来，问题可能就难以诊断，尤其是在错误来自多个交互的 Azure Stack 组件的情况下。 为了应对这一挑战, 我们设计了诊断日志收集体验。 

在1907之前, 使用[test-azurestack](azure-stack-diagnostic-test.md)中的诊断体验来验证系统运行状况, 并使用[特权终结点 (PEP)](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep)收集日志以便进行故障排除。 

从1907版本开始, "**帮助和支持**" 页使用**诊断日志集合**添加了更简单的体验。 
**诊断日志收集**是一项不断投入的投资, 用来改善 Azure Stack 操作员在故障排除过程中的体验。 通过这些改进, 操作员可以通过 Microsoft 客户支持服务 (CSS) 快速收集和共享诊断日志。 日志可以存储在 Azure 中的 blob 容器中, 可以根据需要对其进行自定义。    
   
**诊断日志收集**可以通过两种不同的方式收集诊断日志:

- **自动收集**:如果启用 (推荐), 则特定运行状况警报会自动触发日志收集, 并将其存储在 Azure 存储帐户中
- **立即收集日志**:这是一个按需选项, 可在其中选择从过去7天的1-4 小时滑动窗口收集日志

![诊断日志收集选项的屏幕截图](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

**诊断日志收集**具有简单的用户界面, 不需要 PowerShell。 即使基础结构服务出现故障, 也可以可靠地收集日志。
如果策略允许使用 CSS 共享诊断日志, 则从1907版本开始, 建议使用**诊断日志收集**方法。 如果 "帮助和支持" 中的**诊断日志收集**不可用, 只应使用[PEP](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep)收集日志。

## <a name="automatic-diagnostic-log-collection"></a>自动诊断日志收集 

自动诊断日志收集在引发某些关键警报时, 会主动将诊断日志从 Azure Stack 上传到 Azure 中的存储 blob, 这大大减少了与 CSS 共享诊断日志所需的时间。

有关自动日志收集的详细信息, 请参阅[配置自动 Azure Stack 诊断日志收集](azure-stack-configure-automatic-diagnostic-log-collection.md)。

## <a name="on-demand-diagnostic-log-collection"></a>按需诊断日志收集

使用按需收集时, 当 Azure Stack 操作员手动触发集合时, 诊断日志将从 Azure Stack 上载到 Azure 中的存储 blob。
CSS 将为 CSS 拥有的存储 blob 提供共享访问签名 (SAS) URL。 Azure Stack 操作员可以单击 "**立即收集日志**" 并输入 SAS URL。 然后, 诊断日志将直接上传到 CSS blob, 而无需中间共享。 

有关按需收集日志的详细信息, 请参阅[立即收集 Azure Stack 诊断日志](azure-stack-configure-on-demand-diagnostic-log-collection.md)。

## <a name="bandwidth-considerations"></a>带宽注意事项

诊断日志集合的平均大小取决于它是按需运行还是自动运行。 自动日志收集的平均大小约为 2 GB, 而按需日志收集大小取决于收集的小时数。 

下表列出了对 Azure 进行有限连接或按流量计费的环境的注意事项。

| 网络连接 | 影响 |
|--------------------|--------|
| 低带宽/高延迟连接 | 日志上传需要较长的时间才能完成 | 
| 共享连接 | 上传还可能会影响共享网络连接的其他应用程序/用户 |
| 计量连接 | 你的 ISP 可能会额外收取额外的网络使用量 |

有关详细信息, 请参阅[自动 Azure Stack 日志收集的最佳实践](azure-stack-best-practices-automatic-diagnostic-log-collection.md)。

## <a name="see-also"></a>请参阅

[Azure Stack 日志和客户数据处理](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[使用共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[自动 Azure Stack 日志收集的最佳实践](azure-stack-best-practices-automatic-diagnostic-log-collection.md)
