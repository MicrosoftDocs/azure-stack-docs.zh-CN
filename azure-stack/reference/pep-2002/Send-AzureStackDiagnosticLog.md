---
title: Azure Stack 中心的 AzureStackDiagnosticLog 特权终结点
description: 适用于 PowerShell Azure Stack 特权终结点的参考-AzureStackDiagnosticLog
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: f0e7bf2ebd7c3d5ef238a1e0e946a482c1e8d4a6
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562802"
---
# <a name="send-azurestackdiagnosticlog"></a>AzureStackDiagnosticLog

## <a name="synopsis"></a>摘要
向 Microsoft 发送 Azure Stack 集线器诊断日志。

## <a name="syntax"></a>语法

```
Send-AzureStackDiagnosticLog [[-FromDate] <Object>] [[-FilterByResourceProvider] <Object>]
 [[-FilterByRole] <Object>] [[-ToDate] <Object>] [-AsJob]
```

## <a name="description"></a>说明
发出请求以支持桥服务，以上载标记的诊断日志。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1

下面的示例将最后4小时的日志发送给 Microsoft。

```
PS  C:\> Send-AzureStackDiagnosticLog
```

### <a name="example-2"></a>示例 2
下面的示例将最后4个小时的支持服务和 WAS 日志发送给 Microsoft。
```
PS  C:\> Send-AzureStackDiagnosticLog -FilterByRole SupportBridgeController,WAS
```

## <a name="parameters"></a>参数

### <a name="-filterbyrole"></a>-FilterByRole
可选。
按基础结构角色类型进行筛选。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-filterbyresourceprovider"></a>-FilterByResourceProvider
可选。
按值筛选-添加资源提供程序类型。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-fromdate"></a>-FromDate
可选。
用于收集日志的开始时间。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-todate"></a>-ToDate
可选。
用于收集日志的结束时间。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 4
Default value: None
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

## <a name="next-steps"></a>后续步骤

有关如何访问和使用特权终结点的信息，请参阅[使用 Azure Stack 集线器中的特权终结点](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint)。