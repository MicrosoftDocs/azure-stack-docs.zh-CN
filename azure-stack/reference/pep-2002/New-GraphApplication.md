---
title: Azure Stack 中心的 GraphApplication 特权终结点
description: PowerShell Azure Stack 特权终结点引用-GraphApplication
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: e9bd1d8fa51108f6384b29f33cf6cc0ea8cc166e
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563011"
---
# <a name="new-graphapplication"></a>新-GraphApplication

## <a name="synopsis"></a>摘要
GraphApplication 是一个包装函数，用于在 AD FS 上调用 AD FS Graph cmdlet。

## <a name="syntax"></a>语法

```
New-GraphApplication [-ClientCertificates <Object>] [-Name <Object>] [-ClientRedirectUris <Object>]
 [-GenerateClientSecret] [-AsJob]
```

## <a name="description"></a>说明
调用 AD FS 上的 GraphApplicationGroup，以将新应用程序添加到 AD FS 计算机上。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
```
New-GraphApplication -Name $ApplicationName -ClientRedirectUris $redirectUri -ClientCertificates $certificate
```

## <a name="parameters"></a>参数

### <a name="-name"></a>-Name
最大长度为50个字符的应用程序的名称，该名称将`Azurestack-$Name-$({guid}::{NewGuid}())`被修改为，并由函数返回。

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

有关如何访问和使用特权终结点的信息，请参阅[使用 Azure Stack 集线器中的特权终结点](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint)。