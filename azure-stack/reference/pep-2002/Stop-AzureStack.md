---
title: Azure Stack Hub 的 Stop-AzureStack 特权终结点
description: PowerShell Azure Stack 特权终结点 - Stop-AzureStack 参考
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ae57a7845084e251429b2504c741305bfac9f690
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86485996"
---
# <a name="stop-azurestack"></a>Stop-AzureStack

## <a name="synopsis"></a>摘要
停止所有 Azure Stack Hub 服务。

## <a name="syntax"></a>语法

```
Stop-AzureStack [[-TimeoutInSecs] <Object>] [-AsJob]
```

## <a name="description"></a>说明
停止所有 Azure Stack 服务，并停止 Azure Stack Hub 运行于的物理计算机。

## <a name="parameters"></a>parameters

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
最长时间，超过后即停止执行。

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

有关如何访问和使用特权终结点的信息，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。
