---
title: Azure Stack 中心的 GraphApplication 特权终结点
description: PowerShell Azure Stack 特权终结点引用-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: baccf889139b2a91b23b55ef9bb86387991558da
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562560"
---
# <a name="get-graphapplication"></a>GraphApplication

## <a name="synopsis"></a>摘要
GraphApplication 是一个包装函数，用于获取指定应用程序名称或标识符的图形应用程序信息。

## <a name="syntax"></a>语法

```
Get-GraphApplication [[-ApplicationIdentifier] <Object>] [-ApplicationName <Object>] [-AsJob]
```

## <a name="description"></a>说明
调用 ADFS 计算机上的 GraphApplicationGroup 以获取图形应用程序信息。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
```
Get-GraphApplication -ApplicationName $ApplicationName
```

## <a name="parameters"></a>参数

### <a name="-applicationidentifier"></a>-ApplicationIdentifier
应用程序在 AD FS 中注册的应用程序标识符。

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

有关如何访问和使用特权终结点的信息，请参阅[使用 Azure Stack 集线器中的特权终结点](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint)。