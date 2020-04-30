---
title: Azure Stack 中心的 SyslogClient 特权终结点
description: 适用于 PowerShell Azure Stack 特权终结点的参考-SyslogClient
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: ecb66bdceef8ee4da5018f0f2ceac81cd7d57061
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562714"
---
# <a name="set-syslogclient"></a>SyslogClient

## <a name="synopsis"></a>摘要
导入并应用 syslog 客户端终结点证书。

## <a name="syntax"></a>语法

```
Set-SyslogClient [-OutputSeverity <Object>] [-PfxBinary <Object>] [-RemoveCertificate] [-CertPassword <Object>]
 [-AsJob]
```

## <a name="parameters"></a>参数

### <a name="-pfxbinary"></a>-PfxBinary
二进制格式的证书。
使用 "**获取内容**" 提取证书文件中的字节数组。

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

### <a name="-certpassword"></a>-CertPassword
安全字符串形式的证书密码。

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

### <a name="-removecertificate"></a>-RemoveCertificate
 

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

### <a name="-outputseverity"></a>-OutputSeverity
 

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