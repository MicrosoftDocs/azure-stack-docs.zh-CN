---
title: Remove-AksHciCluster
description: Remove-AksHciCluster PowerShell 命令删除托管的 Kubernetes 群集。
author: jessicaguan
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 868aa8a739c69ad6e29d73192f91c9bda73a3195
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873623"
---
# <a name="remove-akshcicluster"></a>Remove-AksHciCluster

## <a name="synopsis"></a>摘要
删除托管的 Kubernetes 群集。

## <a name="syntax"></a>语法

### <a name="delete-a-managed-kubernetes-cluster"></a>删除托管的 Kubernetes 群集
```powershell
Remove-AksHciCluster -name 
                    [-force]   
```

## <a name="description"></a>说明
删除托管的 Kubernetes 群集。

## <a name="examples"></a>示例

### <a name="delete-an-existing-managed-kubernetes-cluster"></a>删除现有的托管 Kubernetes 群集
```powershell
PS C:\> Remove-AksHciCluster -name myCluster
```

## <a name="parameters"></a>参数

### <a name="-name"></a>-name
Kubernetes 群集的名称。

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
