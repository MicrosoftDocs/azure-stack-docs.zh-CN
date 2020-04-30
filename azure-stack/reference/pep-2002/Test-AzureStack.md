---
title: Azure Stack 中心的 Test-azurestack 特权终结点
description: PowerShell Azure Stack 特权终结点引用-Test-azurestack
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: e2c3b6213d31880b262501eb123d37eab7ec0a48
ms.sourcegitcommit: 54f98b666bea9226c78f26dc255ddbdda539565f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82563363"
---
# <a name="test-azurestack"></a>Test-AzureStack

## <a name="synopsis"></a>摘要
验证 Azure Stack 中心的状态。

## <a name="syntax"></a>语法

```
Test-AzureStack [-BackupShareCredential <Object>] [-Include <Object>] [-DetailedResults]
 [-ServiceAdminCredentials <Object>] [-List] [-Group <Object>] [-DoNotDeployTenantVm] [-Repair]
 [-Ignore <Object>] [-Confirm] [-TimeoutSeconds <Object>] [-AdminCredential <Object>]
 [-BackupSharePath <Object>] [-WhatIf] [-UseRunners] [-DisableConsoleOutput] [-AsJob]
```

## <a name="description"></a>说明
如果未检测到任何错误，Test-azurestack 返回`$true`。
如果检测到错误，它将`$false`返回。

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1

若要运行无操作员和用户方案的 Test-azurestack：
```
[azs-ercs01\]: PS\>Test-AzureStack
```

若要运行 Test-azurestack 和用户方案：
```
[azs-ercs01\]: PS\>Test-AzureStack -ServiceAdminCredentials \<PSCredential\>
```

## <a name="parameters"></a>参数

### <a name="-serviceadmincredentials"></a>-ServiceAdminCredentials
 

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

### <a name="-donotdeploytenantvm"></a>-DoNotDeployTenantVm
 

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

### <a name="-admincredential"></a>-AdminCredential
 

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

### <a name="-backupsharepath"></a>-BackupSharePath
 

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

### <a name="-backupsharecredential"></a>-BackupShareCredential
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: [PSCredential]::Empty
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-list"></a>-List
 

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

### <a name="-ignore"></a>-Ignore
 

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

### <a name="-include"></a>-Include
 

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

### <a name="-detailedresults"></a>-DetailedResults
 

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

### <a name="-timeoutseconds"></a>-TimeoutSeconds
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 1800
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-group"></a>-组
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: Default
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-repair"></a>-Repair
 

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

### <a name="-userunners"></a>-UseRunners
 

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

### <a name="-disableconsoleoutput"></a>-DisableConsoleOutput
 

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

### <a name="-whatif"></a>-WhatIf
显示在此 cmdlet 运行的情况下将会发生什么。
此 cmdlet 未运行。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi

Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-confirm"></a>-Confirm
提示你在运行 cmdlet 之前进行确认。

```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf

Required: False
Position: Named
Default value: None
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
