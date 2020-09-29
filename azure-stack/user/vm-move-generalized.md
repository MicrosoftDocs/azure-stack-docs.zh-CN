---
title: 将通用化 VM 从本地移到 Azure Stack 中心
description: 了解如何将通用化 VM 从本地移到 Azure Stack 中心。
author: mattbriggs
ms.topic: how-to
ms.date: 9/8/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: babc5888ea7ce26c031765dd3f3a61a2e0e7f740
ms.sourcegitcommit: 9a340b383dcf42c85bc6ec0d01ff3c9ae29dfe4c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2020
ms.locfileid: "89609754"
---
# <a name="move-a-generalized-vm-from-on-premises-to-azure-stack-hub"></a>将通用化 VM 从本地移到 Azure Stack 中心

可以从本地环境中添加虚拟机 (VM) 映像。 可以 (VHD) 创建映像，并将映像上传到 Azure Stack 中心实例中的存储帐户。 然后，可以从 VHD 创建 VM。

通用磁盘映像是使用 Sysprep 准备的用于删除任何唯一信息（如用户帐户）的映像，从而使它能够重复用于创建多个 VM。 通用 Vhd 适用于创建 Azure Stack 中心云操作员计划用作 marketplace 项的映像的时间。

## <a name="how-to-move-an-image"></a>如何移动图像

查找在准备 VHD 时特定于你的需求的部分。

#### <a name="windows-vm"></a>[Windows VM](#tab/port-win)

按照[准备好要上传到 Azure 的 Windows VHD 或 VHDX](/azure/virtual-machines/windows/prepare-for-upload-vhd-image)中的步骤，在上传之前正确通用化 VHD。 您必须为 Azure Stack 集线器使用 VHD。

#### <a name="linux-vm"></a>[Linux VM](#tab/port-linux)

按照相应的说明通用化 Linux OS 的 VHD：

- [基于 CentOS 的分发版](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](../operator/azure-stack-redhat-create-upload-vhd.md)
- [SLES 或 openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

---

## <a name="verify-your-vhd"></a>验证你的 VHD

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]
## <a name="upload-to-a-storage-account"></a>上传到存储帐户

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-image-in-azure-stack-hub"></a>在 Azure Stack 集线器中创建映像

[!INCLUDE [Create the image in Azure Stack Hub](../includes/user-compute-create-image.md)]

## <a name="next-steps"></a>后续步骤

[将 VM 移到 Azure Stack 集线器概述](vm-move-overview.md)
