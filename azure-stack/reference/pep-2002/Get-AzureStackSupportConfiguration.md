---
title: Azure Stack 中心的 AzureStackSupportConfiguration 特权终结点
description: PowerShell Azure Stack 特权终结点引用-AzureStackSupportConfiguration
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 710201cc045b356037a54e6f7f0eeee63284b0d6
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562549"
---
# <a name="get-azurestacksupportconfiguration"></a>AzureStackSupportConfiguration

## <a name="synopsis"></a>摘要
获取支持服务配置设置。

## <a name="syntax"></a>语法

```
Get-AzureStackSupportConfiguration [-IncludeRegistrationObjectId] [-AsJob]
```

## <a name="description"></a>说明
支持服务配置设置。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
下面的示例获取注册详细信息（如果标记已注册）或 null。

```
PS  C:\> Get-AzureStackSupportConfiguration
```

## <a name="parameters"></a>参数

### <a name="-includeregistrationobjectid"></a>-IncludeRegistrationObjectId
可选。
需要 internet 连接。
检索注册标识对象 ID。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-asjob"></a>-AsJob


```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

## <a name="notes"></a>说明
要求支持 VM 具有 internet 连接。

## <a name="next-steps"></a>后续步骤

有关如何访问和使用特权终结点的信息，请参阅[使用 Azure Stack 集线器中的特权终结点](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint)。