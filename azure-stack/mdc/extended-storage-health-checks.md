---
title: Azure Stack - Modular Data Center blob 存储的扩展存储的运行状况检查
description: 本文提供有关如何对 Modular Data Center blob 存储的扩展存储执行运行状况检查的指南。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2020
ms.author: patricka
ms.reviewer: karlt
ms.lastreviewed: 10/01/2020
ms.openlocfilehash: ebb39f3eab90b0f976e5fee2896cbe85d0d023f5
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872392"
---
# <a name="extended-storage-health-checks"></a>扩展存储运行状况检查

本文提供有关如何检查数据中心扩展存储硬件和 Azure Stack Hub 部署的运行状况的指南。

在开始之前，请查看[更新固件以获取扩展存储](extended-storage-firmware-updates.md)并按照其中的步骤进行操作。

推出系统前的最后一步是将其关闭。 按照快速入门指南中的步骤操作。

## <a name="extended-storage-health"></a>扩展存储运行状况

本部分提供有关如何检查扩展存储硬件的运行状况的指南。
检查事件，并在存在待解决的问题时通知管理员。 


若要检查群集存储池的运行状况，请运行以下命令：
```console
isi storagepool health
```

例如，如果运行正常，命令结果将如下所示：
```console
All pools are healthy.
Unprovisioned drives: none
```

如果运行不正常或驱动器缺失，命令结果将如下所示：

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

下一条命令将在简化的视图中检查群集的整体运行状况：
```console
isi status
```

如果一切正常，则会显示绿色的“正常”，否则“群集运行状况”行上或下表中的一个或多个群集节点 ID 行上会显示黄色警告或红色错误 。

如果需要有关群集运行状况的详细信息，可以运行更详细的命令，结果以扩展视图显示：
```console
isi status -a
```

联系 Microsoft 支持部门以获取有关任何问题的帮助。

## <a name="azure-stack-hub-health"></a>Azure Stack Hub 运行状况

本部分提供有关如何检查 Azure Stack Hub 部署的运行状况的指南。

若要全面了解与扩展存储系统集成的 Azure Stack 部署，请运行以下脚本（以下内容的包装器）：
- 使用提供的凭据连接到 ERCS VM
- 执行 Test-AzureStack -Debug 命令（它将详细的运行状况信息直接输出为屏幕上的输出）

此脚本的前提条件如下所示：
- .\Invoke-ExtendedStorageConfiguration.ps1 脚本文件，可在硬件生命周期主机 (HLH) 的 C:\OEMSoftware\ExtendedStorage\ 文件夹中找到
- $AzScred 凭据变量，应使用 DOMAIN\cloudadmin 凭据填充。 将 DOMAIN 替换为实际域名，如 CONTOSO。


```powershell
$AzScred = Get-Credential -Credential 'DOMAIN\cloudadmin'
.\Invoke-ExtendedStorageConfiguration.ps1 -TestAzureStack -AzureStackCred $AzScred
```

查看输出并验证 Azure Stack 部署的整体运行状态，并验证结果的 NAS 特定部分，以了解与 Azure Stack 集成的扩展存储的特定运行状态。

若要深入了解 Azure Stack 诊断，请参阅[验证 Azure Stack Hub 系统状态](../operator/azure-stack-diagnostic-test.md)。

## <a name="technical-support"></a>技术支持

联系 Microsoft 支持部门以获取有关任何问题的帮助。

## <a name="next-steps"></a>后续步骤

- [更新固件](extended-storage-firmware-updates.md)
