---
title: Azure Stack Hub 的 New-AzureStackActivation 特权终结点
description: PowerShell Azure Stack 特权终结点 - New-AzureStackActivation 参考
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 01182266cd53c060114e5ea555d9839a2b1b90bf
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486761"
---
# <a name="new-azurestackactivation"></a>New-AzureStackActivation

## <a name="synopsis"></a>摘要
激活 Azure Stack Hub。

## <a name="syntax"></a>语法

```
New-AzureStackActivation [[-TimeoutInSeconds] <Object>] [[-ActivationKey] <Object>] [-AsJob]
```

## <a name="parameters"></a>parameters

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

有关如何访问和使用特权终结点的信息，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。
