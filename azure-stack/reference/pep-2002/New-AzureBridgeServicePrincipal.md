---
title: Azure Stack Hub 的 New-AzureBridgeServicePrincipal 特权终结点
description: PowerShell Azure Stack 特权终结点 - New-AzureBridgeServicePrincipal 参考
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ffcf27ad55cdc4a5456f89fb076ca42120078d5d
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486778"
---
# <a name="new-azurebridgeserviceprincipal"></a>New-AzureBridgeServicePrincipal

## <a name="synopsis"></a>摘要
在 Azure Active Directory (Azure AD) 中创建新服务主体。

## <a name="syntax"></a>语法

```
New-AzureBridgeServicePrincipal [[-TenantId] <Object>] [[-AzureEnvironment] <Object>]
 [[-TimeoutInSeconds] <Object>] [[-RefreshToken] <Object>] [-AsJob]
```

## <a name="parameters"></a>parameters

### <a name="-azureenvironment"></a>-AzureEnvironment
 

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

### <a name="-tenantid"></a>-TenantId
 

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

### <a name="-refreshtoken"></a>-RefreshToken
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
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
Position: 4
Default value: 420
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
