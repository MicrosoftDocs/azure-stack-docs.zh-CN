---
title: Azure Stack Hub 提供程序资源使用情况 API
description: 资源使用情况 API（用于检索 Azure Stack Hub 使用情况信息）的参考。
author: sethmanheim
ms.topic: article
ms.date: 11/09/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 2d8b1eddb295a07a120858e1fa5845b51858ab9a
ms.sourcegitcommit: 980be7813e6f39fb59926174a5d3e0d392b04293
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414021"
---
# <a name="provider-resource-usage-api"></a>提供商资源使用情况 API

“提供者”  一词适用于服务管理员和任何委派的提供者。 Azure Stack Hub 操作员和委派的提供者可使用提供者使用情况 API，查看其直接租户的使用情况。 例如，如下图所示，P0 可以调用提供者 API，以获取 P1 和 P2 的直接使用情况信息；而 P1 可以通过调用来获取 P3 和 P4 的使用情况信息。

![提供者层次结构的概念模型](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>API 调用参考

### <a name="request"></a>请求

请求会获取所请求的订阅在请求的时间范围内的消耗量详细信息。 没有请求正文。

此使用情况 API 是提供者 API，因此必须将提供者订阅中的“所有者”、“参与者”或“读者”角色分配给调用方。   

| 方法 | 请求 URI |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value}` |

### <a name="arguments"></a>参数

| 参数 | 说明 |
| --- | --- |
| `armendpoint` |Azure Stack Hub 环境的 Azure 资源管理器终结点。 按 Azure Stack Hub 约定，Azure 资源管理器终结点名称的格式为 `https://adminmanagement.{domain-name}`。 例如，对于 Azure Stack 开发工具包 (ASDK)，如果域名为“local.azurestack.external”  ，则资源管理器终结点为 `https://adminmanagement.local.azurestack.external`。 |
| `subId` |进行调用的用户的订阅 ID。 |
| `reportedStartTime` |查询的开始时间。 `DateTime` 的值应为以协调世界时 (UTC) 和小时开始时的时间呈现，例如 13:00。 对于每日聚合，请将此值设置为 UTC 午夜。 格式是转义的 ISO 8601，例如 `2015-06-16T18%3a53%3a11%2b00%3a00Z`，其中冒号转义为 `%3a`，而加号转义为 `%2b`，使其符合 URI 规范。 |
| `reportedEndTime` |查询的结束时间。 适用于 `reportedStartTime` 的约束也适用于此参数。 `reportedEndTime` 的值不得为未来或当前的日期。 如果是，结果会设为“处理未完成”。 |
| `aggregationGranularity` |这是可选参数，它有两个截然不同的可能值： **daily** 和 **hourly** 。 如同以上两个值所暗示，一个会每日返回数据，另一个则会每小时返回数据。 默认值为 **daily** 选项。 |
| `subscriberId` |订阅 ID。 若要获取筛选的数据，需要提供者直接租户的订阅 ID。 如果未指定订阅 ID 参数，调用会返回所有提供者直接租户的使用情况数据。 |
| `api-version` |用于发出此请求的协议版本。 此值设置为 `2015-06-01-preview`。 |
| `continuationToken` |从上次调用使用情况 API 提供者取回的标记。 响应大于 1,000 行时，需要此标记， 可作为进度的书签。 如果此标记不存在，则根据传入的粒度从一天或一小时的开始检索数据。 |

### <a name="response"></a>响应

```http
GET
/subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&subscriberId=sub1.1&api-version=1.0
```

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce.Admin/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce.Admin/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

. . .
```

### <a name="response-details"></a>响应详细信息

| 参数 | 说明 |
| --- | --- |
|`id` |使用情况聚合的唯一 ID。 |
|`name` |使用情况聚合的名称。 |
|`type` |资源定义。 |
|`subscriptionId` |Azure Stack Hub 用户的订阅标识符。 |
|`usageStartTime`|此使用情况聚合所属的使用情况存储桶 UTC 开始时间。|
|`usageEndTime`|此使用情况聚合所属的使用情况存储桶 UTC 结束时间。 |
|`instanceData` |实例详细信息的键/值对（采用新格式）：<br> `resourceUri`：完全限定的资源 ID，其中包括资源组和实例名称。 <br> `location`：运行此服务的区域。 <br> `tags`：用户所指定的资源标记。 <br> `additionalInfo`：更多关于所消耗资源的详细信息（例如 OS 版本或映像类型）。 |
|`quantity`|此时间范围内发生的资源消耗数量。 |
|`meterId` |所消耗资源的唯一 ID（也称 `ResourceID`）。 |

## <a name="retrieve-usage-information"></a>检索使用情况信息

### <a name="powershell"></a>PowerShell

若要生成使用情况数据，你应该有正在运行且主动使用系统的资源，例如，活动的虚拟机 (VM) 或包含某些数据的存储帐户。 如果不确定你是否有任何资源在 Azure Stack Hub 市场中运行，请部署一个 VM，并验证“VM 监视”边栏选项卡以确保它正在运行。 使用以下 PowerShell cmdlet 来查看使用情况数据：

1. [安装适用于 Azure Stack Hub 的 PowerShell](azure-stack-powershell-install.md)。
2. [配置 Azure Stack Hub 用户](../user/azure-stack-powershell-configure-user.md)或 [Azure Stack Hub 操作员](azure-stack-powershell-configure-admin.md) PowerShell 环境。
3. 若要检索使用情况数据，请调用 [Get-AzsSubscriberUsage](/powershell/module/azs.commerce.admin/get-azssubscriberusage) PowerShell cmdlet：

   ```powershell
   Get-AzsSubscriberUsage -ReportedStartTime "2017-09-06T00:00:00Z" -ReportedEndTime "2017-09-07T00:00:00Z"
   ```

### <a name="rest-api"></a>REST API

可以通过调用 **Microsoft.Commerce.Admin** 服务来收集已删除订阅的使用情况信息。

#### <a name="return-all-tenant-usage-for-deleted-for-active-users"></a>为活动用户返回已删除的所有租户使用情况

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version=2015-06-01-preview` |

#### <a name="return-usage-for-deleted-or-active-tenant"></a>返回已删除或活动租户的使用情况

| 方法 | 请求 URI |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={subscriber-id}&api-version=2015-06-01-preview` |

## <a name="next-steps"></a>后续步骤

- [租户资源使用情况 API 参考](azure-stack-tenant-resource-usage-api.md)
- [有关使用情况的常见问题解答](azure-stack-usage-related-faq.md)
