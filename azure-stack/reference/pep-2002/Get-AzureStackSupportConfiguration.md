---
title: Azure Stack Hub 的 Get-AzureStackSupportConfiguration 特权终结点
description: PowerShell Azure Stack 特权终结点的引用 - Get-AzureStackSupportConfiguration
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 97f6bd0fb751885a2a4a4553aba0d7a9329e5458
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487220"
---
# <a name="get-azurestacksupportconfiguration"></a>Get-AzureStackSupportConfiguration

## <a name="synopsis"></a>摘要
获取支持服务配置设置。

## <a name="syntax"></a>语法

```
Get-AzureStackSupportConfiguration [-IncludeRegistrationObjectId] [-AsJob]
```

## <a name="description"></a>说明
支持服务配置设置。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
下面的示例在标记已注册的情况下获取注册详细信息，否则显示为 null。

```
PS  C:\> Get-AzureStackSupportConfiguration
```

## <a name="parameters"></a>parameters

### <a name="-includeregistrationobjectid"></a>-IncludeRegistrationObjectId
可选。
需要建立 Internet 连接。
检索注册标识对象 ID。

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

## <a name="notes"></a>注释
要求“支持 VM”具有 Internet 连接。

## <a name="next-steps"></a>后续步骤

有关如何访问和使用特权终结点的信息，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。
