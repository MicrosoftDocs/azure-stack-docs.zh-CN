---
title: 租户资源使用情况 Api 参考
titleSuffix: Azure Stack
description: 资源使用 Api 参考，用于检索 Azure Stack 集线器使用情况信息。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 5e6fd1042edcf59955a6e766d2ffb215c49c2949
ms.sourcegitcommit: c4368652f0dd68c432aa1dabddbabf161a4a6399
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2020
ms.locfileid: "75914717"
---
# <a name="tenant-resource-usage-api-reference"></a>租户资源使用情况 API 参考

租户可以使用租户 Api 来查看租户自己的资源使用情况数据。 这些 Api 与 Azure 使用情况 Api 一致。

可以使用 Windows PowerShell cmdlet [get-usageaggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates)来获取使用情况数据，就像在 Azure 中一样。

## <a name="api-call"></a>API 调用

### <a name="request"></a>请求

请求获取请求的订阅和请求的时间范围的消耗详细信息。 没有请求正文。

| **方法** | **请求 URI** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="parameters"></a>参数

| **Parameter** | **说明** |
| --- | --- |
| Armendpoint |Azure Stack 中心环境的 Azure 资源管理器终结点。 Azure Stack 集线器约定是 Azure 资源管理器终结点的名称采用 `https://management.{domain-name}`格式。 例如，对于开发工具包，域名为 test-azurestack，则 `https://management.local.azurestack.external`资源管理器终结点。 |
| subId |正在进行调用的用户的订阅 ID。 只能使用此 API 查询单个订阅的使用情况。 提供程序可以使用提供程序资源使用情况 API 来查询所有租户的使用情况。 |
| reportedStartTime |查询的开始时间。 *DateTime*的值应采用 UTC 格式，在该小时的开头应为;例如，13:00。 对于每日聚合，请将此值设置为 UTC 午夜。 格式为转义 ISO 8601;例如， **2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z**，其中冒号转义为% 3a，并将其转义为% 2b，使其能够识别 URI。 |
| reportedEndTime |查询的结束时间。 适用于**reportedStartTime**的约束也适用于此参数。 **ReportedEndTime**的值不能为将来的值。 |
| aggregationGranularity |具有两个离散潜在值的可选参数：**每天**和**每小时**。 如值所示，一种是每日粒度返回数据，另一种是每小时的解析。 默认值为 "**每日**" 选项。 |
| api-version |用于发出此请求的协议的版本。 必须使用**2015-06-01-preview**。 |
| ContinuationToken |从上次调用使用情况 API 提供程序检索到的令牌。 当响应大于1000行时，需要使用此令牌。 它充当进度书签。 如果不存在，则基于传入的粒度从当天或小时的开头检索数据。 |

### <a name="response"></a>响应

```html
GET
/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0
```

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

...
```

### <a name="response-details"></a>响应详细信息

| **Parameter** | **说明** |
| --- | --- |
| id |用量聚合的唯一 ID。 |
| name |使用聚合的名称。 |
| type |资源定义。 |
| subscriptionId |Azure 用户的订阅标识符。 |
| usageStartTime |此使用情况聚合所属的用量存储桶的 UTC 开始时间。 |
| usageEndTime |此使用情况聚合所属的使用情况存储桶的 UTC 结束时间。 |
| instanceData |实例详细信息的键值对（采用新格式）：<br>  *resourceUri*：完全限定的资源 ID，包括资源组和实例名称。 <br>  *location*：运行此服务的区域。 <br>  *标记*：用户指定的资源标记。 <br>  *additionalInfo*：有关已使用的资源的更多详细信息。 例如，OS 版本或映像类型。 |
| quantity |此时间范围内发生的资源消耗量。 |
| meterId |已使用的资源的唯一 ID （也称为**ResourceID**）。 |

## <a name="next-steps"></a>后续步骤

- [提供程序资源使用情况 API](azure-stack-provider-resource-api.md)
- [使用情况相关的常见问题](azure-stack-usage-related-faq.md)
