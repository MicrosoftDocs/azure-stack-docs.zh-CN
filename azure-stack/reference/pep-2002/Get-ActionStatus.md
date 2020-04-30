---
title: Azure Stack 中心的 ActionStatusprivileged 终结点
description: PowerShell Azure Stack 特权终结点引用-ActionStatus
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: f7300224d6b512361c978c33a2f599dfb9a1d852
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562681"
---
# <a name="get-actionstatus"></a>ActionStatus

## <a name="synopsis"></a>摘要
获取具有指定的函数名称的操作的最新操作的状态。

## <a name="syntax"></a>语法

```
Get-ActionStatus [[-ActionType] <Object>] [[-FunctionName] <Object>] [-AsJob]
```

## <a name="description"></a>说明
获取具有指定的函数名称的操作的最新操作的状态。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
```
Get-ActionStatus -FunctionName "Start-SecretRotation"
```

## <a name="parameters"></a>参数

### <a name="-functionname"></a>-FunctionName
正在为其查询状态的函数的名称。

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

### <a name="-actiontype"></a>-ActionType
操作操作的类型名称。

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
