---
title: 为 Azure Stack 中心准备基于 Red Hat 的虚拟机 |Microsoft Docs
titleSuffix: Azure Stack Hub
description: 了解如何创建和上传包含 Red Hat Linux 操作系统的 Azure 虚拟硬盘 (VHD)。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 12/11/2019
ms.openlocfilehash: 4a3b79a8b1b58ad3da4abf9d5a59d750aaeae0ec
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75809757"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack-hub"></a>为 Azure Stack 中心准备基于 Red Hat 的虚拟机

本文介绍如何准备 Red Hat Enterprise Linux （RHEL）虚拟机（VM）以在 Azure Stack 中心中使用。 本文中所述的 RHEL 版本为 7.1 +。 本文所述的用于准备工作的虚拟机监控程序为 Hyper-V、基于内核的虚拟机 (KVM) 和 VMware。

有关 Red Hat Enterprise Linux 支持信息，请参阅[Red Hat 和 Azure Stack 中心：常见问题解答](https://access.redhat.com/articles/3413531)。

## <a name="prepare-a-red-hat-based-vm-from-hyper-v-manager"></a>从 Hyper-v 管理器准备基于 Red Hat 的 VM

本部分假定你已拥有 Red Hat 网站的 ISO 文件，并已将 RHEL 映像安装到虚拟硬盘（VHD）。 有关如何使用 Hyper-v 管理器来安装操作系统映像的详细信息，请参阅[安装 Hyper-v 角色和配置 VM](https://technet.microsoft.com/library/hh846766.aspx)。

### <a name="rhel-installation-notes"></a>RHEL 安装说明

* Azure Stack 中心不支持 VHDX 格式。 Azure 仅支持固定 VHD。 可使用 Hyper-V 管理器将磁盘转换为 VHD 格式，也可以使用 convert-vhd cmdlet。 如果使用 VirtualBox，则选择“固定大小”，而不是在创建磁盘时默认动态分配选项。
* Azure Stack 集线器仅支持第1代 Vm。 可以将第1代 VM 从 VHDX 转换为 VHD 文件格式，并从动态扩展为固定大小磁盘。 无法更改 VM 的代。 有关详细信息，请参阅是否[应在 hyper-v 中创建第1代或第2代 VM？](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* VHD 允许的最大大小为 1,023 GB。
* 在安装 Linux 操作系统时，建议使用标准分区而不是逻辑卷管理器 (LVM)（通常是许多安装的默认设置）。 这种做法可以避免 LVM 名称与克隆的虚拟机冲突，尤其是当需要将操作系统磁盘附加到另一个相同的 VM 进行故障排除时。
* 需要装载通用磁盘格式 (UDF) 文件系统的内核支持。 首次启动时，附加到来宾的 UDF 格式的媒体会将预配配置传递到 Linux VM。 Azure Linux 代理必须安装 UDF 文件系统才能读取其配置和预配 VM。
* 不要在操作系统磁盘上配置交换分区。 可以配置 Linux 代理，并在临时资源磁盘上创建交换文件。 有关详细信息，请参阅以下步骤。
* Azure 上的所有 VHD 必须已将虚拟大小调整为 1 MB。 将原始磁盘转换为 VHD 时，必须确保原始磁盘大小为 1 MB 的倍数，然后转换。 可以在以下步骤中找到更多详细信息。
* Azure Stack 中心支持 cloud init。 [Cloud-init](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) 是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 安装程序包和写入文件，或者配置用户和安全。 由于是在初始启动过程中调用 cloud-init，因此无需额外的步骤且无需代理来应用配置。 有关将云初始化添加到映像的说明，请参阅[准备要用于云初始化的现有 Linux AZURE VM 映像](https://docs.microsoft.com/azure/virtual-machines/linux/cloudinit-prepare-custom-image)。

### <a name="prepare-an-rhel-7-vm-from-hyper-v-manager"></a>从 Hyper-v 管理器准备 RHEL 7 VM

1. 在 "Hyper-v 管理器" 中，选择 VM。

1. 选择 "**连接**"，打开 VM 的控制台窗口。

1. 创建或编辑 `/etc/sysconfig/network` 文件并添加以下文本：

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. 创建或编辑 `/etc/sysconfig/network-scripts/ifcfg-eth0` 文件，并根据需要添加以下文本：

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. 通过运行以下命令，确保网络服务在启动时启动：

    ```bash
    sudo systemctl enable network
    ```

1. 注册 Red Hat 订阅，以通过运行以下命令来启用来自 RHEL 存储库中的包的安装：

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. 在 grub 配置中修改内核引导行，以使其包含 Azure 的其他内核参数。 若要进行此修改，请在文本编辑器中打开 `/etc/default/grub`，并修改 `GRUB_CMDLINE_LINUX` 参数。 例如：

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   此修改可确保所有控制台消息都发送到第一个串行端口，从而可以协助 Azure 支持人员调试问题。 此配置还会关闭 NIC 的新 RHEL 7 命名约定。

   在需要将所有日志发送到串行端口的云环境中，图形和静默启动并不有用。 如果需要，可以保留配置的 `crashkernel` 选项。 此参数将 VM 中的可用内存量减少 128 MB 或更大，这可能会在较小的 VM 大小上出现问题。 建议删除以下参数：

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. 完成 `/etc/default/grub`的编辑后，运行以下命令以重新生成 grub 配置：

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. [1910 在版发布后可选]停止并卸载云初始化：

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. 请确保 SSH 服务器已安装且已配置为在引导时启动（默认采用此配置）。 修改 `/etc/ssh/sshd_config` 以包含以下行：

    ```sh
    ClientAliveInterval 180
    ```

1. 为 Azure Stack 中心创建自定义 vhd 时，请记住，在1910版本之前，2.2.20 和2.2.35 之间的 WALinuxAgent 版本（两者均为独占）在 Azure Stack 中心环境中不起作用。 你可以使用版本 2.2.20/2.2.35 版本来准备映像。 若要使用上述版本的2.2.35 来准备自定义映像，请将 Azure Stack 集线器更新为1903版本及更高版本或应用1901/1902 修补程序。

    [1910 版之前]按照以下说明下载兼容的 WALinuxAgent：

    1. 下载 setuptools。

    ```bash
    wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
    tar xzf setuptools-7.0.tar.gz
    cd setuptools-7.0
    ```

    1. 从 GitHub 下载并解压缩代理的2.2.20 版本。

    ```bash
    wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
    unzip v2.2.20.zip
    cd WALinuxAgent-2.2.20
    ```

    1. 安装 setup.py。

    ```bash
    sudo python setup.py install
    ```

    1. 重新启动 waagent。

    ```bash
    sudo systemctl restart waagent
    ```

    1. 测试代理版本是否与下载的版本匹配。 对于本示例，应为2.2.20。

    ```bash
    waagent -version
    ```
    
    [1910 发布后]按照以下说明下载兼容的 WALinuxAgent：
    
    1. WALinuxAgent 包 `WALinuxAgent-<version>` 已推送到 Red Hat extras 存储库。 通过运行以下命令启用 extras 存储库：

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

    1. 通过运行以下命令安装 Azure Linux 代理：

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```


1. 不要在操作系统磁盘上创建交换空间。

    Azure Linux 代理可使用在 Azure 上预配 VM 后附加到 VM 的本地资源磁盘自动配置交换空间。 本地资源磁盘是临时磁盘，并且在取消预配 VM 时可能会被清空。 在上一步中安装 Azure Linux 代理后，相应地在 `/etc/waagent.conf` 中修改以下参数：

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. 如果想要取消注册订阅，运行以下命令：

    ```bash
    sudo subscription-manager unregister
    ```

1. 如果你使用的是使用企业证书颁发机构部署的系统，RHEL VM 将不信任 Azure Stack 中心根证书。 需要将其放入受信任的根存储中。 有关详细信息，请参阅[将受信任的根证书添加到服务器](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html)。

1. 运行以下命令以取消预配 VM，并为其准备好在 Azure 上进行预配：

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. 选择 "**操作**" > **关闭**hyper-v 管理器。

1. 使用 Hyper-v 管理器 "编辑磁盘" 功能或 "转换-VHD PowerShell" 命令将 VHD 转换为固定大小的 VHD。 Linux VHD 现已准备好上传到 Azure。

## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>从 KVM 准备基于 Red Hat 的虚拟机

1. 从 Red Hat 网站上下载 RHEL 7 的 KVM 映像。 此过程以 RHEL 7 为例。

1. 设置 root 密码。

    生成加密密码，并复制命令的输出：

    ```bash
    openssl passwd -1 changeme
    ```

   使用 guestfish 设置 root 密码：

    ```sh
    guestfish --rw -a <image-name>
    > <fs> run
    > <fs> list-filesystems
    > <fs> mount /dev/sda1 /
    > <fs> vi /etc/shadow
    > <fs> exit
    ```

   将 root 用户的第二个字段从“!!”更改 为加密密码。

1. 从 qcow2 映像创建 KVM 中的 VM。 将磁盘类型设置为 **qcow2**，将虚拟网络接口设备型号设置为 **virtio**。 然后，启动 VM，并以 root 身份登录。

1. 创建或编辑 `/etc/sysconfig/network` 文件并添加以下文本：

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. 创建或编辑 `/etc/sysconfig/network-scripts/ifcfg-eth0` 文件并添加以下文本：

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. 通过运行以下命令，确保网络服务在启动时启动：

    ```bash
    sudo systemctl enable network
    ```

1. 注册 Red Hat 订阅，以通过运行以下命令来启用来自 RHEL 存储库中的包的安装：

    ```bash
    subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. 在 grub 配置中修改内核引导行，以使其包含 Azure 的其他内核参数。 若要执行此配置，请在文本编辑器中打开 `/etc/default/grub`，并修改 `GRUB_CMDLINE_LINUX` 参数。 例如：

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   此命令还将确保所有控制台消息都发送到第一个串行端口，从而可以协助 Azure 支持人员调试问题。 此命令还会关闭 NIC 的新 RHEL 7 命名约定。

   在将所有日志都发送到串行端口的云环境中，图形和静默启动并不有用。 如果需要，可以保留配置的 `crashkernel` 选项。 此参数将 VM 中的可用内存量减少 128 MB 或更大，这可能会在较小的 VM 大小上出现问题。 建议删除以下参数：

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. 完成 `/etc/default/grub`的编辑后，运行以下命令以重新生成 grub 配置：

    ```bash
    grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. 将 Hyper-V 模块添加到 initramfs 中。

    编辑 `/etc/dracut.conf` 并添加以下内容：

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    重新生成 initramfs：

    ```bash
    dracut -f -v
    ```

1. [1910 在版发布后可选]停止并卸载云初始化：

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. 确保已安装 SSH 服务器且已将其配置为在引导时启动。

    ```bash
    systemctl enable sshd
    ```

    修改 /etc/ssh/sshd_config 以包含以下行：

    ```sh
    PasswordAuthentication yes
    ClientAliveInterval 180
    ```

1. 为 Azure Stack 中心创建自定义 vhd 时，请记住，在1910版本之前，2.2.20 和2.2.35 之间的 WALinuxAgent 版本（两者均为独占）在 Azure Stack 中心环境中不起作用。 你可以使用版本 2.2.20/2.2.35 版本来准备映像。 若要使用上述版本的2.2.35 来准备自定义映像，请将 Azure Stack 集线器更新为1903版本及更高版本或应用1901/1902 修补程序。

    [1910 版之前]按照以下说明下载兼容的 WALinuxAgent：

    1. 下载 setuptools。

    ```bash
    wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
    tar xzf setuptools-7.0.tar.gz
    cd setuptools-7.0
    ```

    1. 从 GitHub 下载并解压缩代理的2.2.20 版本。

    ```bash
    wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
    unzip v2.2.20.zip
    cd WALinuxAgent-2.2.20
    ```

    1. 安装 setup.py。

    ```bash
    sudo python setup.py install
    ```

    1. 重新启动 waagent。

    ```bash
    sudo systemctl restart waagent
    ```

    1. 测试代理版本是否与下载的版本匹配。 对于本示例，应为2.2.20。

    ```bash
    waagent -version
    ```
    
    [1910 发布后]按照以下说明下载兼容的 WALinuxAgent：
    
    1. WALinuxAgent 包 `WALinuxAgent-<version>` 已推送到 Red Hat extras 存储库。 通过运行以下命令启用 extras 存储库：

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

    1. 通过运行以下命令安装 Azure Linux 代理：

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```

1. 不要在操作系统磁盘上创建交换空间。

    Azure Linux 代理可使用在 Azure 上预配 VM 后附加到 VM 的本地资源磁盘自动配置交换空间。 本地资源磁盘是临时磁盘，并且在取消预配 VM 时可能会被清空。 在上一步中安装 Azure Linux 代理后，相应地在 `/etc/waagent.conf` 中修改以下参数：

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    #NOTE: set this to whatever you need it to be.
    ```

1. 通过运行以下命令取消注册订阅（如有必要）：

    ```bash
    subscription-manager unregister
    ```

1. 如果你使用的是使用企业证书颁发机构部署的系统，RHEL VM 将不信任 Azure Stack 中心根证书。 需要将其放入受信任的根存储中。 有关详细信息，请参阅[将受信任的根证书添加到服务器](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html)。

1. 运行以下命令以取消预配 VM，并为其准备好在 Azure 上进行预配：

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. 关闭 KVM 中的 VM。

1. 将 qcow2 映像转换为 VHD 格式。

    > [!NOTE]
    > Qemu-img 版本中有一个已知 bug > = 2.2.1，导致 VHD 格式不正确。 QEMU 2.6 中已修复此问题。 建议使用 qemu-img-img 2.2.0 或更低版本，或者更新到2.6 或更高版本。 参考： https://bugs.launchpad.net/qemu/+bug/1490611 。

    首先将此映像转换为原始格式：

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    请确保原始映像大小为 1 MB。 如果不是，请将大小四舍五入，使其等于 1 MB：

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    将原始磁盘转换为固定大小的 VHD：

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    或者，对于 qemu-img 版本**2.6 +** ，请包含 `force_size` 选项：

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-vmware"></a>从 VMware 准备基于 Red Hat 的虚拟机

本部分假设已在 VMware 中安装了 RHEL VM。 有关如何在 VMware 中安装操作系统的详细信息，请参阅 [VMware 来宾操作系统安装指南](https://partnerweb.vmware.com/GOSIG/home.html)。

* 在安装 Linux 操作系统时，建议使用标准分区而不是 LVM，这通常是许多安装的默认设置。 此方法可避免 LVM 名称与克隆的虚拟机冲突，尤其是在需要将操作系统磁盘附加到另一个 VM 进行故障排除时。 如果需要，可以在数据磁盘上使用 LVM 或 RAID。
* 不要在操作系统磁盘上配置交换分区。 可将 Linux 代理配置为在临时资源磁盘上创建交换文件。 可以在下面的步骤中找到有关此配置的详细信息。
* 创建虚拟硬盘时，选择“将虚拟磁盘存储为单个文件”。

### <a name="prepare-an-rhel-7-vm-from-vmware"></a>从 VMware 准备 RHEL 7 VM

1. 创建或编辑 `/etc/sysconfig/network` 文件并添加以下文本：

    ```sh
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

1. 创建或编辑 `/etc/sysconfig/network-scripts/ifcfg-eth0` 文件并添加以下文本：

    ```sh
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

1. 通过运行以下命令，确保网络服务会在引导时启动：

    ```bash
    sudo chkconfig network on
    ```

1. 注册 Red Hat 订阅，以通过运行以下命令来启用来自 RHEL 存储库中的包的安装：

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. 在 grub 配置中修改内核引导行，以使其包含 Azure 的其他内核参数。 若要进行此修改，请在文本编辑器中打开 `/etc/default/grub`，并修改 `GRUB_CMDLINE_LINUX` 参数。 例如：

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    此配置还将确保所有控制台消息都发送到第一个串行端口，从而可以协助 Azure 支持人员调试问题。 此外，还会关闭 NIC 的新 RHEL 7 命名约定。 建议删除以下参数：

    ```sh
    rhgb quiet crashkernel=auto
    ```

    在需要将所有日志发送到串行端口的云环境中，图形和静默启动并不有用。 如果需要，可以保留配置的 `crashkernel` 选项。 此参数将 VM 中的可用内存量减少 128 MB 或更大，这可能会在较小的 VM 大小上出现问题。

1. 完成 `/etc/default/grub`的编辑后，运行以下命令以重新生成 grub 配置：

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. 将 Hyper-V 模块添加到 initramfs 中。

    编辑 `/etc/dracut.conf`，添加内容：

    ```sh
    add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
    ```

    重新生成 initramfs：

    ```bash
    dracut -f -v
    ```

1. [1910 在版发布后可选]停止并卸载云初始化：

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. 请确保已安装 SSH 服务器且已将其配置为在引导时启动。 此设置通常是默认设置。 修改 `/etc/ssh/sshd_config` 以包含以下行：

    ```sh
    ClientAliveInterval 180
    ```

1. 为 Azure Stack 中心创建自定义 vhd 时，请记住，在1910版本之前，2.2.20 和2.2.35 之间的 WALinuxAgent 版本（两者均为独占）在 Azure Stack 中心环境中不起作用。 你可以使用版本 2.2.20/2.2.35 版本来准备映像。 若要使用上述版本的2.2.35 来准备自定义映像，请将 Azure Stack 集线器更新为1903版本及更高版本或应用1901/1902 修补程序。

    [1910 版之前]按照以下说明下载兼容的 WALinuxAgent：

    1. 下载 setuptools。

    ```bash
    wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
    tar xzf setuptools-7.0.tar.gz
    cd setuptools-7.0
    ```

    1. 从 GitHub 下载并解压缩代理的2.2.20 版本。

    ```bash
    wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
    unzip v2.2.20.zip
    cd WALinuxAgent-2.2.20
    ```

    1. 安装 setup.py。

    ```bash
    sudo python setup.py install
    ```

    1. 重新启动 waagent。

    ```bash
    sudo systemctl restart waagent
    ```

    1. 测试代理版本是否与下载的版本匹配。 对于本示例，应为2.2.20。

    ```bash
    waagent -version
    ```
    
    [1910 发布后]按照以下说明下载兼容的 WALinuxAgent：
    
    1. WALinuxAgent 包 `WALinuxAgent-<version>` 已推送到 Red Hat extras 存储库。 通过运行以下命令启用 extras 存储库：

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

    1. 通过运行以下命令安装 Azure Linux 代理：

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```

1. 不要在操作系统磁盘上创建交换空间。

    Azure Linux 代理可使用在 Azure 上预配 VM 后附加到 VM 的本地资源磁盘自动配置交换空间。 请注意，本地资源磁盘是临时磁盘，并可能在取消预配 VM 时被清空。 在上一步中安装 Azure Linux 代理后，相应地在 `/etc/waagent.conf` 中修改以下参数：

    ```sh
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    NOTE: set this to whatever you need it to be.
    ```

1. 如果想要取消注册订阅，运行以下命令：

    ```bash
    sudo subscription-manager unregister
    ```

1. 如果你使用的是使用企业证书颁发机构部署的系统，RHEL VM 将不信任 Azure Stack 中心根证书。 需要将其放入受信任的根存储中。 有关详细信息，请参阅[将受信任的根证书添加到服务器](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html)。

1. 运行以下命令以取消预配 VM，并为其准备好在 Azure 上进行预配：

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. 关闭 VM，并将 VMDK 文件转换为 VHD 格式。

    > [!NOTE]
    > Qemu-img 版本中有一个已知 bug > = 2.2.1，导致 VHD 格式不正确。 QEMU 2.6 中已修复此问题。 建议使用 qemu-img-img 2.2.0 或更低版本，或者更新到2.6 或更高版本。 参考： <https://bugs.launchpad.net/qemu/+bug/1490611>。

    首先将此映像转换为原始格式：

    ```bash
    qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw
    ```

    请确保原始映像大小为 1 MB。 如果不是，请将大小四舍五入，使其等于 1 MB：

    ```bash
    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
    rounded_size=$((($size/$MB + 1)*$MB))
    qemu-img resize rhel-7.4.raw $rounded_size
    ```

    将原始磁盘转换为固定大小的 VHD：

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

    或者，对于 qemu-img 版本**2.6 +** ，请包含 `force_size` 选项：

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-vm-from-an-iso-by-using-a-kickstart-file-automatically"></a>使用 kickstart 文件自动从 ISO 准备基于 Red Hat 的 VM

1. 创建包括以下内容的 kickstart 文件，并保存该文件。 停止和卸载 cloud init 是可选的（Azure Stack 集线器 post 1910 版支持云初始化）。 仅在1910版本后，从 redhat 存储库安装代理。 在1910之前，请使用上一部分中的 Azure 存储库。 有关 kickstart 安装的详细信息，请参阅 [Kickstart 安装指南](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)。

    ```sh
    Kickstart for provisioning a RHEL 7 Azure VM

    System authorization information
    auth --enableshadow --passalgo=sha512

    Use graphical install
    text

    Do not run the Setup Agent on first boot
    firstboot --disable

    Keyboard layouts
    keyboard --vckeymap=us --xlayouts='us'

    System language
    lang en_US.UTF-8

    Network information
    network  --bootproto=dhcp

    Root password
    rootpw --plaintext "to_be_disabled"

    System services
    services --enabled="sshd,waagent,NetworkManager"

    System timezone
    timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

    Partition clearing information
    clearpart --all --initlabel

    Clear the MBR
    zerombr

    Disk partitioning information
    part /boot --fstype="xfs" --size=500
    part / --fstyp="xfs" --size=1 --grow --asprimary

    System bootloader configuration
    bootloader --location=mbr

    Firewall configuration
    firewall --disabled

    Enable SELinux
    selinux --enforcing

    Don't configure X
    skipx

    Power down the machine after install
    poweroff

    %packages
    @base
    @console-internet
    chrony
    sudo
    parted
    -dracut-config-rescue

    %end

    %post --log=/var/log/anaconda/post-install.log

    #!/bin/bash

    Register Red Hat Subscription
    subscription-manager register --username=XXX --password=XXX --auto-attach --force

    Install latest repo update
    yum update -y

    Stop and Uninstall cloud-init
    systemctl stop cloud-init
    yum remove cloud-init
    
    Enable extras repo
    subscription-manager repos --enable=rhel-7-server-extras-rpms

    Install WALinuxAgent
    yum install -y WALinuxAgent

    Unregister Red Hat subscription
    subscription-manager unregister

    Enable waaagent at boot-up
    systemctl enable waagent

    Disable the root account
    usermod root -p '!!'

    Configure swap in WALinuxAgent
    sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
    sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

    Set the cmdline
    sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

    Enable SSH keepalive
    sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

    Build the grub cfg
    grub2-mkconfig -o /boot/grub2/grub.cfg

    Configure network
    cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    EOF

    Deprovision and prepare for Azure
    waagent -force -deprovision

    %end
    ```

1. 将 kickstart 文件放在安装系统可以访问的位置。

1. 在 Hyper-V 管理器中，创建新的 VM。 在“连接虚拟硬盘”页上，选择“稍后附加虚拟硬盘”，并完成新建虚拟机向导。

1. 打开 VM 设置：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 将新的虚拟硬盘附加到 VM。 请务必选择“VHD 格式”和“固定大小”。

    b.保留“数据库类型”设置，即设置为“共享”。 将安装 ISO 附加到 DVD 光驱。

    c. 将 BIOS 设置为从 CD 启动。

1. 启动 VM。 当安装指南出现时，请按 **Tab** 键来配置启动选项。

1. 在启动选项的末尾输入 `inst.ks=<the location of the kickstart file>`，并按 **Enter**。

1. 等待安装完成。 完成后，VM 会自动关闭。 Linux VHD 现已准备好上传到 Azure。

## <a name="known-issues"></a>已知问题

### <a name="the-hyper-v-driver-couldnt-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>使用非 Hyper-v 虚拟机监控程序时，无法在初始 RAM 磁盘中包含 Hyper-v 驱动程序

在某些情况下，Linux 安装程序可能不会在初始 RAM 磁盘（initrd 或 initramfs）中包含 Hyper-v 驱动程序，除非 Linux 检测到它正在 Hyper-v 环境中运行。

当使用不同的虚拟化系统（如 Oracle VM VirtualBox、Xen 项目等）来准备 Linux 映像时，可能需要重新生成 initrd 以确保至少 hv_vmbus 和 hv_storvsc 内核模块在初始 RAM 上可用磁盘. 至少在基于上游 Red Hat 分发的系统上这是一个已知问题。

要解决此问题，请将 Hyper-V 模块添加到 initramfs 并进行重新生成：

编辑 `/etc/dracut.conf` 并添加以下内容：

```sh
add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
```

重新生成 initramfs：

```bash
dracut -f -v
```

有关详细信息，请参阅重新[生成 initramfs](https://access.redhat.com/solutions/1958)。

## <a name="next-steps"></a>后续步骤

你现在已准备好使用 Red Hat Enterprise Linux 虚拟硬盘在 Azure Stack 中心创建新的 Vm。 如果这是你第一次将 VHD 文件上载到 Azure Stack 中心，请参阅[创建和发布 Marketplace 项](azure-stack-create-and-publish-marketplace-item.md)。

有关已通过认证以运行 Red Hat Enterprise Linux 的虚拟机监控程序的详细信息，请参阅[Red Hat 网站](https://access.redhat.com/certified-hypervisors)。
