---
title: 替换物理磁盘
titleSuffix: Azure Stack
description: 了解如何替换 Azure Stack 中的物理磁盘。
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
ms.date: 12/02/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/02/2019
ms.openlocfilehash: 0700ca4caefbec2245f2303720a675aece6c21e6
ms.sourcegitcommit: fd7d43738f275f36dacfa0786697e7c44d405abb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694817"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>替换 Azure Stack 中的物理磁盘

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*

本文介绍了在 Azure Stack 中替换物理磁盘的一般过程。 如果物理磁盘发生故障，应尽快更换。

> [!Note]  
> 替换物理数据驱动器**不**需要将缩放单位节点提前置于维护模式（排出）范围中。 此外，在物理驱动器被替换后，不需要使用 Azure Stack 中心管理员门户来修复缩放单位节点。 以下文章提供了有关在需要修复时[替换 Azure Stack 缩放单位节点上的硬件组件](azure-stack-replace-component.md)的详细信息。

可以将此过程用于集成系统，并适用于具有热插拔磁盘的 Azure Stack 开发工具包（ASDK）部署。

实际的磁盘更换步骤因原始设备制造商 (OEM) 硬件供应商而异。 请参阅供应商的现场可更换单元 (FRU) 文档来了解特定于你的系统的详细步骤。

## <a name="review-disk-alert-information"></a>查看磁盘警报信息
磁盘发生故障时，你将收到一个警报，告知你与物理磁盘的连接已丢失。

![显示 Azure Stack 管理中的物理磁盘的连接丢失的警报](media/azure-stack-replace-disk/DiskAlert.png)

如果打开警报，则警报描述将包含必须替换的磁盘的缩放单位节点和确切物理槽位置。 Azure Stack 通过使用 LED 指示器功能，进一步帮助你确定发生故障的磁盘。

## <a name="replace-the-physical-disk"></a>替换物理磁盘

请按照 OEM 硬件供应商的 FRU 说明进行实际磁盘更换。

> [!note]
> 一次将一个缩放单位节点的磁盘替换为一个。 等待虚拟磁盘修复作业完成，然后再转到下一个缩放单位节点。

若要防止在集成系统中使用不受支持的磁盘，系统将阻止供应商不支持的磁盘。 如果尝试使用不受支持的磁盘，则新的警报会告诉你磁盘已被隔离，因为不支持该模式或固件。

替换磁盘后，Azure Stack 会自动发现新磁盘并启动虚拟磁盘修复过程。

## <a name="check-the-status-of-virtual-disk-repair-using-azure-stack-powershell"></a>使用 Azure Stack PowerShell 检查虚拟磁盘修复状态

替换磁盘后，可以使用 Azure Stack PowerShell 监视虚拟磁盘运行状况状态并修复作业进度。

1. 检查是否已安装 Azure Stack PowerShell。 有关详细信息，请参阅[Install PowerShell for Azure Stack](azure-stack-powershell-install.md)。
2. 使用 PowerShell 作为操作员连接到 Azure Stack。 有关详细信息，请参阅[使用 PowerShell 作为操作员连接到 Azure Stack](azure-stack-powershell-configure-admin.md)。
3. 运行以下 cmdlet 以验证虚拟磁盘运行状况和修复状态：

    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name
    Get-AzsVolume -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Select-Object VolumeLabel, OperationalStatus, RepairStatus
    ```

    ![Azure Stack Powershell 中的卷运行状况](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

4. 验证 Azure Stack 系统状态。 有关说明，请参阅[验证 Azure Stack 系统状态](azure-stack-diagnostic-test.md)。
5. 或者，你可以运行以下命令来验证替换的物理磁盘的状态。

```powershell  
$scaleunit=Get-AzsScaleUnit
$StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name

Get-AzsDrive -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Sort-Object StorageNode,MediaType,PhysicalLocation | Format-Table Storagenode, Healthstatus, PhysicalLocation, Model, MediaType,  CapacityGB, CanPool, CannotPoolReason
```

![将 Azure Stack 中的物理磁盘替换为 Powershell](media/azure-stack-replace-disk/check-replaced-physical-disks-azure-stack.png)

## <a name="check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint"></a>使用特权终结点检查虚拟磁盘修复状态

替换磁盘后，可以使用特权终结点监视虚拟磁盘运行状况状态并修复作业进度。 请在任何连接到特权终结点的计算机上执行以下步骤。

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

   ![VirtualDisk 命令的 Powershell 输出](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. 运行以下命令以查看当前存储作业状态：
    ```powershell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ```
      ![Get-storagejob 命令的 Powershell 输出](media/azure-stack-replace-disk/GetStorageJobOutput.png)

4. 验证 Azure Stack 系统状态。 有关说明，请参阅[验证 Azure Stack 系统状态](azure-stack-diagnostic-test.md)。

## <a name="troubleshoot-virtual-disk-repair-using-the-privileged-endpoint"></a>使用特权终结点排查虚拟磁盘修复问题

如果虚拟磁盘修复作业出现堵塞，请运行以下命令来重新启动该作业：
  ```powershell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```
