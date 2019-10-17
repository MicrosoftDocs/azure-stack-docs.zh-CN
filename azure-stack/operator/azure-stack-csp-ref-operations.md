---
title: 在 Azure Stack 中注册使用情况跟踪的租户 |Microsoft Docs
description: 了解如何在 Azure Stack 中注册租户和如何跟踪租户使用情况。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/14/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/14/2019
ms.openlocfilehash: 72310e813d0dd0a64575f1b2452bf4a5191638ef
ms.sourcegitcommit: 97d41b3ebed07aa85a50087b6076671fd37e08c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72350177"
---
# <a name="register-tenants-for-usage-tracking-in-azure-stack"></a>在 Azure Stack 中注册使用情况跟踪的租户

*适用于： Azure Stack 集成系统*

本文包含有关注册操作的详细信息。 您可以使用以下操作来执行以下操作：

- 管理租户注册
- 管理租户使用情况跟踪

## <a name="add-tenant-to-registration"></a>将租户添加到注册

若要将新租户添加到注册，可以使用此操作。 租户使用情况在与 Azure Active Directory （Azure AD）租户连接的 Azure 订阅下报告。

你还可以使用此操作来更改与租户关联的订阅。 调用 PUT 或**move-azurermresource**以覆盖上一映射。

可以将单个 Azure 订阅与租户相关联。 如果尝试将第二个订阅添加到现有租户，将覆盖第一个订阅。

### <a name="use-api-profiles"></a>使用 API 配置文件

以下注册 cmdlet 要求在运行 PowerShell 时指定 API 配置文件。 API 配置文件表示一组 Azure 资源提供程序及其 API 版本。 它们有助于在与多个 Azure 云交互时使用正确版本的 API。 例如，如果在使用全局 Azure 并 Azure Stack 时使用多个云，则 API 配置文件将指定一个与发布日期匹配的名称。 使用**2017-09-03**配置文件。

有关 Azure Stack 和 API 配置文件的详细信息，请参阅[在 Azure Stack 中管理 API 版本配置文件](../user/azure-stack-version-profiles.md)。

### <a name="parameters"></a>parameters

| 参数                  | 描述 |
|---                         | --- |
| registrationSubscriptionID | 用于初始注册的 Azure 订阅。 |
| customerSubscriptionID     | 属于要注册的客户的 Azure 订阅（不 Azure Stack）。 必须通过合作伙伴中心在云解决方案提供商（CSP）产品/服务中创建。 如果客户有多个租户，请为租户创建订阅以登录到 Azure Stack。 |
| resourceGroup              | Azure 中存储注册的资源组。 |
| registrationName           | Azure Stack 注册的名称。 它是存储在 Azure 中的对象。 该名称的格式通常为**test-azurestack-CloudID**，其中**CloudID**是你的 AZURE STACK 部署的云 ID。 |

> [!NOTE]  
> 租户需要在其使用的每个 Azure Stack 部署中进行注册。 如果租户使用多个 Azure Stack，请使用租户订阅更新每个部署的初始注册。

### <a name="powershell"></a>PowerShell

使用**move-azurermresource** cmdlet 添加租户。 [连接到 Azure Stack](azure-stack-powershell-configure-admin.md)，然后在提升的提示符下，使用以下 cmdlet：

```powershell  
New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 调用

**操作**： PUT  
**RequestURI**： `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**响应**：201已创建  
**响应正文**：空  

## <a name="list-all-registered-tenants"></a>列出所有已注册的租户

获取已添加到注册的所有租户的列表。

 > [!NOTE]  
 > 如果未注册任何租户，则不会收到响应。

### <a name="parameters"></a>parameters

| 参数                  | 描述          |
|---                         | ---                  |
| registrationSubscriptionId | 用于初始注册的 Azure 订阅。   |
| resourceGroup              | Azure 中存储注册的资源组。    |
| registrationName           | Azure Stack 部署的注册名称。 它是存储在 Azure 中的对象。 该名称的格式通常为**test-azurestack-CloudID**，其中**CloudID**是你的 AZURE STACK 部署的云 ID。   |

### <a name="powershell"></a>PowerShell

使用**move-azurermresource** cmdlet 列出所有已注册的租户。 [连接到 Azure Stack](azure-stack-powershell-configure-admin.md)，然后在提升的提示符下运行以下 cmdlet：

```powershell
Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 调用

你可以使用 GET 操作获取所有租户映射的列表。

**操作**： GET  
**RequestURI**： `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?api-version=2017-06-01 HTTP/1.1`  
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

你可以删除已添加到注册的租户。 如果该租户仍在使用 Azure Stack 上的资源，则会向初始 Azure Stack 注册中使用的订阅收取其使用量。

### <a name="parameters"></a>parameters

| 参数                  | 描述          |
|---                         | ---                  |
| registrationSubscriptionId | 注册的订阅 ID。   |
| resourceGroup              | 用于注册的资源组。   |
| registrationName           | 注册的名称。  |
| customerSubscriptionId     | 客户订阅 ID。  |

### <a name="powershell"></a>PowerShell

使用**move-azurermresource** cmdlet 删除租户。 [连接到 Azure Stack](azure-stack-powershell-configure-admin.md)，然后在提升的提示符下运行以下 cmdlet：

```powershell
Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 调用

您可以使用删除操作删除租户映射。

**操作**：删除  
**RequestURI**： `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**响应**：204无内容  
**响应正文**：空

## <a name="next-steps"></a>后续步骤

- [如何从 Azure Stack 检索资源使用情况信息](azure-stack-billing-and-chargeback.md)
