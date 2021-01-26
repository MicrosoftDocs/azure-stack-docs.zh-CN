---
title: Set-SyslogServer
description: 适用于 PowerShell Azure Stack 特权终结点的参考-Set-SyslogServer
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 3d6cb57660485d715a1136bdffa94682bc4dee2c
ms.sourcegitcommit: 2ac64ac431411b673e655465939d3c95cc94c55d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2021
ms.locfileid: "98810936"
---
# <a name="set-syslogserver"></a>Set-SyslogServer

## <a name="synopsis"></a>摘要
设置 syslog 服务器终结点。

## <a name="syntax"></a>语法

```
Set-SyslogServer [-Remove] [[-ServerName] <Object>] [-NoEncryption] [-SkipCertificateCheck] [-UseUDP]
 [-SkipCNCheck] [[-ServerPort] <Object>] [-AsJob]
```


## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1

```
Set-SyslogServer -ServerName <FQDN or IP address of Syslog server>
```

### <a name="example-2"></a>示例 2
```
-NoEncryption
```

### <a name="example-3"></a>示例 3
```
-SkipCertificateCheck
```

### <a name="example-4"></a>示例 4
```
-SkipCNCheck
```

### <a name="example-5"></a>示例 5
```
-UseUDP
```

### <a name="example-6"></a>示例 6
```
Set-SyslogServer -Remove
```

## <a name="parameters"></a>parameters

### <a name="-servername"></a>-ServerName
Syslog 服务器的 FQDN 或 IPv4 地址。
如果没有任何其他参数，默认协议为启用加密的 TCP；证书验证；证书名称检查。

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

### <a name="-serverport"></a>-ServerPort
Syslog 服务器的服务器端口。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: 2
Default value: 0
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-noencryption"></a>-NoEncryption
如果希望对 TCP 流量禁用加密，请指定 NoEncryption。

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

### <a name="-skipcncheck"></a>-SkipCNCheck
如果希望跳过检查证书名称，请指定 SkipCNCheck。

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

### <a name="-skipcertificatecheck"></a>-SkipCertificateCheck
如果希望跳过检查 Syslog 服务器证书，请指定 SkipCertificateCheck。

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

### <a name="-useudp"></a>-UseUDP
如果 Syslog 服务器使用 UDP 协议，请指定 UseUDP。

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

### <a name="-remove"></a>-Remove
 

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

有关如何访问和使用特权终结点的信息，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。
