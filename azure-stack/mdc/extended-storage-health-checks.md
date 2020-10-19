---
title: 模块化数据中心 blob 存储的扩展存储中的 Azure Stack 运行状况检查
description: 本文介绍如何在模块化数据中心 blob 存储的扩展存储中执行运行状况检查。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: justinha
ms.reviewer: karlt
ms.lastreviewed: 10/01/2020
ms.openlocfilehash: 9d8e7915cb5c1ca9d0a073c750f67a5688157e74
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182860"
---
# <a name="extended-storage-health-checks"></a>扩展的存储运行状况检查

本文提供了有关检查数据中心扩展存储硬件和 Azure Stack 中心部署的运行状况的指南。

在开始之前，请查看并按照 [为扩展存储更新固件](extended-storage-firmware-updates.md)中的步骤进行操作。

发运系统之前的最后一步是将其关闭。 按照快速入门指南中的步骤操作。

## <a name="extended-storage-health"></a>扩展的存储运行状况

本部分提供有关检查扩展存储硬件的运行状况的指导。
检查事件，并在存在要解决的问题时通知管理员。 


若要检查群集存储池的运行状况，请运行以下命令：
```console
isi storagepool health
```

例如，如果运行正常，命令结果将如下所示：
```console
All pools are healthy.
Unprovisioned drives: none
```

如果不正常或驱动器丢失，命令结果将如下所示：

```console
SmartPools Health
Name                  Health  Type Prot   Members          Down          Smartfailed
--------------------- ------- ---- ------ ---------------- ------------- -------------
a2000_200tb_800gb-    -M---
ssd-sed_16gb
 a2000_200tb_800gb-   -M---   HDD  3x     3,11,14,19,23,30 Nodes:        Nodes:
ssd-sed_16gb:24                           ,38,41,46:bay6,1 Drives:       Drives:
                                          0-11,16,19,
                                          25:bay6,10-11,16

OK = Ok, U = Too few nodes, M = Missing drives,
D = Some nodes or drives are down, S = Some nodes or drives are smartfailed,
R = Some nodes or drives need repair
Unprovisioned drives: none
```

联系 Microsoft 支持部门以获取有关任何问题的帮助。

下一条命令将检查群集的总体运行状况，其中显示了简化的视图：
```console
isi status
```

如果一切正常，则会出现绿色的 **"确定"** ，否则在 **群集运行状况** 行或下表中的一个或多个群集节点 ID 行上出现黄色警告或红色错误。

如果需要有关群集运行状况的详细信息，可以在扩展视图中运行更详细的命令：
```console
isi status -a
```

联系 Microsoft 支持部门以获取有关任何问题的帮助。

## <a name="azure-stack-hub-health"></a>Azure Stack 中心运行状况

本部分提供有关检查 Azure Stack 中心部署的运行状况的指南。

若要获取与扩展存储系统集成的 Azure Stack 部署的总体视图，请运行以下脚本，它是以下内容的包装器：
- 使用提供的凭据连接到 ERCS VM
- 执行 Test-AzureStack 调试命令 (，它将详细的运行状况信息作为屏幕上的输出直接输出) 

此脚本的先决条件如下所示：
- .\Invoke-ExtendedStorageConfiguration.ps1 脚本文件，可在硬件生命周期主机的 C:\OEMSoftware\ExtendedStorage\ 文件夹中找到 (HLH) 
- $AzScred 凭据变量，应用 *域*\cloudadmin 凭据进行填充。 将 *domain* 替换为实际域名，例如 CONTOSO。


```powershell
$AzScred = Get-Credential -Credential 'DOMAIN\cloudadmin'
.\Invoke-ExtendedStorageConfiguration.ps1 -TestAzureStack -AzureStackCred $AzScred
```

查看输出并验证 Azure Stack 部署的总体 heath 状态，以及与 Azure Stack 扩展存储集成的特定运行状况状态的 **NAS** 特定的部分。

若要深入了解 Azure Stack 诊断，请参阅 [验证 Azure Stack 中心系统状态](../operator/azure-stack-diagnostic-test.md)。

## <a name="technical-support"></a>技术支持

联系 Microsoft 支持部门以获取有关任何问题的帮助。

## <a name="next-steps"></a>后续步骤

- [更新固件](extended-storage-firmware-updates.md)
