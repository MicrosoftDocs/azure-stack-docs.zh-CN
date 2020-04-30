---
title: Azure Stack 中心的 GraphApplication 特权终结点
description: PowerShell Azure Stack 特权终结点引用-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 164a20928f312d2ffa4dea73f54c05f41809ebf5
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562868"
---
# <a name="remove-graphapplication"></a>GraphApplication

## <a name="synopsis"></a>摘要
GraphApplication 是一个包装函数，用于在 AD FS 上调用 AD FS Graph cmdlet。

## <a name="syntax"></a>语法

```
Remove-GraphApplication [[-ApplicationIdentifier] <Object>] [-AsJob]
```

## <a name="description"></a>说明
调用 AD FS 上的 GraphApplicationGroup，以删除 AD FS 计算机上指定的应用程序。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
```
Remove-GraphApplication -ApplicationIdentifier "Application-Identifier-123456"
```

## <a name="parameters"></a>参数

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
要删除的应用程序的标识符

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