---
title: Azure Stack Hub 的 Start-SecretRotation 特权终结点 cmdlet
description: PowerShell Azure Stack Hub 特权终结点参考 - Start-SecretRotation
author: mattbriggs
ms.topic: reference
ms.date: 07/29/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 8a3dee9ad26c37626c0f3383b874f149be4fac01
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742612"
---
# <a name="start-secretrotation"></a>Start-SecretRotation

## <a name="synopsis"></a>摘要
触发戳记上的机密轮换。

## <a name="syntax"></a>语法

```
Start-SecretRotation [-PathAccessCredential <Object>] [-ReRun] [-CertificatePassword <Object>] [-Internal]
 [-PfxFilesPath <Object>] [-AsJob]
```

## <a name="description"></a>说明
为 Azure Stack Hub 系统的基础结构机密调用机密轮换过程。 默认情况下，它只轮换外部网络基础结构终结点的证书。 请参阅[在 Azure Stack Hub 中轮换机密](../../operator/azure-stack-rotate-secrets.md)，了解更多详细信息。

## <a name="parameters"></a>parameters

### <a name="-internal"></a>-Internal
轮换内部网络基础结构终结点的机密。

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

### <a name="-pfxfilespath"></a>-PfxFilesPath
共享用于外部证书轮换的新 pfx 文件的路径。
如果需要外部证书轮换，则这是必需的。

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

### <a name="-pathaccesscredential"></a>-PathAccessCredential
用于访问 PfxFilesPath 的凭据。
如果需要外部证书轮换，则这是必需的。

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

### <a name="-certificatepassword"></a>-CertificatePassword
提供的新 pfx 文件的密码。
如果需要外部证书轮换，则这是必需的。
它可能与初始部署时提供的原始 pfx 密码不同。
我们将使用正确的 CA 密码重新生成 pfx 文件。

用法：

```console
    # Rotates external certificates only
    Start-SecretRotation -PfxFilesPath \<String\> -PathAccessCredential \<PSCredential\> -CertificatePassword \<SecureString\>

    # Rotates internal secrets only
    Start-SecretRotation -Internal  

    # Reruns internal secrets only
    Start-SecretRotation -Internal -ReRun 

    # Reruns external certificates only
    Start-SecretRotation -ReRun
```

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

### <a name="-rerun"></a>-ReRun
 

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
