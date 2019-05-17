---
title: 在 Azure Stack 中注册租户以便进行使用情况跟踪 | Microsoft Docs
description: 详细介绍用于管理租户注册的操作，以及如何在 Azure Stack 中跟踪租户使用情况。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: faacd3faa954c1cad76bdc4601020e5d6c547537
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782243"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>在 Azure Stack 中管理租户注册

*适用于：Azure Stack 集成系统*

本文包含有关注册操作的详细信息。 可以使用这些操作：
- 管理租户注册
- 管理租户使用情况跟踪

可以找到有关如何添加、列出或删除租户映射的详细信息。 可以使用 PowerShell 或计费 API 终结点来管理使用情况跟踪。 可以找到有关如何添加、列出或删除租户映射的详细信息。 可以使用 PowerShell 或计费 API 终结点来管理使用情况跟踪。

## <a name="add-tenant-to-registration"></a>将租户添加到注册

如果要在注册中添加新租户，可以使用此操作。 在与其 Azure Active Directory (Azure AD) 租户连接的 Azure 订阅下报告租户使用情况。

如果想要更改与租户关联的订阅，也可以使用此操作。 调用 PUT/New-AzureRMResource 以覆盖以前的映射。

可以将单个 Azure 订阅与一个租户相关联。 如果尝试将第二个订阅添加到现有租户，则第一个订阅将被覆盖。

### <a name="use-api-profiles"></a>使用 API 配置文件

注册 cmdlet 要求你在运行 PowerShell 时指定一个 API 配置文件。 API 配置文件表示一组 Azure 资源提供程序及其 API 版本。 当与多个 Azure 云进行交互时，它们可帮助你使用 API 的正确版本。 例如，在使用全球 Azure 和 Azure Stack 时，可以使用多个云。 配置文件指定与其发布日期匹配的名称。 你将需要使用 **2017-09-03** 配置文件。

有关 Azure Stack 和 API 配置文件的详细信息，请参阅[在 Azure Stack 中管理 API 版本配置文件](../user/azure-stack-version-profiles.md)。

### <a name="parameters"></a>parameters

| 参数                  | 描述 |
|---                         | --- |
| registrationSubscriptionID | 用于初始注册的 Azure 订阅。 |
| customerSubscriptionID     | 属于要注册的客户的 Azure 订阅（非 Azure Stack）。 必须通过合作伙伴中心在云服务提供商 (CSP) 产品/服务中创建。 如果客户有多个租户，则为要登录 Azure Stack 的租户创建订阅。 |
| resourceGroup              | Azure 中用于存储注册的资源组。 |
| registrationName           | Azure Stack 的注册名称。 它是 Azure 中存储的对象。 该名称通常采用格式 azurestack-CloudID，其中 CloudID 是 Azure Stack 部署的云 ID。 |

> [!Note]  
> 租户需要注册到它们使用的每个 Azure Stack。 如果租户使用多个 Azure Stack，则需要使用租户订阅更新每个部署的初始注册。

### <a name="powershell"></a>PowerShell

使用 New-AzureRmResource cmdlet 添加一个租户。 [连接到 Azure Stack](azure-stack-powershell-configure-admin.md)，然后从提升的提示符使用以下 cmdlet：

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>API 调用

**Operation**：PUT  
**RequestURI**：`subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**响应**：201 Created  
**响应正文**：空  

## <a name="list-all-registered-tenants"></a>列出所有已注册租户

获取已添加到注册的所有租户列表。

 > [!Note]  
 > 如果没有租户已注册，则不会收到响应。

### <a name="parameters"></a>parameters

| 参数                  | 描述          |
|---                         | ---                  |
| registrationSubscriptionId | 用于初始注册的 Azure 订阅。   |
| resourceGroup              | Azure 中用于存储注册的资源组。    |
| registrationName           | Azure Stack 的注册名称。 它是 Azure 中存储的对象。 该名称通常采用格式 **azurestack**-***CloudID***，其中 ***CloudID*** 是 Azure Stack 部署的云 ID。   |

### <a name="powershell"></a>PowerShell

使用 Get-AzureRmResource cmdlet 列出所有已注册的租户。 [连接到 Azure Stack](azure-stack-powershell-configure-admin.md)，然后从提升的提示符使用以下 cmdlet：

```powershell
  Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 调用

可以使用 GET 操作获取所有租户映射的列表。

**Operation**：GET  
**RequestURI**：`subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**响应**：200  
**响应正文**： 

```JSON  
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

可以删除已添加到注册的租户。 如果该租户仍在使用 Azure Stack 上的资源，则会对初始 Azure Stack 注册中使用的订阅收取这些资源的使用费用。

### <a name="parameters"></a>parameters

| 参数                  | 描述          |
|---                         | ---                  |
| registrationSubscriptionId | 注册的订阅 ID。   |
| resourceGroup              | 注册的资源组。   |
| registrationName           | 注册的名称。  |
| customerSubscriptionId     | 客户订阅 ID。  |

### <a name="powershell"></a>PowerShell

使用 Remove-AzureRmResource cmdlet 删除租户。 [连接到 Azure Stack](azure-stack-powershell-configure-admin.md)，然后从提升的提示符使用以下 cmdlet：

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API 调用

可以使用 DELETE 操作删除租户映射。

**Operation**：DELETE  
**RequestURI**：`subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**响应**：204 无内容  
**响应正文**：空

## <a name="next-steps"></a>后续步骤

 - 若要详细了解如何从 Azure Stack 检索资源使用情况信息，请参阅 [Azure Stack 中的使用情况和计费](azure-stack-billing-and-chargeback.md)。
