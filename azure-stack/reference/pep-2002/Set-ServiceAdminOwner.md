---
title: Azure Stack 中心的 ServiceAdminOwner 特权终结点
description: 适用于 PowerShell Azure Stack 特权终结点的参考-ServiceAdminOwner
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: b44a15c2b5ae1569e747a0c371d11950b5a40297
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562769"
---
# <a name="set-serviceadminowner"></a>ServiceAdminOwner

## <a name="synopsis"></a>摘要
用于更新服务管理员的脚本。

## <a name="syntax"></a>语法

```
Set-ServiceAdminOwner [[-TimeoutInSecs] <Object>] [[-ServiceAdminOwnerUpn] <Object>] [-AsJob]
```

## <a name="description"></a>说明
用于更新服务管理员 UPN 的脚本。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
```
Set-ServiceAdminOwner -NewServiceAdminUpn "administrator@contoso.com" -TimeoutInSecs 1000
```

## <a name="parameters"></a>参数

### <a name="-serviceadminownerupn"></a>-ServiceAdminOwnerUpn
 

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

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

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