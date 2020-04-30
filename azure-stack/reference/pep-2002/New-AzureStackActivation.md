---
title: Azure Stack 中心的 AzureStackActivation 特权终结点
description: PowerShell Azure Stack 特权终结点引用-AzureStackActivation
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: fdd30f54973d5e1d6485e89b30c1ae811c950822
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563055"
---
# <a name="new-azurestackactivation"></a>新-AzureStackActivation

## <a name="synopsis"></a>摘要
激活 Azure Stack Hub。

## <a name="syntax"></a>语法

```
New-AzureStackActivation [[-TimeoutInSeconds] <Object>] [[-ActivationKey] <Object>] [-AsJob]
```

## <a name="parameters"></a>参数

### <a name="-activationkey"></a>-ActivationKey
 

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

### <a name="-timeoutinseconds"></a>-TimeoutInSeconds
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: 1000
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