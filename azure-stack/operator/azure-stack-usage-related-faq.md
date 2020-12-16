---
title: 用量 API 常见问题解答
titleSuffix: Azure Stack Hub
description: 有关 Azure Stack Hub 用量的常见问题列表，包括计量、与 Azure 用量 API 的比较、使用时间与报告时间，以及错误代码。
services: azure-stack
documentationcenter: ''
author: sethmanheim
ms.topic: article
ms.date: 12/15/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 02/26/2019
ms.openlocfilehash: a4f9744139199f999475f24a3910e1929b0a8b9b
ms.sourcegitcommit: a53ea4a28e715c80a99fa89e9d364bc4556558de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97576966"
---
# <a name="frequently-asked-questions-about-azure-stack-hub-usage"></a>有关 Azure Stack Hub 用量的常见问题解答

本文解答有关 Azure Stack Hub 用量和 Azure Stack Hub 用量 API 的一些常见问题。

## <a name="what-meter-ids-can-i-see"></a>可以查看哪些计量 ID？

系统针对以下资源提供程序报告用量：

### <a name="network"></a>网络
  
**计量 ID**：F271A8A388C44D93956A063E1D2FA80B  
**计量名称**：静态 IP 地址用量  
**单元**：IP 地址  
**注释**：已用 IP 地址计数。 如果以每日粒度调用用量 API，计量器会返回 IP 地址乘以小时数。  
  
**计量 ID**：9E2739BA86744796B465F64674B822BA  
**计量名称**：动态 IP 地址用量  
**单元**：IP 地址  
**注释**：已用 IP 地址计数。 如果以每日粒度调用用量 API，计量器会返回 IP 地址乘以小时数。  
  
### <a name="storage"></a>存储
  
**计量 ID**：B4438D5D-453B-4EE1-B42A-DC72E377F1E4  
**计量名称**：TableCapacity  
**单元**：GB\*小时  
**注释**：表使用的总容量。  
  
**计量 ID**：B5C15376-6C94-4FDD-B655-1A69D138ACA3  
**计量名称**：PageBlobCapacity  
**单元**：GB\*小时  
**注释**：页 Blob 使用的总容量。  
  
**计量 ID**：B03C6AE7-B080-4BFA-84A3-22C800F315C6  
**计量名称**：QueueCapacity  
**单元**：GB\*小时  
**注释**：队列使用的总容量。  
  
**计量 ID**：09F8879E-87E9-4305-A572-4B7BE209F857  
**计量名称**：BlockBlobCapacity  
**单元**：GB\*小时  
**注释**：块 Blob 使用的总容量。  
  
**计量 ID**：B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90  
**计量名称**：TableTransactions  
**单元**：请求计数以万为单位  
**注释**：表服务请求数（以 10,000 计）。  
  
**计量 ID**：50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D  
**计量名称**：TableDataTransIn  
**单元**：传入数据 (GB)  
**注释**：表服务传入数据 (GB)。  
  
**计量 ID**：1B8C1DEC-EE42-414B-AA36-6229CF199370  
**计量名称**：TableDataTransOut  
**单元**：传出数据 (GB)  
**注释**：表服务传出数据 (GB)。
  
**计量 ID**：43DAF82B-4618-444A-B994-40C23F7CD438  
**计量名称**：BlobTransactions  
**单元**：请求计数以万为单位  
**注释**：Blob 服务请求数（以 10,000 计）。  
  
**计量 ID**：9764F92C-E44A-498E-8DC1-AAD66587A810  
**计量名称**：BlobDataTransIn  
**单元**：传入数据 (GB)  
**注释**：Blob 服务传入数据 (GB)。  
  
**计量 ID**：3023FEF4-ECA5-4D7B-87B3-CFBC061931E8  
**计量名称**：BlobDataTransOut  
**单元**：传出数据 (GB)  
**注释**：Blob 服务传出数据 (GB)。  
  
**计量 ID**：EB43DD12-1AA6-4C4B-872C-FAF15A6785EA  
**计量名称**：QueueTransactions  
**单元**：请求计数以万为单位  
**注释**：队列服务请求数（以 10,000 计）。  
  
**计量 ID**：E518E809-E369-4A45-9274-2017B29FFF25  
**计量名称**：QueueDataTransIn  
**单元**：传入数据 (GB)  
**注释**：队列服务传入数据 (GB)。  
  
**计量 ID**：DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2  
**计量名称**：QueueDataTransOut  
**单元**：传出数据 (GB)  
**注释**：队列服务传出数据 (GB)。

### <a name="compute"></a>计算
  
**计量 ID**：FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5  
**计量名称**：基本 VM 大小小时  
**单元**：虚拟核心小时数  
**注释**：虚拟核心数乘以 VM 运行小时数。  
  
**计量 ID**：9CD92D4C-BAFD-4492-B278-BEDC2DE8232A  
**计量名称**：Windows VM 大小小时数  
**单元**：虚拟核心小时数  
**注释**：虚拟核心数乘以 VM 运行小时数。  
  
**计量 ID**：6DAB500F-A4FD-49C4-956D-229BB9C8C793  
**计量名称**：VM 大小小时数  
**单元**：VM 小时数  
**注释**：捕获基本 VM 和 Windows VM。 不针对核心进行调整。  
  
### <a name="managed-disks"></a>托管磁盘

**计量 ID**：380874f9-300c-48e0-95a0-d2d9a21ade8f **计量名称**：S4 **单位**：磁盘计数\*月 **备注**：标准托管磁盘 - 32 GB

**计量 ID**：1b77d90f-427b-4435-b4f1-d78adec53222 **计量名称**：S6 **单位**：磁盘计数\*月 **备注**：标准托管磁盘 - 64 GB

**计量 ID**：d5f7731b-f639-404a-89d0-e46186e22c8d **计量名称**：S10 **单位**：磁盘计数\*月 **备注**：标准托管磁盘 - 128 GB

**计量 ID**：ff85ef31-da5b-4eac-95dd-a69d6f97b18a **计量名称**：S15 **单位**：磁盘计数\*月 **备注**：标准托管磁盘 - 256 GB

**计量 ID**：88ea9228-457a-4091-adc9-ad5194f30b6e **计量名称**：S20 **单位**：磁盘计数\*月 **备注**：标准托管磁盘 - 512 GB

**计量 ID**：5b1db88a-8596-4002-8052-347947c26940 **计量名称**：S30 **单位**：磁盘计数\*月 **备注**：标准托管磁盘 - 1024 GB

**计量 ID**：7660b45b-b29d-49cb-b816-59f30fbab011 **计量名称**：P4 **单位**：磁盘计数\*月 **备注**：高级托管磁盘 - 32 GB

**计量 ID**：817007fd-a077-477f-bc01-b876f27205fd **计量名称**：P6 **单位**：磁盘计数\*月 **备注**：高级托管磁盘 - 64 GB

**计量 ID**：e554b6bc-96cd-4938-a5b5-0da990278519 **计量名称**：P10 **单位**：磁盘计数\*月 **备注**：高级托管磁盘 - 128 GB  

**计量 ID**：cdc0f53a-62a9-4472-a06c-e99a23b02907 **计量名称**：P15 **单位**：磁盘计数\*月 **备注**：高级托管磁盘 - 256 GB

**计量 ID**：b9cb2d1a-84c2-4275-aa8b-70d2145d59aa **计量名称**：P20 **单位**：磁盘计数\*月 **备注**：高级托管磁盘 - 512 GB

**计量 ID**：06bde724-9f94-43c0-84c3-d0fc54538369 **计量名称**：P30 **单位**：磁盘计数\*月 **备注**：高级托管磁盘 - 1024 GB

**计量 ID**：7ba084ec-ef9c-4d64-a179-7732c6cb5e28 **计量名称**：ActualStandardDiskSize **单位**：GB\*月 **说明**：标准托管磁盘的磁盘实际大小。

**计量 ID**：daef389a-06e5-4684-a7f7-8813d9f792d5  
**计量名称**：ActualPremiumDiskSize **单位**：GB\*月 **说明**：高级托管磁盘的磁盘实际大小。

**计量 ID**：108fa95b-be0d-4cd9-96e8-5b0d59505df1  
**计量名称**：ActualStandardSnapshotSize **单位**：GB\*月 **说明**：托管标准快照的磁盘实际大小。  

**计量 ID**：578ae51d-4ef9-42f9-85ae-42b52d3d83ac **计量名称**：ActualPremiumSnapshotSize **单位**：GB\*月 **说明**：高级托管磁盘的实际大小快照。

**计量 ID**：5d76e09f-4567-452a-94cc-7d1f097761f0 **计量名称**：S4 **单位**：磁盘计数\*小时 **备注**：标准托管磁盘 - 32 GB（已弃用）

**计量 ID**：dc9fc6a9-0782-432a-b8dc-978130457494 **计量名称**：S6 **单位**：磁盘计数\*小时 **备注**：标准托管磁盘 - 64 GB（已弃用）

**计量 ID**：e5572fce-9f58-49d7-840c-b168c0f01fff **计量名称**：S10 **单位**：磁盘计数\*小时 **备注**：标准托管磁盘 - 128 GB（已弃用）

**计量 ID**：9a8caedd-1195-4cd5-80b4-a4c22f9302b8 **计量名称**：S15 **单位**：磁盘计数\*小时 **备注**：标准托管磁盘 - 256 GB（已弃用）

**计量 ID**：5938f8da-0ecd-4c48-8d5a-c7c6c23546be **计量名称**：S20 **单位**：磁盘计数\*小时 **备注**：标准托管磁盘 - 512 GB（已弃用）

**计量 ID**：7705a158-bd8b-4b2b-b4c2-0782343b81e6 **计量名称**：S30 **单位**：磁盘计数\*小时 **备注**：标准托管磁盘 - 1024 GB（已弃用）

**计量 ID**：5c105f5f-cbdf-435c-b49b-3c7174856dcc **计量名称**：P4 **单位**：磁盘计数\*小时 **备注**：高级托管磁盘 - 32 GB（已弃用）

**计量 ID**：518b412b-1927-4f25-985f-4aea24e55c4f **计量名称**：P6 **单位**：磁盘计数\*小时 **备注**：高级托管磁盘 - 64 GB（已弃用）

**计量 ID**：5cfb1fed-0902-49e3-8217-9add946fd624 **计量名称**：P10 **单位**：磁盘计数\*小时 **备注**：高级托管磁盘 - 128 GB（已弃用）  

**计量 ID**：8de91c94-f740-4d9a-b665-bd5974fa08d4 **计量名称**：P15  
**单元**：磁盘计数\*小时 **备注**：高级托管磁盘 - 256 GB（已弃用）

**计量 ID**：c7e7839c-293b-4761-ae4c-848eda91130b **计量名称**：P20 **单位**：磁盘计数\*小时 **备注**：高级托管磁盘 - 512 GB（已弃用）

**计量 ID**：9f502103-adf4-4488-b494-456c95d23a9f **计量名称**：P30 **单位**：磁盘计数\*小时 **备注**：高级托管磁盘 - 1024 GB（已弃用）

**计量 ID**：8a409390-1913-40ae-917b-08d0f16f3c38 **计量名称**：ActualStandardDiskSize **单位**：字节\*小时 **备注**：标准托管磁盘的磁盘实际大小（已弃用）。  

**计量 ID**：1273b16f-8458-4c34-8ce2-a515de551ef6  
**计量名称**：ActualPremiumDiskSize **单位**：字节\*小时 **备注**：高级托管磁盘的磁盘实际大小（已弃用）。

**计量 ID**：89009682-df7f-44fe-aeb1-63fba3ddbf4c  
**计量名称**：ActualStandardSnapshotSize **单位**：字节\*小时 **备注**：托管标准快照的磁盘实际大小（已弃用）。

**计量 ID**：95b0c03f-8a82-4524-8961-ccfbf575f536 **计量名称**：ActualPremiumSnapshotSize **单位**：字节\*小时 **备注**：高级托管磁盘的实际大小（已弃用）快照。

**计量 ID**：75d4b707-1027-4403-9986-6ec7c05579c8 **计量名称**：ActualStandardSnapshotSize **单位**：GB\*月 **说明**：托管标准快照的磁盘实际大小（已弃用）。

**计量 ID**：5ca1cbb9-6f14-4e76-8be8-1ca91547965e **计量名称**：ActualPremiumSnapshotSize **单位**：GB\*月 **说明**：高级托管磁盘的实际大小（已弃用）快照。

### <a name="sql-rp"></a>Sql RP
  
**计量 ID**：CBCFEF9A-B91F-4597-A4D3-01FE334BED82  
**计量名称**：DatabaseSizeHourSqlMeter  
**单元**：MB\*小时  
**注释**：创建时的总数据库容量。 如果以每日粒度调用用量 API，计量器会返回 MB 乘以小时数。  
  
### <a name="mysql-rp"></a>MySql RP
  
**计量 ID**：E6D8CFCD-7734-495E-B1CC-5AB0B9C24BD3  
**计量名称**：DatabaseSizeHourMySqlMeter  
**单元**：MB\*小时  
**注释**：创建时的总数据库容量。 如果以每日粒度调用用量 API，计量器会返回 MB 乘以小时数。

### <a name="key-vault"></a>密钥保管库
  
**计量 ID**：EBF13B9F-B3EA-46FE-BF54-396E93D48AB4  
**计量名称**：Key Vault 事务  
**单元**：请求计数以万为单位  
**注释**：Key Vault 数据平面接收的 REST API 请求数。  
  
**计量 ID**：2C354225-B2FE-42E5-AD89-14F0EA302C87  
**计量名称**：高级密钥事务  
**单元**：10K 事务  
**注释**：RSA 3K/4K，ECC 密钥事务（预览版）。  
  
### <a name="app-service"></a>应用服务
  
**计量 ID**：190C935E-9ADA-48FF-9AB8-56EA1CF9ADAA  
**计量名称**：应用服务  
**单元**：虚拟核心小时数  
**注释**：用于运行应用服务的虚拟核心数。

>[!NOTE]
>Microsoft 使用此指标对 Azure Stack 集线器上的应用服务收费。 云解决方案提供商可能使用其他应用服务计量（如下所示）来计算其租户的使用情况。  
  
**计量 ID**：67CC4AFC-0691-48E1-A4B8-D744D1FEDBDE  
**计量名称**：Functions 请求  
**单元**：10 个请求  
**注释**：请求的执行总数（按 10 个执行计）。 每当函数为响应事件而运行，或者被绑定触发时，计为一次执行。  
  
**计量 ID**：D1D04836-075C-4F27-BF65-0A1130EC60ED  
**计量名称**：Functions - 计算  
**单元**：GB-s  
**注释**：以千兆字节/秒 (GB/s) 计量的资源消耗量。 **观察到的资源消耗** 的计算方式是：将平均内存大小 (GB) 乘以执行函数所需的时间（毫秒）。 在衡量函数使用的内存时，会向上舍入到最接近的 128 MB，最大内存大小为 1,536 MB，而在计算执行时间时，则向上舍入到最接近的 1 毫秒。 执行一次函数的最短执行时间和最小内存分别为 100 毫秒和 128 MB。  
  
**计量 ID**：957E9F36-2C14-45A1-B6A1-1723EF71A01D  
**计量名称**：共享应用服务小时数  
**单元**：1 小时 **备注**：共享应用服务计划的每小时使用情况。 计划按单个应用来计量。  
  
**计量 ID**：539CDEC7-B4F5-49F6-AAC4-1F15CFF0EDA9  
**计量名称**：免费应用服务小时数  
**单元**：1 小时 **备注**：免费应用服务计划的每小时使用情况。 计划按单个应用来计量。  
  
**计量 ID**：88039D51-A206-3A89-E9DE-C5117E2D10A6  
**计量名称**：小型标准应用服务小时数  
**单元**：1 小时 **备注**：根据大小与实例数进行计算。  
  
**计量 ID**：83A2A13E-4788-78DD-5D55-2831B68ED825  
**计量名称**：中型标准应用服务小时数  
**单元**：1 小时 **备注**：根据大小与实例数进行计算。  
  
**计量 ID**：1083B9DB-E9BB-24BE-A5E9-D6FDD0DDEFE6  
**计量名称**：大型标准应用服务小时数  
**单元**：1 小时 **备注**：根据大小与实例数进行计算。  
  
### <a name="custom-worker-tiers"></a>自定义辅助角色层
  
**计量 ID**：*自定义辅助角色层*
**计量名称**：自定义辅助角色层  
**单元**：小时 **备注**：确定性计量 ID 根据 SKU 和自定义辅助角色层名称来创建。 此计量 ID 对每个自定义辅助角色层来说都是唯一的。  
  
**计量 ID**：264ACB47-AD38-47F8-ADD3-47F01DC4F473  
**计量名称**：SNI SSL  
**单元**：按 SNI SSL 绑定  
**注释**：应用服务支持两种类型的 SSL 连接：服务器名称指示 (SNI) SSL 连接和 IP 地址 SSL 连接。 基于 SNI 的 SSL 适用于现代浏览器，而基于 IP 的 SSL 适用于所有浏览器。  
  
**计量 ID**：60B42D72-DC1C-472C-9895-6C516277EDB4  
**计量名称**：IP SSL **单位**：按基于 IP 的 SSL 绑定 **备注**：应用服务支持两种类型的 SSL 连接：服务器名称指示 (SNI) SSL 连接和 IP 地址 SSL 连接。 基于 SNI 的 SSL 适用于现代浏览器，而基于 IP 的 SSL 适用于所有浏览器。  
  
**计量 ID**：73215A6C-FA54-4284-B9C1-7E8EC871CC5B  
**计量名称**：Web 进程 **单位**：  
**注释**：按每小时每活动站点计算。
  
**计量 ID**：5887D39B-0253-4E12-83C7-03E1A93DFFD9  
**计量名称**：外部出口带宽  
**单元**：GB  
**注释**：总传入请求响应字节数 + 总传出请求字节数 + 总传入 FTP 请求响应类型数 + 总传入 Web 部署请求响应字节数。  
  
## <a name="how-do-the-azure-stack-hub-usage-apis-compare-to-the-azure-usage-api-currently-in-public-preview"></a>Azure Stack 集线器使用情况 Api 如何与 [Azure 使用情况 api](/azure/billing/billing-usage-rate-card-overview#azure-resource-usage-api-preview) (公共预览) 相比？

* 租户使用情况 API 与 Azure API 保持一致，但有一个例外： Azure Stack 中心当前不支持 *showDetails* 标志。
* 提供者使用情况 API 仅适用于 Azure Stack 集线器。
* 目前，Azure 中提供的 [RATECARD API](/azure/billing/billing-usage-rate-card-overview#azure-resource-ratecard-api-preview) 在 Azure Stack 集线器中不可用。

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>使用时间与报告时间有何差别？

用量数据报告包含两个主要时间值：

* **报告时间**：用量事件进入用量系统的时间。
* **使用时间**：使用 Azure Stack Hub 资源的时间。

你可能会发现，特定用量事件的“使用时间”与“报告时间”值有差异。 在任何环境中，延迟可能长达数小时。

目前，只能按“报告时间”查询。 

## <a name="what-do-these-usage-api-error-codes-mean"></a>这些用量 API 错误代码的含义是什么？

| **HTTP 状态代码** | 错误代码  | **说明** |
| --- | --- | --- |
| 400/错误的请求 |NoApiVersion |缺少 `api-version` 查询参数。 |
| 400/错误的请求 |InvalidProperty |属性缺失或使用了无效值。 响应正文中错误代码内的消息指示缺少属性。 |
| 400/错误的请求 |RequestEndTimeIsInFuture |`ReportedEndTime` 的值是将来时间。 此参数不允许将来的时间值。 |
| 400/错误的请求 |SubscriberIdIsNotDirectTenant |提供程序 API 调用使用的订阅 ID 不是调用方的有效租户。 |
| 400/错误的请求 |SubscriptionIdMissingInRequest |缺少调用方的订阅 ID。 |
| 400/错误的请求 |InvalidAggregationGranularity |请求的聚合粒度无效。 有效值为 daily 和 hourly。 |
| 503 |ServiceUnavailable |由于服务繁忙或调用受到限制，发生了可重试的错误。 |

## <a name="what-is-the-policy-for-charging-for-vms"></a>VM 收费政策是什么？

正在运行的和已停止的 VM 会生成使用情况数据。 与 Azure 一致的是，必须解除分配才能停止使用情况数据的发出。 如果门户不可用，但计算资源提供程序仍在运行，则会发出使用情况数据。

## <a name="how-do-i-extract-usage-data-from-the-azure-stack-hub-usage-apis"></a>如何通过 Azure Stack Hub 用量 API 提取用量数据？

若要在 Azure Stack Hub 上通过本地用量 API 提取用量数据，最简单的方法是使用 [GitHub 上的用量摘要脚本](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/Usagesummary.ps1)。 该脚本需要将开始日期和结束日期作为输入参数。

或者，可以使用 REST API，如[提供程序资源使用情况 API](azure-stack-provider-resource-api.md) 和[租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md) 文章中所述。

## <a name="how-can-i-associate-usage-extracted-from-azure-usage-apis-to-a-specific-azure-stack-hub-user-subscription"></a>如何将通过 Azure 用量 API 提取的用量与特定的 Azure Stack Hub 用户订阅相关联？

用量记录包含名为 **additionalinfo** 的属性包，其中包括 Azure Stack Hub 订阅 ID。 此 ID 是发出相应用量记录的用户订阅。

## <a name="next-steps"></a>后续步骤

* [Azure Stack Hub 中的客户计费和退款](azure-stack-billing-and-chargeback.md)
* [提供程序资源使用情况 API](azure-stack-provider-resource-api.md)
* [租户资源使用情况 API](azure-stack-tenant-resource-usage-api.md)
