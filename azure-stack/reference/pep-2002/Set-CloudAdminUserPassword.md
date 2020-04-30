---
title: Azure Stack 中心的 CloudAdminUserPassword 特权终结点
description: 适用于 PowerShell Azure Stack 特权终结点的参考-CloudAdminUserPassword
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 93864226716673c07e54844356d50c49f52edb09
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562791"
---
# <a name="set-cloudadminuserpassword"></a>Set-CloudAdminUserPassword

## <a name="syntax"></a>语法

```
Set-CloudAdminUserPassword [[-UserName] <Object>] [[-CurrentPassword] <Object>] [[-NewPassword] <Object>]
 [-AsJob]
```

## <a name="parameters"></a>参数

### <a name="-currentpassword"></a>-CurrentPassword
 

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

### <a name="-newpassword"></a>-NewPassword
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-username"></a>-UserName
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

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