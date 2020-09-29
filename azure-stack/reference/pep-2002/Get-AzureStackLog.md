---
title: Azure Stack Hub 的 Get-AzureStackLog 特权终结点
description: PowerShell Azure Stack 特权终结点 - Get-AzureStackLog 参考
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 34e25cafddde8b39077ae82bbc74b363c251a06b
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86487237"
---
# <a name="get-azurestacklog"></a>Get-AzureStackLog

## <a name="synopsis"></a>摘要
从具有超时的 Azure Stack Hub 的各种角色获取日志。
适用于多节点或单节点环境。

## <a name="syntax"></a>语法

```
Get-AzureStackLog [-FilterByRole <Object>] [-CustomerConfigurationFilePath <Object>] [-OutputPath <Object>]
 [-LocalAdminCredential <Object>] [-FilterByResourceProvider <Object>] [-InputSasUri <Object>]
 [-FilterByNode <Object>] [-OutputShareCredential <Object>] [-ToDate <Object>] [-FilterByLogType <Object>]
 [-TimeOutInMinutes <Object>] [-FromDate <Object>] [-OutputSharePath <Object>] [-OutputSasUri <Object>]
 [-PerformVMLogRecovery] [-IncludeDumpFile] [-AsJob]
```

## <a name="parameters"></a>parameters

### <a name="-filterbyrole"></a>-FilterByRole
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-filterbyresourceprovider"></a>-FilterByResourceProvider
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-filterbynode"></a>-FilterByNode
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-filterbylogtype"></a>-FilterByLogType
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-customerconfigurationfilepath"></a>-CustomerConfigurationFilePath
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-fromdate"></a>-FromDate
这将获取从上一小时开始的日志。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: (Get-Date).AddHours(-1)
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-todate"></a>-ToDate
这将获取截至当前日期的日志

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: (Get-Date)
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-inputsasuri"></a>-InputSasUri
要由其下载日志的 Blob 服务 SAS URI。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-outputpath"></a>-OutputPath
要将 zip 文件置于的根文件夹路径。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-outputsharepath"></a>-OutputSharePath
要将收集的日志移动到的共享路径。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-outputsharecredential"></a>-OutputShareCredential
共享凭据。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-outputsasuri"></a>-OutputSasUri
要将收集的日志移动到的 Blob SAS URI。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-localadmincredential"></a>-LocalAdminCredential
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-timeoutinminutes"></a>-TimeOutInMinutes
日志收集工具超时。

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 240
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-includedumpfile"></a>-IncludeDumpFile
默认禁用转储文件收集。

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

### <a name="-performvmlogrecovery"></a>-PerformVMLogRecovery
对 `Off`、`OffCritical`、`Paused` 和 `PausedCritical` VM 执行 WindowsEvents 日志恢复。

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

有关如何访问和使用特权终结点的信息，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。
