---
title: 提供者资源使用情况 API | Microsoft Docs
description: 资源使用情况 API 的参考，该 API 用于检索 Azure Stack 使用情况信息
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
ms.date: 06/18/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/25/2018
ms.openlocfilehash: c1333d088cf00b5e909ba5c4ced409bec7538189
ms.sourcegitcommit: c4507a100eadd9073aed0d537d054e394b34f530
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67198486"
---
# <a name="provider-resource-usage-api"></a>提供商资源使用情况 API

“提供者”  一词适用于服务管理员和任何委派的提供者。 Azure Stack 操作员和委派的提供者可使用提供者使用情况 API，查看其直接租户的使用情况。 例如，以下关系图中所示，P0 可以调用提供程序 API，以获取 P1 和 P2 直接使用情况信息和 P1 可以调用 P3 和 P4 的使用情况信息。

![提供者层次结构的概念模型](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>API 调用参考

### <a name="request"></a>请求

请求会获取所请求的订阅在请求的时间范围内的消耗量详细信息。 没有请求正文。

此使用情况 API 是提供程序 API，因此调用方必须分配**所有者**，**参与者**，或**读取器**提供商的订阅中的角色。

| 方法 | 请求 URI |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value}` |

### <a name="arguments"></a>参数

| 参数 | 描述 |
| --- | --- |
| `armendpoint` |Azure Stack 环境的 Azure 资源管理器终结点。 按 Azure Stack 约定，Azure 资源管理器终结点名称的格式为 `https://adminmanagement.{domain-name}`。 例如，对于开发工具包，如果域名为 *local.azurestack.external*，则资源管理器终结点是 `https://adminmanagement.local.azurestack.external`。 |
| `subId` |进行调用的用户的订阅 ID。 |
| `reportedStartTime` |查询的开始时间。 值为`DateTime`应为以协调世界时 (UTC) 和开头的小时; 例如，13:00。 对于每日聚合，请将此值设置为 UTC 午夜。 转义格式 ISO 8601;例如， `2015-06-16T18%3a53%3a11%2b00%3a00Z`，其中冒号会转义为`%3a`和加号会转义为`%2b`以便它适合 URI 的。 |
| `reportedEndTime` |查询的结束时间。 将应用于的约束`reportedStartTime`也适用于此参数。 值为`reportedEndTime`不能为在将来或当前日期。 如果是，结果会设为“处理未完成”。 |
| `aggregationGranularity` |可选参数，它有两个离散潜在值：**每日**并**每小时**。 如同以上两个值所暗示，一个会每日返回数据，另一个则会每小时返回数据。 **每日**选项是默认值。 |
| `subscriberId` |订阅 ID。 若要获取筛选的数据，需要提供者直接租户的订阅 ID。 如果未指定订阅 ID 参数，调用会返回所有提供者直接租户的使用情况数据。 |
| `api-version` |用于发出此请求的协议版本。 此值设置为 `2015-06-01-preview`。 |
| `continuationToken` |从上次调用使用情况 API 提供者取回的标记。 响应大于 1,000 行时，需要此标记。 它可作为进度的书签。 若无此标记，则会从一天或小时开始时的时间检索数据，取决于所传入的粒度。 |

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

| 参数 | 描述 |
| --- | --- |
|`id` |使用情况聚合的唯一 ID。 |
|`name` |使用情况聚合的名称。 |
|`type` |资源定义。 |
|`subscriptionId` |Azure Stack 用户的订阅标识符。 |
|`usageStartTime`|此使用情况聚合所属的使用情况存储桶 UTC 开始时间。|
|`usageEndTime`|此使用情况聚合所属的使用情况存储桶 UTC 结束时间。 |
|`instanceData` |实例详细信息的键/值对（采用新格式）：<br> `resourceUri`：完全限定的资源 ID，其中包括资源组和实例名称。 <br> `location`：运行此服务的区域。 <br> `tags`：用户所指定的资源标记。 <br> `additionalInfo`：所消耗资源的; 有关更多详细信息例如，OS 版本或映像类型。 |
|`quantity`|此时间范围内发生的资源消耗数量。 |
|`meterId` |已耗用的资源的唯一 ID (也称为`ResourceID`)。 |

## <a name="retrieve-usage-information"></a>检索使用情况信息

### <a name="powershell"></a>PowerShell

若要生成使用情况数据，应具有正在运行且主动使用系统; 的资源例如，活动的虚拟机或包含某些数据的存储帐户。 如果你不确定是否具有任何运行在 Azure Stack marketplace 中的资源、 将虚拟机 (VM) 部署和验证 VM 监视边栏选项卡，请确保它正在运行。 使用以下 PowerShell cmdlet 来查看使用情况数据：

1. [安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。
2. [配置 Azure Stack 用户](../user/azure-stack-powershell-configure-user.md)或[Azure Stack 操作员](azure-stack-powershell-configure-admin.md)PowerShell 环境。
3. 若要检索使用情况数据，请使用 [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) PowerShell cmdlet：

   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

### <a name="rest-api"></a>REST API

可以通过调用 Microsoft.Commerce.Admin 服务来收集已删除订阅的使用信息。

#### <a name="return-all-tenant-usage-for-deleted-for-active-users"></a>删除活动用户返回所有租户使用的情况

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version=2015-06-01-preview` |

#### <a name="return-usage-for-deleted-or-active-tenant"></a>返回已删除或处于活动状态的租户的使用情况

| 方法 | 请求 URI |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={subscriber-id}&api-version=2015-06-01-preview` |

## <a name="next-steps"></a>后续步骤

- [租户资源使用情况 API 参考](azure-stack-tenant-resource-usage-api.md)
- [有关使用情况的常见问题解答](azure-stack-usage-related-faq.md)
