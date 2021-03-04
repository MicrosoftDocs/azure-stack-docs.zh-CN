---
title: Update-AksHciCluster
author: jessicaguan
description: Update-AksHciCluster PowerShell 命令将托管的 Kubernetes 群集更新为较新的 Kubernetes 或 OS 版本。
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: a8fc5c1e87ed78891333af7a3a998767110428ea
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873575"
---
# <a name="update-akshcicluster"></a>Update-AksHciCluster

## <a name="synopsis"></a>摘要
将托管的 Kubernetes 群集更新为较新的 Kubernetes 或 OS 版本。

## <a name="syntax"></a>语法

```powershell
Update-AksHciCluster -name <String>
                    [-kubernetesVersion <String>]
                    [-operatingSystem]
```

## <a name="description"></a>说明
将托管的 Kubernetes 群集更新为较新的 Kubernetes 或 OS 版本。

## <a name="examples"></a>示例

### <a name="example"></a>示例
```powershell
PS C:\> Update-AksHciCluster -clusterName myCluster -kubernetesVersion v1.18.8 -operatingSystem
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

### <a name="-kubernetesversion"></a>-kubernetesVersion
要升级到的 Kubernetes 的版本。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-operatingsystem"></a>-操作系统
如果要更新到下一版本的操作系统，请使用此标志。

```yaml
Type: System.Management.Automation.SwitchParameter
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

