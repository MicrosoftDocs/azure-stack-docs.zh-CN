---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 4c0e9fd823b7b932ec11c61e24ce650df1cf9cc9
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91936013"
---
上载 VHD 之前，必须验证 VHD 是否满足要求。 不符合要求的 Vhd 将无法在 Azure Stack 集线器中加载。

1. 你将使用 Hyper-v 提供的 PowerShell 模块。 激活支持 PowerShell 模块的 Hyper-v 安装。 可以通过使用提升的提示符打开 PowerShell 并运行以下 cmdlet 来检查该模块：

    ```powershell  
    Get-Command -Module hyper-v
    ```

    如果你没有 Hyper-v 命令，请参阅使用 [hyper-v 和 Windows PowerShell](/virtualization/hyper-v-on-windows/quick-start/try-hyper-v-powershell)。 

2. 获取计算机上 VHD 的路径。 运行以下 cmdlet：

    ```powershell  
    get-vhd <path-to-your-VHD>
    ```

    该 cmdlet 将返回 VHD 对象并显示属性，例如：
    
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

3. 对于 VHD 对象，请检查是否满足 Azure Stack 集线器的要求。
    - [VHD 的类型为固定。](#vhd-is-of-fixed-type)
    - [VHD 的最小虚拟大小至少为 20 MB。](#vhd-has-minimum-virtual-size-of-at-least-20-mb)
    - [VHD 已对齐。](#vhd-is-aligned)
    - [VHD blob 长度 = 虚拟大小 + vhd 脚注长度 (512) 。](#vhd-blob-length) 
    
    此外，Azure Stack 集线器仅支持[第1代 () vm](#generation-one-vms)中的映像。

4. 如果 VHD 与 Azure Stack 集线器不兼容，则需要返回到源映像和 Hyper-v，创建满足要求的 VHD，并上传。 若要最大程度地减少上载过程中可能发生的损坏，请使用 AzCopy。

### <a name="how-to-fix-your-vhd"></a>如何修复 VHD

要使 VHD 与 Azure Stack 中心兼容，必须满足以下要求。

#### <a name="vhd-is-of-fixed-type"></a>VHD 的类型为固定
**标识**：使用 `get-vhd` cmdlet 获取 VHD 对象。  
**修复**：可以将 VHDX 文件转换为 VHD，将动态扩展磁盘转换为固定大小的磁盘，但无法更改 VM 的代。
使用 [Hyper-v 管理器或 PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-convert-the-disk) 来转换磁盘。

### <a name="vhd-has-minimum-virtual-size-of-at-least-20-mb"></a>VHD 的最小虚拟大小至少为 20 MB
**标识**：使用 `get-vhd` cmdlet 获取 VHD 对象。  
**修复**：使用 [hyper-v 管理器或 PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) 调整磁盘大小。 

### <a name="vhd-is-aligned"></a>VHD 已对齐
**标识**：使用 `get-vhd` cmdlet 获取 VHD 对象。  
**修复**：虚拟大小必须是一 (1) MB 的倍数。 

磁盘必须将虚拟大小调整为 1 MiB。 如果 VHD 是 1 MiB 的一部分，则需要将磁盘大小调整为 1 MiB 的倍数。 基于上传的 VHD 创建映像时，不到 1 MiB 的磁盘将导致错误。 若要验证大小，你可以使用 PowerShell 获取 VHD cmdlet 显示 "大小"，该大小必须是 Azure 中 1 MiB 的倍数，"FileSize" 将等于 VHD 页脚的 "Size" 加上512字节。

使用 [Hyper-v 管理器或 PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) 调整磁盘大小。 


### <a name="vhd-blob-length"></a>VHD blob 长度
**标识**：使用 `get-vhd` cmdlet 显示 `Size`   
**修复**： VHD blob 长度 = 虚拟大小 + vhd 脚注长度 (512) 。 在 Blob 末尾有一小段脚注，描述了 VHD 的属性。 `Size` 在 Azure 中必须是 1 MiB 的倍数，并且 `FileSize` 对于 VHD 页脚，它将等于 `Size` + 512 字节。

使用 [Hyper-v 管理器或 PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) 调整磁盘大小。 

### <a name="generation-one-vms"></a>生成一个 Vm
**确定**：若要确认虚拟机是否为第1代虚拟机，请使用 cmdlet `Get-VM | Format-Table Name, Generation` 。  
**修复**：需要在虚拟机监控程序 (hyper-v) 中重新创建 VM。