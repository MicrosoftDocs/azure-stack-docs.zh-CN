---
title: Azure Stack 中心的 PrivilegedEndpoint 特权终结点
description: 适用于 PowerShell 的引用 Azure Stack 特权终结点-PrivilegedEndpoint
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: dafeb8c162ef79b5522a80d398f2bc440164cdc2
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562967"
---
# <a name="register-customdnsserver"></a>注册-CustomDnsServer

## <a name="synopsis"></a>摘要
用于向 Azure Stack 中心 DNS 注册自定义 DNS 服务器的脚本。 此脚本将在 Azure Stack Hub DNS 中创建条件性转发。

## <a name="syntax"></a>语法

```
Register-CustomDnsServer [[-TimeoutInSecs] <Object>] [[-CustomDomainName] <Object>]
 [[-CustomDnsIPAddresses] <Object>] [-AsJob]
```

## <a name="description"></a>说明
用于向 Azure Stack DNS 注册自定义 DNS 服务器的脚本。
此脚本将在 Azure Stack DNS 中创建条件性转发

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
```
Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "10.20.20.01","10.20.20.02" -TimeoutInSecs 1000
```

## <a name="parameters"></a>参数

### <a name="-customdomainname"></a>-CustomDomainName
 

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

### <a name="-customdnsipaddresses"></a>-CustomDnsIPAddresses
 

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

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 3
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