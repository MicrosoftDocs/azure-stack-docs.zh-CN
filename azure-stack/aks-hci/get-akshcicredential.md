---
title: Get-AksHciCredential
author: jessicaguan
description: Get-AksHciCredential PowerShell 命令使用 kubectl 访问群集。
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 50f99f6e5a64a1a0b687c0dfdc42b407b4106500
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873610"
---
# <a name="get-akshcicredential"></a>Get-AksHciCredential

## <a name="synopsis"></a>摘要
使用访问群集 `kubectl` 。 这将使用指定群集的 _kubeconfig_ 文件作为的默认 _kubeconfig_ 文件 `kubectl` 。

## <a name="syntax"></a>语法

```powershell
Get-AksHciCredential -name <String>
                    [-outputLocation <String>]
                    [-adAuth]
```

## <a name="description"></a>说明
使用 kubectl 访问群集。

## <a name="examples"></a>示例

### <a name="access-your-cluster-using-kubectl"></a>使用 kubectl 访问群集。
```powershell
PS C:\> Get-AksHciCredential -name myCluster
```

## <a name="parameters"></a>参数

### <a name="-name"></a>-name
群集的名称。

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

### <a name="-outputlocation"></a>-outputLocation
要将 kubeconfig 下载到的位置。 默认值为 `%USERPROFILE%\.kube`。

```yaml
Type: System.String
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: %USERPROFILE%\.kube
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-adauth"></a>-adAuth
使用此标志可获取 kubeconfig 的 Active Directory SSO 版本。

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
