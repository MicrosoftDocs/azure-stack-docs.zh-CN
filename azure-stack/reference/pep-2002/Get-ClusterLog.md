---
title: Azure Stack Hub 的 Get-ClusterLog 特权终结点
description: PowerShell Azure Stack 特权终结点 - Get-ClusterLog 的参考信息
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: c8da0312a9c0d3f3250ed603523fd28b13dab38e
ms.sourcegitcommit: 30ea43f486895828710297967270cb5b8d6a1a18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2020
ms.locfileid: "93415362"
---
# <a name="get-clusterlog"></a>Get-ClusterLog

## <a name="syntax"></a>语法

```
Get-ClusterLog [-SkipClusterState] [-InputObject <Object>] [-Cluster <Object>] [-NetworkDiagnostics]
 [-Destination <Object>] [-NetworkDiagnosticsLevel <Object>] [[-Node] <Object>] [-TimeSpan <Object>]
 [-PerformanceHistoryTimeFrame <Object>] [-Health] [-ExportClusterPerformanceHistory] [-UseLocalTime] [-AsJob]
```

## <a name="parameters"></a>parameters

### <a name="-cluster"></a>-Cluster
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-destination"></a>-Destination
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-exportclusterperformancehistory"></a>-ExportClusterPerformanceHistory
导出 ClusterPorformanceHistory 数据。

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

### <a name="-health"></a>-Health
生成群集运行状况日志。

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

### <a name="-inputobject"></a>-InputObject
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-networkdiagnostics"></a>-NetworkDiagnostics
生成群集网络诊断日志。

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

### <a name="-networkdiagnosticslevel"></a>-NetworkDiagnosticsLevel
指定网络诊断日志的深度级别。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-node"></a>-Node
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 0
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-performancehistorytimeframe"></a>-PerformanceHistoryTimeFrame
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-skipclusterstate"></a>-SkipClusterState
生成群集日志而不检索群集状态信息。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: scs

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-timespan"></a>-TimeSpan
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases: Span

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-uselocaltime"></a>-UseLocalTime
使用本地时间而不是 GMT 生成群集日志。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: lt

Required: False
Position: Named
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

[https://go.microsoft.com/fwlink/?LinkId=216212](/powershell/module/failoverclusters/get-clusterlog)

有关如何访问和使用特权终结点的信息，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。