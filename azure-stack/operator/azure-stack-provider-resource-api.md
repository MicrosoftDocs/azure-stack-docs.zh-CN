---
title: Azure Stack 中心提供程序资源使用情况 API
description: 资源使用情况 API 的参考，它检索 Azure Stack 集线器使用情况信息。
author: sethmanheim
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 0dff4901d24f759404b69184506d1219273d90c5
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698107"
---
# <a name="provider-resource-usage-api"></a>提供商资源使用情况 API

术语*提供程序*适用于服务管理员和任何委托的提供程序。 Azure Stack 中心操作员和委派的提供商可以使用提供者使用情况 API 来查看其直接租户的使用情况。 例如，如下图所示，P0 可以调用提供程序 API 来获取 P1 和 P2 上的直接使用信息，P1 可以调用有关 P3 和 P4 的使用情况信息。

![提供者层次结构的概念模型](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>API 调用引用

### <a name="request"></a>请求

请求获取请求的订阅和请求的时间范围的消耗详细信息。 没有请求正文。

此使用情况 API 是一个提供程序 API，因此必须在提供者的订阅中为调用方分配**所有者**、**参与者**或**读取**者角色。

| 方法 | 请求 URI |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value}` |

### <a name="arguments"></a>参数

| 参数 | 说明 |
| --- | --- |
| `armendpoint` |Azure Stack 中心环境的 Azure 资源管理器终结点。 Azure Stack 中心约定是 Azure 资源管理器终结点的名称采用 `https://adminmanagement.{domain-name}`格式。 例如，对于 Azure Stack 开发工具包（ASDK），如果域名为*test-azurestack*，则资源管理器终结点 `https://adminmanagement.local.azurestack.external`。 |
| `subId` |发出调用的用户的订阅 ID。 |
| `reportedStartTime` |查询的开始时间。 `DateTime` 的值应为协调世界时（UTC）和一小时的开头;例如，13:00。 对于每日聚合，请将此值设置为 UTC 午夜。 格式为转义 ISO 8601;例如，`2015-06-16T18%3a53%3a11%2b00%3a00Z`，其中冒号转义为 `%3a`，并转义到 `%2b`，使其能够识别 URI。 |
| `reportedEndTime` |查询的结束时间。 适用于 `reportedStartTime` 的约束也适用于此参数。 `reportedEndTime` 的值不能是将来的值，也不能是当前日期。 如果为，则结果设置为 "处理未完成"。 |
| `aggregationGranularity` |具有两个离散潜在值的可选参数：**每天**和**每小时**。 如值所示，一种是每日粒度返回数据，另一种是每小时的解析。 默认值为 "**每日**" 选项。 |
| `subscriberId` |订阅 ID。 若要获取筛选的数据，需要提供提供程序的直接租户的订阅 ID。 如果未指定订阅 ID 参数，则调用将返回所有提供者的直接租户的使用情况数据。 |
| `api-version` |用于发出此请求的协议的版本。 此值设置为 `2015-06-01-preview`。 |
| `continuationToken` |从上次调用使用情况 API 提供程序检索到的令牌。 当响应大于1000行时，需要使用此令牌。 它作为进度的书签。 如果该标记不存在，则将根据传入的粒度从当天或小时的开头检索数据。 |

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
|`id` |用量聚合的唯一 ID。 |
|`name` |使用聚合的名称。 |
|`type` |资源定义。 |
|`subscriptionId` |Azure Stack 中心用户的订阅标识符。 |
|`usageStartTime`|此使用情况聚合所属的用量存储桶的 UTC 开始时间。|
|`usageEndTime`|此使用情况聚合所属的使用情况存储桶的 UTC 结束时间。 |
|`instanceData` |实例详细信息的键值对（采用新格式）：<br> `resourceUri`：完全限定的资源 ID，其中包括资源组和实例名称。 <br> `location`：运行此服务的区域。 <br> `tags`：用户指定的资源标记。 <br> `additionalInfo`：有关使用的资源的更多详细信息;例如，操作系统版本或映像类型。 |
|`quantity`|此时间范围内发生的资源消耗量。 |
|`meterId` |使用的资源的唯一 ID （也称为 `ResourceID`）。 |

## <a name="retrieve-usage-information"></a>检索使用情况信息

### <a name="powershell"></a>PowerShell

若要生成使用情况数据，应具有正在运行且正在使用系统的资源。例如，活动虚拟机（VM）或包含某些数据的存储帐户。 如果不确定是否有任何资源在 Azure Stack 中心市场上运行，请部署 VM，并验证 VM 监视边栏选项卡以确保其正在运行。 使用以下 PowerShell cmdlet 查看使用情况数据：

1. [为 Azure Stack 集线器安装 PowerShell](azure-stack-powershell-install.md)。
2. [配置 Azure Stack 集线器用户](../user/azure-stack-powershell-configure-user.md)或[Azure Stack 中心操作员](azure-stack-powershell-configure-admin.md)PowerShell 环境。
3. 若要检索使用情况数据，请调用[AzsSubscriberUsage](/powershell/module/azs.commerce.admin/get-azssubscriberusage) PowerShell cmdlet：

   ```powershell
   Get-AzsSubscriberUsage -ReportedStartTime "2017-09-06T00:00:00Z" -ReportedEndTime "2017-09-07T00:00:00Z"
   ```

### <a name="rest-api"></a>REST API

您可以通过调用**Microsoft. Admin**服务来收集已删除订阅的使用情况信息。

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
- [使用情况相关的常见问题](azure-stack-usage-related-faq.md)
