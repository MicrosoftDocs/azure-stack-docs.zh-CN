---
title: Get-AksHciKubernetesVersion
author: jessicaguan
description: Get-AksHciKubernetesVersion PowerShell 命令列出了用于创建托管 Kubernetes 群集的可用版本。
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 76b87c1ddef2943181d30d43cf0bc06da644b110
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873609"
---
# <a name="get-akshcikubernetesversion"></a>Get-AksHciKubernetesVersion

## <a name="synopsis"></a>摘要
列出用于创建托管 Kubernetes 群集的可用版本。

## <a name="syntax"></a>语法

```powershell
Get-AksHciKubernetesVersion
```

## <a name="description"></a>说明
列出用于创建托管 Kubernetes 群集的可用版本。

## <a name="examples"></a>示例

### <a name="example"></a>示例 
```powershell
PS C:\> Get-AksHciKubernetesVersion
```

```Output
Linux {v1.16.10, v1.16.15, v1.17.11, v1.17.13...}
Windows {v1.18.8, v1.18.10}
```
