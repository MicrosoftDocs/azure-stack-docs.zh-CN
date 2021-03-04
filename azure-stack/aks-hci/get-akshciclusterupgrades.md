---
title: Get-AksHciClusterUpgrades
author: jessicaguan
description: Get-AksHciClusterUpgrades PowerShell 命令获取 Azure Kubernetes Service 群集的可用 Kubernetes 升级。
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 3d5f4a6b67345baaa8df9b9eccc26d0a0503dafd
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873589"
---
# <a name="get-akshciclusterupgrades"></a>Get-AksHciClusterUpgrades

## <a name="synopsis"></a>摘要
获取 Azure Kubernetes Service 群集的可用 Kubernetes 升级。

## <a name="syntax"></a>语法

```powershell
Get-AksHciClusterUpgrades -name <String>
                          
```

## <a name="description"></a>说明
获取 Azure Kubernetes Service 群集的可用 Kubernetes 升级。

## <a name="examples"></a>示例

```powershell
PS C:\> Get-AksHciClusterUpgrades -name mycluster
```

## <a name="parameters"></a>parameters

### <a name="-name"></a>-name
群集的字母数字名称。

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
