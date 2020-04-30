---
title: Azure Stack 中心的 Get-storagejob 特权终结点
description: PowerShell Azure Stack 特权终结点引用-Get-storagejob
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: fad5223778bb49b20d3af86f781c6864c9bdc8c9
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562538"
---
# <a name="get-storagejob"></a>Get-StorageJob

## <a name="syntax"></a>语法

```
Get-StorageJob [-CimSession <Object>] [-Name <Object>] [-VirtualDisk <Object>] [-JobState <Object>]
 [-UniqueId <Object>] [-ThrottleLimit <Object>] [-AsJob] [-StoragePool <Object>] [-StorageSubsystem <Object>]
 [-Volume <Object>] [-Disk <Object>]
```

## <a name="parameters"></a>参数

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

### <a name="-cimsession"></a>-CimSession
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases: Session

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-disk"></a>-Disk
 

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

### <a name="-jobstate"></a>-JobState
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Accepted values: New, Starting, Running, Suspended, ShuttingDown, Completed, Terminated, Killed, Exception, Service, QueryPending

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-name"></a>-Name
 

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

### <a name="-storagepool"></a>-StoragePool
 

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

### <a name="-storagesubsystem"></a>-StorageSubsystem
 

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

### <a name="-throttlelimit"></a>-ThrottleLimit
 

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

### <a name="-uniqueid"></a>-UniqueId
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases: Id

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-virtualdisk"></a>-VirtualDisk
 

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

### <a name="-volume"></a>-卷
 

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

## <a name="inputs"></a>输入

### <a name="systemstring"></a>System.String[]

### <a name="microsoftmanagementinfrastructureciminstance"></a>CimInstance。

## <a name="outputs"></a>Outputs

### <a name="microsoftmanagementinfrastructureciminstance"></a>CimInstance。

### <a name="microsoftmanagementinfrastructureciminstancerootmicrosoftwindowsstoragemsft_storagejob"></a>CimInstance # ROOT/Microsoft/Windows/Storage/MSFT_StorageJob

## <a name="next-steps"></a>后续步骤

有关如何访问和使用特权终结点的信息，请参阅[使用 Azure Stack 集线器中的特权终结点](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint)。