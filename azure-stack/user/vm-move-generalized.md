---
title: 将通用化 VM 从本地移动到 Azure Stack Hub
description: 了解如何将通用化 VM 从本地移动到 Azure Stack Hub。
author: mattbriggs
ms.topic: how-to
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 8e26ed6e67fd8ad6269c95b22e4f2e1879b96300
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247857"
---
# <a name="move-a-generalized-vm-from-on-premises-to-azure-stack-hub"></a>将通用化 VM 从本地移动到 Azure Stack Hub

可以从本地环境添加虚拟机 (VM) 映像。 可以创建映像作为虚拟硬盘 (VHD)，并将映像上传到 Azure Stack Hub 实例中的存储帐户。 然后可以从 VHD 创建 VM。

通用磁盘映像是使用 Sysprep 准备的用于删除任何唯一信息（如用户帐户）的映像，从而使它能够重复用于创建多个 VM。 在创建 Azure Stack Hub 云操作员规划用作市场项的映像时，通用 VHD 非常合适。

## <a name="how-to-move-an-image"></a>如何移动映像

在准备 VHD 时查找特定于你需求的部分。

#### <a name="windows-vm"></a>[Windows VM](#tab/port-win)

按照[准备好要上传到 Azure 的 Windows VHD 或 VHDX](/azure/virtual-machines/windows/prepare-for-upload-vhd-image)中的步骤，在上传之前正确通用化 VHD。 必须对 Azure Stack Hub 使用 VHD。

#### <a name="linux-vm"></a>[Linux VM](#tab/port-linux)

按照相应的说明，为 Linux OS 使 VHD 通用化：

- [基于 CentOS 的分发版](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](../operator/azure-stack-redhat-create-upload-vhd.md)
- [SLES 或 openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

---

## <a name="verify-your-vhd"></a>验证 VHD

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]
## <a name="upload-to-a-storage-account"></a>上传到存储帐户

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-image-in-azure-stack-hub"></a>在 Azure Stack Hub 中创建映像

[!INCLUDE [Create the image in Azure Stack Hub](../includes/user-compute-create-image.md)]

## <a name="next-steps"></a>后续步骤

[将 VM 移动到 Azure Stack Hub 概述](vm-move-overview.md)
