---
title: 向 Azure Stack 集线器发出 API 请求 |Microsoft Docs
description: 了解如何从 Azure 中检索身份验证，以便向 Azure Stack 集线器发出 API 请求。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 3defbf731a2b4b89fce8f9815513456104f80ea6
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883263"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="make-api-requests-to-azure-stack-hub"></a>向 Azure Stack 集线器发出 API 请求

可以使用 Azure Stack 集线器 REST Api 自动执行操作，例如将虚拟机（VM）添加到 Azure Stack 中心云。

Api 要求客户端向 Microsoft Azure 登录终结点进行身份验证。 终结点在发送到 Azure Stack 中心 Api 的每个请求的标头中返回要使用的令牌。 Microsoft Azure 使用 Oauth 2.0。

本文提供使用**卷**实用工具创建 Azure Stack 集线器请求的示例。 卷是一个命令行工具，它具有用于传输数据的库。 这些示例演示了检索令牌以访问 Azure Stack 中心 Api 的过程。 大多数编程语言都提供了 Oauth 2.0 库，它们具有强大的令牌管理功能，并处理任务（例如刷新令牌）。

查看将 Azure Stack 中心 REST Api 与常规 REST 客户端（如**卷**）结合使用的整个过程，以帮助你了解底层请求和响应有效负载中的预期内容。

本文不会探讨可用于检索令牌的所有选项，如交互式登录或创建专用应用 Id。 若要获取有关这些主题的信息，请参阅[Azure REST API 引用](/rest/api/)。

## <a name="get-a-token-from-azure"></a>从 Azure 获取令牌

创建使用内容类型 `x-www-form-urlencoded` 格式的请求正文以获取访问令牌。 将你的请求发布到 Azure REST 身份验证和登录终结点。

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**租户 ID**为：

- 租户域，如 `fabrikam.onmicrosoft.com`
- 你的租户 ID，例如 `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
- 与租户无关的密钥的默认值： `common`

### <a name="post-body"></a>文章正文

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

对于每个值：

- **grant_type**：  
   要使用的身份验证方案的类型。 在此示例中，值为 `password`。

- **资源**：  
   令牌访问的资源。 可以通过查询 Azure Stack 集线器管理元数据终结点来找到资源。 查看**访问群体**部分。

- **Azure Stack 集线器管理终结点**：

   ```bash
   https://management.{region}.{Azure Stack Hub domain}/metadata/endpoints?api-version=2015-01-01
   ```

  > [!NOTE]  
  > 如果你是尝试访问租户 API 的管理员，请确保使用租户终结点;例如，`https://adminmanagement.{region}.{Azure Stack Hub domain}/metadata/endpoints?api-version=2015-01-011`。

  例如，将 Azure Stack 开发工具包用作终结点：

   ```bash
   curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
   ```

  响应：

  ```bash
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
     "loginEndpoint":"https://login.windows.net/",
     "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
     }
  }
  ```

### <a name="example"></a>示例

  ```bash
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

- **client_id**

  此值硬编码为默认值：

  ```bash
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  可选选项可用于特定方案：

  | 应用程序 | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

- **username**

  例如，Azure Stack 中心 Azure AD 帐户：

  ```bash
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

- password

  Azure Stack 中心 Azure AD 管理员密码。

### <a name="example"></a>示例

请求：

```bash
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

响应：

```bash
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>API 查询

获取访问令牌后，将其作为标头添加到每个 API 请求。 若要将其添加为标头，请使用以下值创建**授权**标头： `Bearer <access token>`。 例如：

请求：

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

响应：

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>URL 结构和查询语法

泛型请求 URI 包含： `{URI-scheme} :// {URI-host} / {resource-path} ? {query-string}`

- **URI 方案**：  
URI 指示用于发送请求的协议。 例如，`http` 或 `https`。
- **URI 主机**：  
主机指定在其中托管 REST 服务终结点的服务器的域名或 IP 地址，例如 `graph.microsoft.com` 或 `adminmanagement.local.azurestack.external`。
- **资源路径**：  
路径指定资源或资源集合，其中可能包含服务在确定这些资源的选择时使用的多个段。 例如： `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` 可用于查询应用程序集合中特定应用程序所有者的列表。
- **查询字符串**：  
此字符串提供其他简单参数，例如 API 版本或资源选择条件。

## <a name="azure-stack-hub-request-uri-construct"></a>Azure Stack 中心请求 URI 构造

```bash
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>URI 语法

```bash
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>查询 URI 示例

```bash
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>后续步骤

有关使用 Azure REST 终结点的详细信息，请参阅[azure REST API 参考](/rest/api/)。
