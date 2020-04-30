---
title: Azure Stack 中心的 TLSPolicy 特权终结点
description: 适用于 PowerShell Azure Stack 特权终结点的参考-TLSPolicy
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: a662ccbc76d25a936bd91bfe7ec499458c538321
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562692"
---
# <a name="set-tlspolicy"></a>TLSPolicy

## <a name="syntax"></a>语法

```
Set-TLSPolicy [[-Version] <Object>] [-AsJob]
```

## <a name="parameters"></a>参数

### <a name="-version"></a>-Version
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Accepted values: TLS_1.2, TLS_All

Required: False
Position: 0
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