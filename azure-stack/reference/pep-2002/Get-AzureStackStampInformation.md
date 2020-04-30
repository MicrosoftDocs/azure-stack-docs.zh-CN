---
title: Azure Stack 中心的 AzureStackStampInformation 特权终结点
description: PowerShell Azure Stack 特权终结点引用-AzureStackStampInformation
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 3318ce344804174fe3252808d5857e93b8113e22
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562571"
---
# <a name="get-azurestackstampinformation"></a>Get-AzureStackStampInformation

## <a name="synopsis"></a>摘要
获取标记信息。

## <a name="syntax"></a>语法

```
Get-AzureStackStampInformation [-AsJob]
```

## <a name="description"></a>说明
导入 ECE 客户端模块并在 ECE 服务的云角色上启动操作计划。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
```
Get-AzureStackStampInformation
```

## <a name="parameters"></a>参数

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