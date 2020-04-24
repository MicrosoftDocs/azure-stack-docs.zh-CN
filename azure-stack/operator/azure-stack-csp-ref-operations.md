---
title: 在 Azure Stack Hub 中注册租户以跟踪使用情况
description: 了解如何在 Azure Stack Hub 中注册租户以及如何跟踪租户使用情况。
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/14/2019
ms.openlocfilehash: 72fdb2bf4d19291ff5b0a83c2233354bde4ec201
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "79294876"
---
# <a name="register-tenants-for-usage-tracking-in-azure-stack-hub"></a>在 Azure Stack Hub 中注册租户以跟踪使用情况

本文包含有关注册操作的详细信息。 可以使用这些操作：

- 管理租户注册
- 管理租户使用情况跟踪

## <a name="add-tenant-to-registration"></a>将租户添加到注册

如果要在注册中添加新租户，可以使用此操作。 在与 Azure Active Directory (Azure AD) 租户连接的 Azure 订阅下报告租户使用情况。

还可以使用此操作来更改与租户关联的订阅。 调用 PUT 或 **New-AzureRMResource** PowerShell cmdlet 来覆盖以前的映射。

可以将单个 Azure 订阅与一个租户相关联。 如果尝试将第二个订阅添加到现有租户，则第一个订阅将被覆盖。

### <a name="use-api-profiles"></a>使用 API 配置文件

以下注册 cmdlet 要求你在运行 PowerShell 时指定一个 API 配置文件。 API 配置文件表示一组 Azure 资源提供程序及其 API 版本。 当与多个 Azure 云进行交互时，它们可帮助你使用 API 的正确版本。 例如，如果你在使用全局 Azure 和 Azure Stack Hub 时使用多个云，则 API 配置文件指定一个与其发布日期相匹配的名称。 使用 **2017-09-03** 配置文件。

有关 Azure Stack Hub 和 API 配置文件的详细信息，请参阅[在 Azure Stack Hub 中管理 API 版本配置文件](../user/azure-stack-version-profiles.md)。

### <a name="parameters"></a>parameters

| 参数                  | 说明 |
|---                         | --- |
| registrationSubscriptionID | 用于初始注册的 Azure 订阅。 |
| customerSubscriptionID     | 属于要注册的客户的 Azure 订阅（非 Azure Stack Hub）。 必须通过合作伙伴中心在云解决方案提供商 (CSP) 产品/服务中创建。 如果客户有多个租户，则为要登录 Azure Stack Hub 的租户创建订阅。 |
| resourceGroup              | Azure 中用于存储注册的资源组。 |
| registrationName           | Azure Stack Hub 的注册名称。 它是 Azure 中存储的对象。 该名称通常采用格式 **azurestack-CloudID**，其中 **CloudID** 是 Azure Stack Hub 部署的云 ID。 |

> [!NOTE]  
> 租户必须注册到它们使用的每个 Azure Stack Hub 部署。 如果租户使用多个 Azure Stack Hub，则需要使用租户订阅更新每个部署的初始注册。

### <a name="powershell"></a>PowerShell

使用 **New-AzureRmResource** cmdlet 添加一个租户。 [连接到 Azure Stack Hub](azure-stack-powershell-configure-admin.md)，然后从提升的提示符使用以下 cmdlet：

```powershell  
New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 调用

**操作**：PUT  
**RequestURI**：`subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**响应**：201 已创建  
**响应正文**：空  

## <a name="list-all-registered-tenants"></a>列出所有已注册租户

获取已添加到注册的所有租户列表。

 > [!NOTE]  
 > 如果没有租户已注册，则不会收到响应。

### <a name="parameters"></a>parameters

| 参数                  | 说明          |
|---                         | ---                  |
| registrationSubscriptionId | 用于初始注册的 Azure 订阅。   |
| resourceGroup              | Azure 中用于存储注册的资源组。    |
| registrationName           | Azure Stack Hub 部署的注册名称。 它是 Azure 中存储的对象。 该名称通常采用格式 **azurestack-CloudID**，其中 **CloudID** 是 Azure Stack Hub 部署的云 ID。   |

### <a name="powershell"></a>PowerShell

使用 **Get-AzureRmResource** cmdlet 列出所有已注册的租户。 [连接到 Azure Stack Hub](azure-stack-powershell-configure-admin.md)，然后从提升的提示符运行以下 cmdlet：

```powershell
Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 调用

可以使用 GET 操作获取所有租户映射的列表。

**操作**：GET  
**RequestURI**：`subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?api-version=2017-06-01 HTTP/1.1`  
**响应**：200  
**响应正文**：

```json
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}",
            "name": " cspSubscriptionId 1",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}",
            "name": " cspSubscriptionId2 ",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>删除租户映射

可以删除已添加到注册的租户。 如果该租户仍在使用 Azure Stack Hub 上的资源，则会对初始 Azure Stack Hub 注册中使用的订阅收取这些资源的使用费用。

### <a name="parameters"></a>parameters

| 参数                  | 说明          |
|---                         | ---                  |
| registrationSubscriptionId | 注册的订阅 ID。   |
| resourceGroup              | 注册的资源组。   |
| registrationName           | 注册的名称。  |
| customerSubscriptionId     | 客户订阅 ID。  |

### <a name="powershell"></a>PowerShell

使用 **Remove-AzureRmResource** cmdlet 删除租户。 [连接到 Azure Stack Hub](azure-stack-powershell-configure-admin.md)，然后从提升的提示符运行以下 cmdlet：

```powershell
Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 调用

可以使用 DELETE 操作删除租户映射。

**操作**：DELETE  
**RequestURI**：`subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**响应**：204 无内容  
**响应正文**：空

## <a name="next-steps"></a>后续步骤

- [如何从 Azure Stack Hub 中检索资源使用情况信息](azure-stack-billing-and-chargeback.md)
