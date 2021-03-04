---
title: Get-AksHciCluster
author: jessicaguan
description: Get-AksHciCluster PowerShell 命令列出了包含 Azure Kubernetes 服务主机的 Kubernetes 托管群集。
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 14d7f807ef04432e7ea8c3c57dbb4bb80c64731b
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873582"
---
# <a name="get-akshcicluster"></a>Get-AksHciCluster

## <a name="synopsis"></a>摘要
列出包含 Azure Kubernetes 服务主机的 Kubernetes 托管群集。

## <a name="syntax"></a>语法

```powershell
Get-AksHciCluster [-name <String>]
```

## <a name="description"></a>说明
列出包含 Azure Kubernete 服务主机的 Kubernetes 托管群集。

## <a name="examples"></a>示例

### <a name="list-all-kubernetes-clusters"></a>列出所有 Kubernetes 群集
```powershell
PS C:\> Get-AksHciCluster
```

## <a name="parameters"></a>参数

### <a name="-name"></a>-name
群集的名称。

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
