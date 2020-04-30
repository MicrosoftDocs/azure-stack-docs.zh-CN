---
title: Azure Stack 中心的 PrivilegedEndpoint 特权终结点
description: 适用于 PowerShell 的引用 Azure Stack 特权终结点-PrivilegedEndpoint
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 948d8771be47ca9a594da75d8e4d138ceb6b894c
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563440"
---
# <a name="set-telemetry"></a>设置-遥测

## <a name="synopsis"></a>摘要
启用或禁用将遥测数据传输到 Microsoft。

## <a name="syntax"></a>语法

```
Set-Telemetry [-Disable] [-Enable] [-AsJob]
```

## <a name="description"></a>说明
利用 Set-遥测 cmdlet，你可以通过更改注册表中的相应设置来控制是否向 Microsoft 发送遥测数据。

具体而言，此 cmdlet 会将域组策略配置为将遥测注册表值设置为0，并阻止 Windows UTC 服务在所有基础结构 Vm 和主机上运行。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
```
Set-Telemetry -Enable
```

### <a name="example-2"></a>示例 2
```
Set-Telemetry -Disable
```

## <a name="parameters"></a>参数

### <a name="-enable"></a>-Enable
启用将遥测数据传输到 Microsoft。

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

### <a name="-disable"></a>-Disable
禁止将遥测数据传输到 Microsoft。

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