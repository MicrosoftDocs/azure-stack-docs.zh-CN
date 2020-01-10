---
title: Azure Stack 中心 Azure Monitor 支持的指标 |Microsoft Docs
description: 了解 Azure Stack 中心 Azure Monitor 支持的指标。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: df3644be6384bd264dc78ec80f8119c9cd2ff630
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75819737"
---
# <a name="supported-metrics-for-azure-monitor-on-azure-stack-hub"></a>Azure Stack 中心 Azure Monitor 支持的指标

*适用于： Azure Stack 集线器集成系统*

从 Azure Stack 集线器上的 Azure monitor 检索指标的方式与在全球 Azure 中相同。 可以在门户中创建你的度量值、 从 REST API 获取它们或使用 PowerShell 或 CLI 查询它们。

下表列出了 Azure Stack 中心 Azure Monitor 的指标管道可用的指标。 若要查询和访问这些指标，请使用 API 配置文件的**2018-01-01** api 版本。 有关 API 配置文件和 Azure Stack 集线器的详细信息，请参阅[在 Azure Stack 中心管理 api 版本配置文件](azure-stack-version-profiles.md)。

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

| 度量值 | 指标显示名称 | 单位 | 聚合类型 | Description | 维度 |
|----------------|---------------------|---------|------------------|-----------------------------------------------------------------------------------------------|---------------|
| CPU 百分比 | CPU 百分比 | 百分比 | 平均值 | VM 当前正在使用的已分配计算单元的百分比。 | 无维度 |

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

| 度量值 | 指标显示名称 | 单位 | 聚合类型 | Description | 维度 |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| UsedCapacity | 已用容量 | 字节 | 平均值 | 帐户使用的容量。 | 无维度 |
| 事务 | 事务 | 计数 | 总计 | 向存储服务或指定的 API 操作发出的请求数。 此数字包括成功和失败的请求，还请求生成错误的请求。 将 ResponseType 维度用于不同类型的响应。 | ResponseType、GeoType、ApiName |
| 入口 | 入口 | 字节 | 总计 | 流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储的入口，还包括在 Azure 中的入口。 | GeoType、ApiName |
| 流出量 | 流出量 | 字节 | 总计 | 流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储的出口，还包括 Azure 中的出口。 因此，此数字不反映可计费的出口。 | GeoType、ApiName |
| SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值 | 由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。 | GeoType、ApiName |
| SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值 | 向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。 | GeoType、ApiName |
| 可用性 | 可用性 | 百分比 | 平均值 | 存储服务或指定的 API 操作的可用性百分比。 通过获取 TotalBillableRequests 值并将其除以适用的请求数来计算可用性，包括产生意外错误的请求。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。 | GeoType、ApiName |

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

| 度量值 | 指标显示名称 | 单位 | 聚合类型 | Description | 维度 |
|--------|---------------------|------|------------------|-------------|------------|
| BlobCapacity | Blob 容量 | 字节 | 总计 | 存储帐户的 Blob 服务使用的存储量（以字节为单位）。 | /BlobType |
| BlobCount | Blob 计数 | 计数 | 总计 | 存储帐户的 Blob 服务中的 blob 数。 | /BlobType |
| ContainerCount | Blob 容器计数 | 计数 | 平均值 | 存储帐户的 Blob 服务中的容器数。 | 无维度 |
| 事务 | 事务 | 计数 | 总计 | 向存储服务或指定的 API 操作发出的请求数。 此数字包括成功和失败的请求，还请求生成错误的请求。 将 ResponseType 维度用于不同类型的响应。 | ResponseType、GeoType、ApiName |
| 入口 | 入口 | 字节 | 总计 | 流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储的入口，还包括在 Azure 中的入口。 | GeoType、ApiName |
| 流出量 | 流出量 | 字节 | 总计 | 流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储的出口，还包括 Azure 中的出口。 因此，此数字不反映可计费的出口。 | GeoType、ApiName |
| SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值 | 由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。 | GeoType、ApiName |
| SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值 | 向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。 | GeoType、ApiName |
| 可用性 | 可用性 | 百分比 | 平均值 | 存储服务或指定的 API 操作的可用性百分比。 通过获取 TotalBillableRequests 值并将其除以适用的请求数来计算可用性，包括产生意外错误的请求。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。 | GeoType、ApiName |

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

| 度量值 | 指标显示名称 | 单位 | 聚合类型 | Description | 维度 |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| TableCapacity | 表容量 | 字节 | 平均值 | 存储帐户的表服务使用的存储量（以字节为单位）。 | 无维度 |
| TableCount | 表计数 | 计数 | 平均值 | 存储帐户的表服务中的表数。 | 无维度 |
| TableEntityCount | 表实体计数 | 计数 | 平均值 | 存储帐户的表服务中的表实体数。 | 无维度 |
| 事务 | 事务 | 计数 | 总计 | 向存储服务或指定的 API 操作发出的请求数。 此数字包括成功和失败的请求，还请求生成错误的请求。 将 ResponseType 维度用于不同类型的响应。 | ResponseType、GeoType、ApiName |
| 入口 | 入口 | 字节 | 总计 | 流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储的入口，还包括在 Azure 中的入口。 | GeoType、ApiName |
| 流出量 | 流出量 | 字节 | 总计 | 流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储的出口，还包括 Azure 中的出口。 因此，此数字不反映可计费的出口。 | GeoType、ApiName |
| SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值 | 由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。 | GeoType、ApiName |
| SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值 | 向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。 | GeoType、ApiName |
| 可用性 | 可用性 | 百分比 | 平均值 | 存储服务或指定的 API 操作的可用性百分比。 通过获取 TotalBillableRequests 值并将其除以适用的请求数来计算可用性，包括产生意外错误的请求。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。 | GeoType、ApiName |

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

| 度量值 | 指标显示名称 | 单位 | 聚合类型 | Description | 维度 |
|----------------------|------------------------|--------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------|
| QueueCapacity | 队列容量 | 字节 | 平均值 | 存储帐户的队列服务使用的存储量（以字节为单位）。 | 无维度 |
| QueueCount | 队列计数 | 计数 | 平均值 | 存储帐户的队列服务中的队列数。 | 无维度 |
| QueueMessageCount | 队列消息计数 | 计数 | 平均值 | 存储帐户的队列服务中队列消息的大致数目。 | 无维度 |
| 事务 | 事务 | 计数 | 总计 | 向存储服务或指定的 API 操作发出的请求数。 此数字包括成功和失败的请求，还请求生成错误的请求。 将 ResponseType 维度用于不同类型的响应。 | ResponseType、GeoType、ApiName |
| 入口 | 入口 | 字节 | 总计 | 流入的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储的入口，还包括在 Azure 中的入口。 | GeoType、ApiName |
| 流出量 | 流出量 | 字节 | 总计 | 流出的数据量（以字节为单位）。 此数字包括从外部客户端到 Azure 存储的出口，还包括 Azure 中的出口。 因此，此数字不反映可计费的出口。 | GeoType、ApiName |
| SuccessServerLatency | 成功服务器延迟 | 毫秒 | 平均值 | 由 Azure 存储用于处理成功请求的平均延迟（以毫秒为单位）。 此值不包括 AverageE2ELatency 中指定的网络延迟。 | GeoType、ApiName |
| SuccessE2ELatency | 成功 E2E 延迟 | 毫秒 | 平均值 | 向存储服务或指定的 API 操作发出的成功请求的平均端到端延迟（以毫秒为单位）。 此值包括在 Azure 存储中读取请求、发送响应和接收响应确认所需的处理时间。 | GeoType、ApiName |
| 可用性 | 可用性 | 百分比 | 平均值 | 存储服务或指定的 API 操作的可用性百分比。 通过获取 TotalBillableRequests 值并将其除以适用的请求数来计算可用性，包括产生意外错误的请求。 所有意外错误都会导致存储服务或指定的 API 操作的可用性下降。 | GeoType、ApiName |

## <a name="next-steps"></a>后续步骤

详细了解[Azure Stack 集线器上的 Azure monitor](azure-stack-metrics-azure-data.md)。
