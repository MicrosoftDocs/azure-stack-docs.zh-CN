---
title: 租户资源使用情况 API 参考
titleSuffix: Azure Stack
description: 资源使用情况 API（用于检索 Azure Stack Hub 使用情况信息）的参考资料。
author: sethmanheim
ms.topic: article
ms.date: 12/15/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 1fdc86f9077f6c59fa1424d7118d7c0ec6499d4f
ms.sourcegitcommit: a53ea4a28e715c80a99fa89e9d364bc4556558de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97576949"
---
# <a name="tenant-resource-usage-api-reference"></a>租户资源使用情况 API 参考

租户可以使用租户 API 来查看租户自己的资源用量数据。 这些 API 与 Azure 使用情况 API 一致。

可以使用 Windows PowerShell cmdlet [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) 获取使用情况数据，就像在 Azure 中一样。

## <a name="api-call"></a>API 调用

### <a name="request"></a>请求

请求会获取所请求的订阅在请求的时间范围内的消耗量详细信息。 没有请求正文。

| **方法** | **请求 URI** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="parameters"></a>parameters

| **参数** | **说明** |
| --- | --- |
| Armendpoint |Azure Stack Hub 环境的 Azure 资源管理器终结点。 按 Azure Stack Hub 约定，Azure 资源管理器终结点名称的格式为 `https://management.{domain-name}`。 例如，对于开发工具包，如果域名为 local.azurestack.external，则资源管理器终结点是 `https://management.local.azurestack.external`。 |
| subId |正在发出调用的用户的订阅 ID。 只能使用此 API 查询单个订阅的用量。 提供程序可以使用提供程序资源用量 API 查询所有租户的用量。 |
| reportedStartTime |查询的开始时间。 *DateTime* 值应以 UTC 格式及小时开始时的时间呈现，例如 13:00。 对于每日聚合，请将此值设置为 UTC 午夜。 格式是转义的 ISO 8601，例如 **2015-06-16T18%3a53%3a11%2b00%3a00Z**，其中冒号转义为 %3a 而加号转义为 %2b，使其符合 URI 规范。 |
| reportedEndTime |查询的结束时间。 适用于 **reportedStartTime** 的约束也适用于此参数。 **reportedEndTime** 的值不能是将来的时间。 |
| aggregationGranularity |这是可选参数，它有两个截然不同的可能值：**daily** 和 **hourly**。 如同以上两个值所暗示，一个会每日返回数据，另一个则会每小时返回数据。 默认值为 **daily** 选项。 |
| api-version |用于发出此请求的协议版本。 必须使用 **2015-06-01-preview**。 |
| continuationToken |从上次调用使用情况 API 提供者取回的标记。 响应大于 1,000 行时，需要此标记， 它可作为进度的书签。 若无此标记，则会从一天或小时开始时的时间检索数据，取决于所传入的粒度。 |

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

| **参数** | **说明** |
| --- | --- |
| id |使用情况聚合的唯一 ID。 |
| name |使用情况聚合的名称。 |
| type |资源定义。 |
| subscriptionId |Azure 用户的订阅标识符。 |
| usageStartTime |此使用情况聚合所属的使用情况存储桶 UTC 开始时间。 |
| usageEndTime |此使用情况聚合所属的使用情况存储桶 UTC 结束时间。 |
| instanceData |实例详细信息的键/值对（采用新格式）：<br>  *resourceUri*：完全限定的资源 ID，其中包括资源组和实例名称。 <br>  *location*：运行此服务的区域。 <br>  *tags*：用户指定的资源标记。 <br>  *additionalInfo*：有关已用资源的更多详细信息。 例如，OS 版本或映像类型。 |
| quantity |此时间范围内发生的资源消耗数量。 |
| meterId |所消耗资源的唯一 ID（也称为 **ResourceID**）。 |

## <a name="next-steps"></a>后续步骤

- [提供程序资源使用情况 API](azure-stack-provider-resource-api.md)
- [有关使用情况的常见问题解答](azure-stack-usage-related-faq.md)
