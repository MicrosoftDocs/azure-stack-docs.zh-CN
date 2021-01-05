---
title: 将专用 VM 从本地移动到 Azure Stack Hub
description: 了解如何将专用 VM 从本地移动到 Azure Stack Hub。
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: c88ef8ff8cfebc3749a6988d2e5e7f604df63cc0
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872851"
---
# <a name="move-a-specialized-vm-from-on-premises-to-azure-stack-hub"></a>将专用 VM 从本地移动到 Azure Stack Hub

可以从本地环境添加虚拟机 (VM) 映像。 可以创建映像作为虚拟硬盘 (VHD)，并将映像上传到 Azure Stack Hub 实例中的存储帐户。 然后可以从 VHD 创建 VM。

专用磁盘映像是包含原始 VM 中的用户帐户、应用程序和其他状态数据的现有 VM 中虚拟硬盘 (VHD) 的副本。 将 VM 迁移到 Azure Stack Hub 时通常采用此格式。 需要将 VM 从本地迁移到 Azure Stack Hub 时，专用 VHD 非常适合此操作。

## <a name="how-to-move-an-image"></a>如何移动映像

在准备 VHD 时查找特定于你需求的部分。

#### <a name="windows-vm"></a>[Windows VM](#tab/port-win)

- 按照[准备好要上传到 Azure 的 Windows VHD 或 VHDX](/azure/virtual-machines/windows/prepare-for-upload-vhd-image) 中的步骤，正确准备 VHD。 必须对 Azure Stack Hub 使用 VHD。
   > [!NOTE]  
   > **不要** 使用 Sysprep 通用化 VM。
- 删除 VM 上安装的所有来宾虚拟化工具和代理（例如 VMware 工具）。
- 确保 VM 配置为从 DHCP 获取 IP 地址和 DNS 设置。 这可以确保服务器在启动时获得虚拟网络中的 IP 地址。
- 确保 RDP/SSH 已启用并且防火墙允许通信。
- 若要部署 VM 扩展，请确保 VM 代理 `.msi` 可用。 有关指导，请参阅 [Azure 虚拟机代理概述](/azure/virtual-machines/extensions/agent-windows)。 如果 VHD 中不存在 VM 代理，扩展部署将失败。 预配时无需设置 OS 配置文件或设置 `$vm.OSProfile.AllowExtensionOperations = $true`。

#### <a name="linux-vm"></a>[Linux VM](#tab/port-linux)

#### <a name="generalize-the-vhd"></a>通用化 VHD

按照相应的说明，为 Linux OS 准备好 VHD：

- [基于 CentOS 的分发版](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](../operator/azure-stack-redhat-create-upload-vhd.md)
- [SLES 或 openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

> [!IMPORTANT]
> 请勿运行最后一步：(`sudo waagent -force -deprovision`)，因为这将通用化 VHD。

#### <a name="identify-the-version-of-the-linux-agent"></a>确定 Linux 代理的版本

确定在源 VM 映像中安装的 Linux 代理版本，运行以下命令。 描述预配代码的版本号是 `WALinuxAgent-`，而不是 `Goal state agent`：

   ```bash  
   waagent -version
   ```
    
   例如：
    
   ```bash  
   waagent -version
   WALinuxAgent-2.2.45 running on centos 7.7.1908
   Python: 2.7.5
   Goal state agent: 2.2.46
   ```

#### <a name="linux-agent-224-and-earlier-disable-the-linux-agent-provisioning"></a>Linux 代理 2.2.4 和更早版本，禁用 Linux 代理预配 

禁用 Linux 代理版本低于 2.2.4 的 Linux 代理预配，在 /etc/waagent.conf 中设置以下参数：`Provisioning.Enabled=n, and Provisioning.UseCloudInit=n`。

#### <a name="linux-agent-2245-and-later-disable-the-linux-agent-provisioning"></a>Linux 代理 2.2.45 和更高版本，禁用 Linux 代理预配

若要禁用 Linux 代理版本为 2.2.45 及更高版本的预配，请更改以下配置选项：

现在已忽略 `Provisioning.Enabled` 和 `Provisioning.UseCloudInit`。

在此版本中，当前没有完全禁用预配的 `Provisioning.Agent` 选项；但是，你可以添加预配标记文件，并进行以下设置来忽略预配：

1. 在 /etc/waagent.conf 中添加此配置选项：`Provisioning.Agent=Auto`。
2. 若要确保禁用 walinuxagent 预配，请运行：`mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned`。
3. 运行以下命令，禁用 cloud-init 安装：

   ```bash  
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. 注销。

#### <a name="run-an-extension"></a>运行扩展

1. 在 /etc/waagent.conf 中设置以下参数：

   - `Provisioning.Enabled=n`
   - `Provisioning.UseCloudInit=n`

2. 若要确保禁用 walinuxagent 预配，请运行：`mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned`

3. 如果映像中有 cloud-init，请禁用云 init：

    ```bash  
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. 执行注销。

---

## <a name="verify-your-vhd"></a>验证 VHD

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]

## <a name="upload-to-a-storage-account"></a>上传到存储帐户

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-disk-in-azure-stack-hub"></a>在 Azure Stack Hub 中创建磁盘

[!INCLUDE [Create the disk in Azure Stack Hub](../includes/user-compute-create-disk.md)]

## <a name="next-steps"></a>后续步骤

[将 VM 移动到 Azure Stack Hub 概述](vm-move-overview.md)
