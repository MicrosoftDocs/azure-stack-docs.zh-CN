---
title: Install-AksHciAdAuth
author: jessicaguan
description: Install-AksHciAdAuth PowerShell 命令将安装 Active Directory 身份验证。
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: d89861da54cf7fef44d8da57077aba9f372022c3
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873598"
---
# <a name="install-akshciadauth"></a>Install-AksHciAdAuth

## <a name="synopsis"></a>摘要
安装 Active Directory 身份验证。

## <a name="syntax"></a>语法

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminUser <String>
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminGroup <String>    
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminUserSID <String>
                     [-TTL]    
                    
```

```powershell
Install-AksHciAdAuth -name <String>
                     -keytab [.\current.keytab]
                     [-previousKeytab <String>]
                     -SPN <String>
                     -adminGroupSID <String>    
                     [-TTL]    
                    
```

## <a name="description"></a>说明
安装 Active Directory 身份验证。

## <a name="examples"></a>示例

### <a name="example"></a>示例
```powershell
PS C:\> Install-AksHciAdAuth -name mynewcluster1 -keytab <.\current.keytab> -previousKeytab <.\previous.keytab> -SPN <service/principal@CONTOSO.COM> -adminUser CONTOSO\Bob
```

## <a name="parameters"></a>参数

### <a name="-name"></a>-name
Kubernetes 群集的字母数字名称。

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

### <a name="-keytab"></a>-keytab
包含 keytab 文件的文件路径。 请确保该文件的名称为 `current.keytab` 。

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

### <a name="-previouskeytab"></a>-previousKeytab
在 Active Directory 帐户密码更新之前使用的前一 keytab。 更改 Active Directory 密码时需要此密码。

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

### <a name="-spn"></a>-SPN
与 API 服务器 AD 帐户关联的服务主体的名称。

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

### <a name="-adminuser"></a>-adminUser
要向其授予群集管理权限的管理员的用户名。

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

### <a name="-admingroup"></a>-adminGroup
要向其授予群集管理权限的组名。

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

### <a name="-adminusersid"></a>-adminUserSID
要向其授予群集管理权限的用户 SID。

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

### <a name="-admingroupsid"></a>-adminGroupSID
要向其授予群集管理权限的组 SID。

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

### <a name="-ttl"></a>-TTL
如果给定，生存时间 (小时) `-previousKeytab` 。 默认值为10小时。

```yaml
Type: System.Int32
Parameter Sets: (All)
Aliases:

Required: False
Position: Named
Default value: 10
Accept pipeline input: False
Accept wildcard characters: False
```
