---
title: Uninstall-AksHci
author: jessicaguan
description: Uninstall-AksHci PowerShell 命令将删除 Azure Stack HCI 上的 AKS。
ms.topic: reference
ms.date: 2/12/2021
ms.author: jeguan
ms.openlocfilehash: 89cd1bd48518f90dd4ce850e35e42c3106681823
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101873627"
---
# <a name="uninstall-akshci"></a>Uninstall-AksHci

## <a name="synopsis"></a>摘要
Azure Stack HCI 上删除 Azure Kubernetes 服务。

## <a name="syntax"></a>语法

```powershell
Uninstall-AksHci [-skipConfigCleanup]
```

## <a name="description"></a>说明
Azure Stack HCI 上删除 Azure Kubernetes 服务。 

如果 PowerShell 命令在以前用于部署 Windows 管理中心的群集上运行，则 PowerShell 模块会检查 Windows 管理中心配置文件是否存在。 Windows 管理中心将 Windows 管理中心配置文件放在所有节点上。 

## <a name="example"></a>示例

### <a name="example"></a>示例
```powershell
PS C:\> Uninstall-AksHci
```

## <a name="parameters"></a>参数

### <a name="-skipconfigcleanup"></a>-skipConfigCleanup
卸载后，跳过删除配置。 如果你使用此标志，则将保留你的旧配置。

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
