---
title: Azure Stack Hub 的 Send-AzureStackDiagnosticLog 特权终结点
description: PowerShell Azure Stack 特权终结点 - Send-AzureStackDiagnosticLog 参考信息
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 2b62eb35f096ab5b38ee37ff776435bb90b7c210
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486183"
---
# <a name="send-azurestackdiagnosticlog"></a>Send-AzureStackDiagnosticLog

## <a name="synopsis"></a>摘要
将 Azure Stack Hub 诊断日志发送到 Microsoft。

## <a name="syntax"></a>语法

```
Send-AzureStackDiagnosticLog [[-FromDate] <Object>] [[-FilterByResourceProvider] <Object>]
 [[-FilterByRole] <Object>] [[-ToDate] <Object>] [-AsJob]
```

## <a name="description"></a>说明
请求“支持网桥服务”，以上传用于标记的诊断日志。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1

下面的示例将过去 4 小时的日志发送给 Microsoft。

```
PS  C:\> Send-AzureStackDiagnosticLog
```

### <a name="example-2"></a>示例 2
下面的示例发送过去 4 小时的支持服务，并将 WAS 日志发送给 Microsoft。
```
PS  C:\> Send-AzureStackDiagnosticLog -FilterByRole SupportBridgeController,WAS
```

## <a name="parameters"></a>parameters

### <a name="-filterbyrole"></a>-FilterByRole
可选。
按“基础结构角色”类型筛选。

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
按“增值资源提供程序”类型筛选。

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
日志收集的开始时间。

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
日志收集的结束时间。

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

有关如何访问和使用特权终结点的详细信息，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。
