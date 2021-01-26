---
title: Set-Telemetry
description: 适用于 PowerShell Azure Stack 特权终结点的参考-Set-Telemetry
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 89e9182dd5ebb7a7ab9da6e9d4e7b21da6d4ce9e
ms.sourcegitcommit: 2ac64ac431411b673e655465939d3c95cc94c55d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98810915"
---
# <a name="set-telemetry"></a>Set-Telemetry

## <a name="synopsis"></a>摘要
启用或禁用将遥测数据传输到 Microsoft。

## <a name="syntax"></a>语法

```
Set-Telemetry [-Disable] [-Enable] [-AsJob]
```

## <a name="description"></a>说明
Set-Telemetry cmdlet 允许通过更改注册表中的相应设置来控制是否将遥测数据发送到 Microsoft。

具体而言，此 cmdlet 将域组策略配置为将遥测注册表值设置为 0，并使 Windows UTC 服务停止在任何基础结构 VM 和主机上运行。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
```
Set-Telemetry -Enable
```

### <a name="example-2"></a>示例 2
```
Set-Telemetry -Disable
```

## <a name="parameters"></a>parameters

### <a name="-enable"></a>-Enable
启用将遥测数据传输到 Microsoft。

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

### <a name="-disable"></a>-Disable
禁用将遥测数据传输到 Microsoft。

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

## <a name="next-steps"></a>后续步骤

有关如何访问和使用特权终结点的详细信息，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。
