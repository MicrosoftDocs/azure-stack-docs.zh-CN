---
title: Uninstall-AksHciAdAuth
author: jessicaguan
description: Uninstall-AksHciAdAuth PowerShell 命令卸载 AD 身份验证。
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 4239d9c268d57315584381e02fa121848395e683
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873574"
---
# <a name="uninstall-akshciadauth"></a>Uninstall-AksHciAdAuth

## <a name="synopsis"></a>摘要
卸载 Active Directory 身份验证。

## <a name="syntax"></a>语法

```powershell
Uninstall-AksHciAdAuth -name <String>
```

## <a name="description"></a>说明
卸载 Active Directory 身份验证。

## <a name="examples"></a>示例

### <a name="example"></a>示例
```powershell
PS C:\> Uninstall-AksHciAksHciAdAuth -name myCluster
```

## <a name="parameters"></a>参数

### <a name="-name"></a>-name
Kubernetes 群集的字母数字名称。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: True
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
