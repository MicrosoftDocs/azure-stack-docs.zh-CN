---
title: Azure Stack Hub 的 New-GraphApplication 特权终结点
description: PowerShell Azure Stack 特权终结点参考 - New-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: b0a9aeb2e7ec1035f24d89409f5fa23ef13500fb
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486608"
---
# <a name="new-graphapplication"></a>New-GraphApplication

## <a name="synopsis"></a>摘要
New-GraphApplication 是一个包装器函数，用于对 AD FS 调用 AD FS Graph cmdlet。

## <a name="syntax"></a>语法

```
New-GraphApplication [-ClientCertificates <Object>] [-Name <Object>] [-ClientRedirectUris <Object>]
 [-GenerateClientSecret] [-AsJob]
```

## <a name="description"></a>说明
对 AD FS 调用 New-GraphApplicationGroup 可将新应用程序添加到 AD FS 计算机。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
```
New-GraphApplication -Name $ApplicationName -ClientRedirectUris $redirectUri -ClientCertificates $certificate
```

## <a name="parameters"></a>parameters

### <a name="-name"></a>-Name
最大长度为 50 字符的应用程序的名称，它将被修改为 `Azurestack-$Name-$({guid}::{NewGuid}())` 并由函数返回。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-clientredirecturis"></a>-ClientRedirectUris
用于在 AD FS 中创建新应用程序的重定向 URI。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-clientcertificates"></a>-ClientCertificates
用于在 AD FS 中创建新应用程序的证书。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-generateclientsecret"></a>-GenerateClientSecret
 

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: False
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

若要了解如何访问和使用特权终结点，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。
