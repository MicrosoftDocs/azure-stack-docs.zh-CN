---
title: 自动 Azure Stack 中心日志收集的最佳实践 |Microsoft Docs
description: Azure Stack 中心帮助 + 支持中自动收集日志的最佳做法
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
ms.openlocfilehash: 7baa7440fec1c29ee43c5d3f80282bd6a58a3242
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883025"
---
# <a name="best-practices-for-automatic-azure-stack-hub-log-collection"></a>自动 Azure Stack 中心日志收集的最佳实践 

本主题介绍有关管理 Azure Stack 集线器的自动诊断日志收集的最佳实践。 

## <a name="collecting-logs-from-multiple-azure-stack-hub-systems"></a>从多个 Azure Stack 集线器系统收集日志

为要从中收集日志的每个 Azure Stack 集线器缩放单位设置一个 blob 容器。 有关如何配置 blob 容器的详细信息，请参阅[配置自动 Azure Stack 集线器诊断日志收集](azure-stack-configure-automatic-diagnostic-log-collection.md)。 最佳做法是，只保存单个 blob 容器内相同 Azure Stack 集线器缩放单位的诊断日志。 

## <a name="retention-policy"></a>保留策略

创建 Azure Blob 存储[生命周期管理规则](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts)以管理日志保留策略。 建议保留30天的诊断日志。 若要在 Azure 存储中创建生命周期管理规则，请登录到 Azure 门户，单击 "**存储帐户**"，单击 "blob" 容器，然后在 " **blob 服务**" 下单击 "**生命周期管理**"。

![显示 Azure 门户中的生命周期管理的屏幕截图](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)


## <a name="sas-token-expiration"></a>SAS 令牌过期

将 SAS URL 的到期时间设置为两年。 如果你续订了存储帐户密钥，请确保重新生成 SAS URL。 应根据最佳做法管理 SAS 令牌。 有关详细信息，请参阅[使用 SAS 的最佳做法](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas)。


## <a name="bandwidth-consumption"></a>带宽消耗

诊断日志集合的平均大小根据日志收集是按需还是自动进行变化。 

对于按需日志收集，日志集合的大小取决于正在收集的小时数。 可以从过去七天选择任何1-4 小时的滑动窗口。 

启用自动诊断日志收集后，服务将监视关键警报。 引发关键警报并持续大约30分钟后，服务将收集并上传适当的日志。 此日志集合大小平均约为 2 GB。 如果出现修补程序和更新失败的情况，自动日志收集只有在引发严重警报时才开始，并持续30分钟。 建议遵循[有关监视修补程序和更新的指南](azure-stack-updates.md)。
警报监视、日志收集和上载对用户是透明的。 



在正常的系统中，根本不会收集日志。 在不正常的系统中，日志收集可能在一天中运行两次或三次，但通常只运行一次。 最糟糕的情况是，在最坏的情况下，可能会在一天内最多运行10次。  

下表可以帮助对 Azure 进行有限或按流量计费的连接，请考虑启用自动日志收集的影响。

| 网络连接 | 影响 |
|--------------------|--------|
| 低带宽/高延迟连接 | 日志上传需要较长的时间才能完成 | 
| 共享连接 | 上传还可能会影响共享网络连接的其他应用程序/用户 |
| 按流量计费的连接 | 你的 ISP 可能会额外收取额外的网络使用量 |


## <a name="managing-costs"></a>管理成本

Azure [blob 存储费用](https://azure.microsoft.com/pricing/details/storage/blobs/)取决于每月保存的数据量以及数据冗余等其他因素。 如果没有现有的存储帐户，可以登录到 Azure 门户，单击 "**存储帐户**"，然后按照步骤[创建 AZURE BLOB 容器 SAS URL](azure-stack-configure-automatic-diagnostic-log-collection.md)。

最佳做法是创建 Azure Blob 存储[生命周期管理策略](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts)，以最大程度地减少正在进行的存储成本。 有关如何设置存储帐户的详细信息，请参阅[配置自动 Azure Stack 集线器诊断日志收集](azure-stack-configure-automatic-diagnostic-log-collection.md)

## <a name="see-also"></a>另请参阅

[配置自动 Azure Stack 中心日志收集](azure-stack-best-practices-automatic-diagnostic-log-collection.md)

