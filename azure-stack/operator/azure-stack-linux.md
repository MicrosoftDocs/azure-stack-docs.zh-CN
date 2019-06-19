---
title: 将 Linux 映像添加到 Azure Stack
description: 了解如何将 Linux 映像添加到 Azure Stack。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 8e9617974984afac55c2b1542065ea0455da0acc
ms.sourcegitcommit: 5a720b17bd6a5aab44929c0247db8d512e0669ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67197145"
---
# <a name="add-linux-images-to-azure-stack"></a>将 Linux 映像添加到 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

可以通过将基于 Linux 的映像添加到 Azure Stack 市场来在 Azure Stack 上部署 Linux 虚拟机 (VM)。 将 Linux 映像添加到 Azure Stack 的最简单方法是通过市场管理。 这些映像已准备好，并已针对与 Azure Stack 的兼容性进行测试。

## <a name="marketplace-management"></a>市场管理

若要从 Azure 市场下载 Linux 映像，请使用[将市场项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md) 一文中的过程。 选择要在 Azure Stack 上提供给用户的 Linux 映像。

频繁地更新这些映像，因此请查看 Marketplace 管理通常以保持最新状态。

## <a name="prepare-your-own-image"></a>准备自己的映像

如有可能，下载通过 Marketplace 管理可用的映像。 这些映像已准备好并针对 Azure Stack 进行测试。

### <a name="azure-linux-agent"></a>Azure Linux 代理

Azure Linux 代理 (通常称为`WALinuxAgent`或`walinuxagent`) 是必需的并不是所有版本的 Azure Stack 上的代理工作。 Azure Stack 不支持 2.2.20 和 2.2.35 之间的版本。 若要使用以上 2.2.35 最新的代理版本，应用 1901年修补程序/1902年修补程序，或更新 Azure Stack，到 1903年版本 （或更高版本）。 请注意，目前 Azure Stack 不支持 [cloud-init](https://cloud-init.io/)。

| Azure Stack 内部版本 | Azure Linux 代理内部版本 |
| ------------- | ------------- |
| 1.1901.0.99 或更低版本 | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 或更高版本 |
| 1.1902.2.73  | 2.2.35 或更高版本 |
| 1.1903.0.35  | 2.2.35 或更高版本 |
| 1903 后生成 | 2.2.35 或更高版本 |
| 不支持 | 2.2.21-2.2.34 |

可以按照以下说明准备自己的 Linux 映像：

* [基于 CentOS 的分发版](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES 和 openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-the-marketplace"></a>将映像添加到市场

按照[将映像添加到市场](azure-stack-add-vm-image.md)进行操作。 请确保 `OSType` 参数已设置为 `Linux`。

将映像添加到市场 后，便会创建市场项，用户就可以部署 Linux 虚拟机了。

## <a name="next-steps"></a>后续步骤

* [将市场项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md)
* [Azure Stack 市场概述](azure-stack-marketplace.md)
