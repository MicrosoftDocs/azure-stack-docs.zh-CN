---
title: 更换 Azure Stack 中的物理磁盘 | Microsoft Docs
description: 概述如何更换 Azure Stack 中的物理磁盘的过程。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: bfe18e0aa59f81f614ae00057b2c1f287b1257da
ms.sourcegitcommit: cf9440cd2c76cc6a45b89aeead7b02a681c4628a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2019
ms.locfileid: "66469272"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>更换 Azure Stack 中的物理磁盘

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

本文介绍更换 Azure Stack 中的物理磁盘的一般过程。 如果物理磁盘发生故障，应尽快更换它。

可以将此过程用于集成系统，以及用于具有热插拔磁盘的开发工具包部署。

实际的磁盘更换步骤将因原始设备制造商 (OEM) 硬件供应商而异。 请参阅供应商的现场可更换部件 (fru) 文档的详细步骤，特定于您的系统。

## <a name="review-disk-alert-information"></a>查看磁盘警报信息
当磁盘发生故障时，你会收到通知你物理磁盘已丢失的警报。

![显示物理磁盘连接丢失的警报](media/azure-stack-replace-disk/DiskAlert.png)

如果打开该警报，警报说明包含必须更换之磁盘的缩放单元节点和确切物理插槽位置。 Azure Stack 会使用 LED 指示器功能来进一步帮助你确定发生故障的磁盘。

## <a name="replace-the-disk"></a>更换磁盘

请按照 OEM 硬件供应商的 FRU 说明来实际更换磁盘。

> [!note]
> 每次更换一个缩放单元节点的磁盘。 等待虚拟磁盘修复作业完成，然后移到下一个缩放单元节点

为了防止在集成系统中使用不支持的磁盘，系统会阻止供应商不支持的磁盘。 如果尝试使用不支持的磁盘，则会有新警报通知你因为不支持该磁盘的型号或固件，所以已将该磁盘隔离。

更换磁盘后，Azure Stack 会自动发现新磁盘，并启动虚拟磁盘修复过程。

## <a name="check-the-status-of-virtual-disk-repair-using-azure-stack-powershell"></a>检查使用 Azure Stack PowerShell 的虚拟磁盘修复状态

更换磁盘后，你可以使用 Azure Stack PowerShell 监视虚拟磁盘运行状况状态和修复作业进度。

1. 检查已安装的 Azure Stack PowerShell。 有关详细信息，请参阅[安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。
2. 以操作员身份使用 PowerShell 连接到 Azure Stack。 有关详细信息，请参阅[连接到 Azure Stack 操作员的 PowerShell](azure-stack-powershell-configure-admin.md)。
3. 运行以下 cmdlet 以验证虚拟磁盘运行状况和修复状态：
    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name
    Get-AzsVolume -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Select-Object VolumeLabel, OperationalStatus, RepairStatus
    ```

    ![Azure Stack 卷运行状况](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

4. 验证 Azure Stack 系统状态。 有关说明，请参阅[验证 Azure Stack 系统状态](azure-stack-diagnostic-test.md)。
5. （可选） 可以运行以下命令以验证被替换的物理磁盘的状态。

```powershell  
$scaleunit=Get-AzsScaleUnit
$StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name

Get-AzsDrive -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Format-Table Storagenode, Healthstatus, PhysicalLocation, Model, MediaType,  CapacityGB, CanPool, CannotPoolReason
```

![替换为 Azure Stack 中的物理磁盘](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

## <a name="check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint"></a>检查使用特权终结点的虚拟磁盘修复状态
 
更换磁盘后，可以使用特权终结点监视虚拟磁盘运行状况和修复作业进度。 从任何可以通过网络连接到特权终结点的计算机，按照下列步骤进行操作。

1. 打开 Windows PowerShell 会话并连接到特权终结点。
    ```powershell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ``` 
  
2. 运行以下命令以查看虚拟磁盘运行状况：
    ```powershell
        Get-VirtualDisk -CimSession s-cluster
    ```
   ![Get-VirtualDisk 命令的 Powershell 输出](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. 运行以下命令以查看当前的存储作业状态：
    ```powershell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ```
      ![Get-StorageJob 命令的 Powershell 输出](media/azure-stack-replace-disk/GetStorageJobOutput.png)

4. 验证 Azure Stack 系统状态。 有关说明，请参阅[验证 Azure Stack 系统状态](azure-stack-diagnostic-test.md)。


## <a name="troubleshoot-virtual-disk-repair-using-the-privileged-endpoint"></a>对虚拟磁盘修复使用特权终结点进行故障排除

如果虚拟磁盘修复作业出现停滞，请运行以下命令来重新启动作业：
  ```powershell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ``` 
