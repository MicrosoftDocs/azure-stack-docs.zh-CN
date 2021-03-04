---
title: Get-AksHciLogs
author: jessicaguan
description: Get-AksHciLogs PowerShell 命令将创建一个压缩文件夹，其中包含来自所有 pod 的日志。
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: ef78efb91a6a8b5c0c91e815cb8aedd0a57adfe8
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873608"
---
# <a name="get-akshcilogs"></a>Get-AksHciLogs

## <a name="synopsis"></a>摘要
使用所有 pod 中的日志创建一个压缩文件夹。 

## <a name="syntax"></a>语法

```powershell
Get-AksHciLogs
```

## <a name="description"></a>说明
使用所有 pod 中的日志创建一个压缩文件夹。 此命令将在 `akshcilogs.zip` AZURE STACK HCI 工作目录上的 AKS 中创建一个名为的输出文件夹。 文件的完整路径 `akshcilogs.zip` 将为运行后的输出  `Get-AksHciLogs` (例如， `C:\AksHci\0.9.6.3\akshcilogs.zip` ，其中 `0.9.6.3` 是 Azure Stack HCI release NUMBER) 上的 AKS。

## <a name="examples"></a>示例

### <a name="example"></a>示例
```powershell
PS C:\> Get-AksHciLogs
```
