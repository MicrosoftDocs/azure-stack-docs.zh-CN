---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 12/16/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: aaa08aa85701cda0dd6f85bb77726e227e849f71
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867263"
---
上传 VHD 之前，必须验证 VHD 是否满足要求。 不符合要求的 VHD 将无法在 Azure Stack Hub 中加载。

1. 你将使用 Hyper-V 提供的 PowerShell 模块。 激活 Hyper-V 会安装支持 PowerShell 模块。 你可以通过使用提升的提示符打开 PowerShell 并运行以下 cmdlet 来检查是否具有该模块：

    ```powershell  
    Get-Command -Module hyper-v
    ```

    如果你没有 Hyper-V 命令，请参阅[使用 Hyper-V 和 Windows PowerShell](/virtualization/hyper-v-on-windows/quick-start/try-hyper-v-powershell)。 

2. 获取计算机上的 VHD 路径。 运行以下 cmdlet：

    ```powershell  
    get-vhd <path-to-your-VHD>
    ```

    该 cmdlet 会返回 VHD 对象并显示属性，例如：
    
    ```powershell  
    ComputerName            : YOURMACHINENAME
    Path                    : <path-to-your-VHD>
    VhdFormat               : VHD
    VhdType                 : Fixed
    FileSize                : 68719477248
    Size                    : 68719476736
    MinimumSize             : 32212254720
    LogicalSectorSize       : 512
    PhysicalSectorSize      : 512
    BlockSize               : 0
    ParentPath              :
    DiskIdentifier          : 3C084D21-652A-4C0E-B2D1-63A8E8E64C0C
    FragmentationPercentage : 0
    Alignment               : 1
    Attached                : False
    DiskNumber              :
    IsPMEMCompatible        : False
    AddressAbstractionType  : None
    Number                  :
    ```

3. 对于 VHD 对象，检查是否满足 Azure Stack Hub 的要求。
    - [VHD 属于固定类型。](#vhd-is-of-fixed-type)
    - [VHD 的最小虚拟大小至少为 20 MB。](#vhd-has-minimum-virtual-size-of-at-least-20-mb)
    - [VHD 已调整。](#vhd-is-aligned)
    - [VHD blob 长度 = 虚拟大小 + vhd 页脚长度 (512)。](#vhd-blob-length) 
    
    此外，Azure Stack Hub 仅支持来自[第一 (1) 代 VM](#generation-one-vms) 的映像。

4. 如果 VHD 与 Azure Stack Hub 不兼容，则需要返回到源映像和 Hyper-V，创建满足要求的 VHD 并上传。 若要在上传过程中最大程度减少可能发生的损坏，请使用 AzCopy。

### <a name="how-to-fix-your-vhd"></a>如何修复 VHD

若要使 VHD 与 Azure Stack Hub 兼容，必须满足以下要求。

#### <a name="vhd-is-of-fixed-type"></a>VHD 属于固定类型
识别：使用 `get-vhd` cmdlet 获取 VHD 对象。  
修复：可以将 VHDX 文件转换为 VHD，将动态扩展磁盘转换为固定大小的磁盘，但无法更改 VM 的代次。
使用 [Hyper-V 管理器或 PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-convert-the-disk) 转换磁盘。

### <a name="vhd-has-minimum-virtual-size-of-at-least-20-mb"></a>VHD 的最小虚拟大小至少为 20 MB
识别：使用 `get-vhd` cmdlet 获取 VHD 对象。  
修复：使用 [Hyper-V 管理器或 PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) 调整磁盘大小。 

### <a name="vhd-is-aligned"></a>VHD 已调整
识别：使用 `get-vhd` cmdlet 获取 VHD 对象。  
修复：虚拟大小必须是 1 MB 的倍数。 

磁盘必须已将虚拟大小调整为 1 MiB。 如果 VHD 的大小不是 1 MiB 的整数倍，需要将磁盘大小调整为 1 MiB 的倍数。 基于上传的 VHD 创建映像时，不到 1 MiB 的磁盘将导致错误。 若要验证该大小，可以使用 PowerShell Get-VHD comdlet 来显示“大小”（在 Azure 中必须是 1 MiB 的倍数），以及“文件大小”（等于“大小”加上 VHD 页脚的 512 字节）。

使用 [Hyper-V 管理器或 PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) 调整磁盘大小。 


### <a name="vhd-blob-length"></a>VHD blob 长度
识别：使用 `get-vhd` cmdlet 显示 `Size`   
修复：VHD blob 长度 = 虚拟大小 + vhd 页脚长度 (512)。 在 Blob 末尾有一小段脚注，描述了 VHD 的属性。 `Size` 在 Azure 中必须是 1 MiB 的倍数，`FileSize` 等于 `Size` + VHD 页脚的 512 字节。

使用 [Hyper-V 管理器或 PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) 调整磁盘大小。 

### <a name="generation-one-vms"></a>第一代 VM
识别：若要确认虚拟机是否为第 1 代虚拟机，请使用 cmdlet `Get-VM | Format-Table Name, Generation`。  
修复：需要在虚拟机监控程序 (Hyper-V) 中重新创建 VM。