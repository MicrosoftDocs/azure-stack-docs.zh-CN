---
title: Azure Stack 中心的 Get-azurestacklog 特权终结点
description: PowerShell Azure Stack 特权终结点引用-Get-azurestacklog
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: d7e7ff712e1d8f7c062d7b3a7f38d97b80085e31
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563187"
---
# <a name="get-azurestacklog"></a>Get-AzureStackLog

## <a name="synopsis"></a>摘要
通过超时从 Azure Stack 中心的各种角色获取日志。
适用于多节点或单节点环境。

## <a name="syntax"></a>语法

```
Get-AzureStackLog [-FilterByRole <Object>] [-CustomerConfigurationFilePath <Object>] [-OutputPath <Object>]
 [-LocalAdminCredential <Object>] [-FilterByResourceProvider <Object>] [-InputSasUri <Object>]
 [-FilterByNode <Object>] [-OutputShareCredential <Object>] [-ToDate <Object>] [-FilterByLogType <Object>]
 [-TimeOutInMinutes <Object>] [-FromDate <Object>] [-OutputSharePath <Object>] [-OutputSasUri <Object>]
 [-PerformVMLogRecovery] [-IncludeDumpFile] [-AsJob]
```

## <a name="parameters"></a>参数

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
这将从上一小时开始获取日志。

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
这将在当前日期之前获得日志

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
要从中下载日志的 Blob 服务 SAS Uri。

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
要放置 zip 文件的根文件夹路径。

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
共享路径，在其中移动收集的日志。

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
Blob SAS Uri 用于移动收集的日志的位置。

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
默认情况下禁用转储文件收集。

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
对`Off`、 `OffCritical`、 `Paused`、 `PausedCritical` vm 执行 WindowsEvents 日志恢复。

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

有关如何访问和使用特权终结点的信息，请参阅[使用 Azure Stack 集线器中的特权终结点](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint)。