---
title: Azure Stack Hub 的 Set-CloudAdminUserPassword 特权终结点
description: PowerShell Azure Stack 特权终结点 - Set-CloudAdminUserPassword 参考信息
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 78daede2c45c8ff0cc811ef8361dadace3066e62
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486200"
---
# <a name="set-cloudadminuserpassword"></a>Set-CloudAdminUserPassword

## <a name="syntax"></a>语法

```
Set-CloudAdminUserPassword [[-UserName] <Object>] [[-CurrentPassword] <Object>] [[-NewPassword] <Object>]
 [-AsJob]
```

## <a name="parameters"></a>parameters

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

有关如何访问和使用特权终结点的信息，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。
