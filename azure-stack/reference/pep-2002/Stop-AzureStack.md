---
title: 停止-Test-azurestack Azure Stack 中心的特权终结点
description: PowerShell Azure Stack 特权终结点引用-Test-azurestack
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 946d31132e4a916574e8fb051e9761f81eb7cdf2
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563418"
---
# <a name="stop-azurestack"></a>Stop-AzureStack

## <a name="synopsis"></a>摘要
停止所有 Azure Stack 中心服务。

## <a name="syntax"></a>语法

```
Stop-AzureStack [[-TimeoutInSecs] <Object>] [-AsJob]
```

## <a name="description"></a>说明
停止所有 Azure Stack 服务，并停止正在运行 Azure Stack 中心的物理计算机。

## <a name="parameters"></a>参数

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
将停止执行的最大时间量。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 1
Default value: 2400
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