---
title: Azure Stack Hub 的 Get-GraphApplication 特权终结点
description: PowerShell Azure Stack 特权终结点参考 - Get-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: a100b974a3d622c0b9bb51d850d9a8435d72f549
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487084"
---
# <a name="get-graphapplication"></a>Get-GraphApplication

## <a name="synopsis"></a>摘要
Get-GraphApplication 是一个包装器函数，用于获取指定应用程序名称或标识符的 Graph 应用程序信息。

## <a name="syntax"></a>语法

```
Get-GraphApplication [[-ApplicationIdentifier] <Object>] [-ApplicationName <Object>] [-AsJob]
```

## <a name="description"></a>说明
对 ADFS 计算机调用 Get-GraphApplicationGroup 可获取 Graph 应用程序信息。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
```
Get-GraphApplication -ApplicationName $ApplicationName
```

## <a name="parameters"></a>parameters

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
在 AD FS 中注册的应用程序的应用程序标识符。

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

### <a name="-applicationname"></a>-ApplicationName
在 ADFS 中注册的应用程序的应用程序名称。

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

若要了解如何访问和使用特权终结点，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。
