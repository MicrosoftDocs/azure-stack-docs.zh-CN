---
title: Azure Stack Hub 的 Register-CustomAdfs 特权终结点
description: PowerShell Azure Stack 特权终结点的引用 - Register-CustomAdfs
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 743b8f5d8f1a2a6cb9f6e44fc61510a5da394ef6
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486557"
---
# <a name="register-customadfs"></a>Register-CustomAdfs

## <a name="synopsis"></a>摘要
用于通过使用 Azure Stack AD FS 将自定义 Active Directory 联合身份验证服务 (AD FS) 注册为声明提供程序的脚本

## <a name="syntax"></a>语法

```
Register-CustomAdfs [-CustomADFSFederationMetadataEndpointUri <Object>]
 [-CustomADFSFederationMetadataFileContent <Object>] [-TimeoutInSecs <Object>] [-CustomAdfsName <Object>]
 [-SigningCertificateRevocationCheck <Object>] [-EncryptionCertificateRevocationCheck <Object>] [-AsJob]
```

## <a name="description"></a>说明
用于通过使用 Azure Stack AD FS 将自定义 Active Directory 联合身份验证服务 (AD FS) 注册为声明提供程序的脚本

## <a name="examples"></a>示例

### <a name="example-1"></a>示例 1
```
Register-CustomAdfs -CustomAdfsName "Contoso" -CustomADFSFederationMetadataEndpointUri "https://adfs.contoso.com/federationmetadata/2007-06/federationmetadata.xml"  -TimeoutInSecs 1000
```

### <a name="example-2"></a>示例 2
```
Register-CustomAdfs -CustomAdfsName "Contoso" -CustomADFSFederationMetadataFile "c:\temp\FederationMetadata.xml" -TimeoutInSecs 1000
```
## <a name="parameters"></a>parameters

### <a name="-customadfsname"></a>-CustomAdfsName
 

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

### <a name="-customadfsfederationmetadataendpointuri"></a>-CustomADFSFederationMetadataEndpointUri
 

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

### <a name="-customadfsfederationmetadatafilecontent"></a>-CustomADFSFederationMetadataFileContent
 

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

### <a name="-encryptioncertificaterevocationcheck"></a>-EncryptionCertificateRevocationCheck
 

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

### <a name="-signingcertificaterevocationcheck"></a>-SigningCertificateRevocationCheck
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: CheckChainExcludeRoot
Accept pipeline input: False
Accept wildcard characters: False
```

### <a name="-timeoutinsecs"></a>-TimeoutInSecs
 

```yaml
Type: Object
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 1000
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

有关如何访问和使用特权终结点的详细信息，请参阅[使用 Azure Stack Hub 中的特权终结点](../../operator/azure-stack-privileged-endpoint.md)。
