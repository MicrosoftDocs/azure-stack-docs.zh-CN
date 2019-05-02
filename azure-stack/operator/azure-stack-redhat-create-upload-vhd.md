---
title: 创建并上传 Red Hat Enterprise Linux VHD，以供在 Azure Stack 中使用 | Microsoft Docs
description: 了解如何创建和上传包含 Red Hat Linux 操作系统的 Azure 虚拟硬盘 (VHD)。
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 04/15/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: jeffgo
ms.lastreviewed: 08/15/2018
ms.openlocfilehash: 636ac38785f2604b97ee5bf1cfc3615baa259d26
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64293824"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack"></a>为 Azure Stack 准备基于 Red Hat 的虚拟机

在本文中，将了解如何准备 Red Hat Enterprise Linux (RHEL) 虚拟机，以供在 Azure Stack 中使用。 本文介绍的 RHEL 版本为 7.1+。 本文所述的用于准备工作的虚拟机监控程序为 Hyper-V、基于内核的虚拟机 (KVM) 和 VMware。

有关 Red Hat Enterprise Linux 支持信息，请参阅 [Red Hat 和 Azure Stack：常见问题解答](https://access.redhat.com/articles/3413531)。

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>从 Hyper-V 管理器准备基于 Red Hat 的虚拟机

本部分假设已从 Red Hat 网站获取 ISO 文件并将 RHEL 映像安装到虚拟硬盘 (VHD)。 有关如何使用 Hyper-V 管理器来安装操作系统映像的详细信息，请参阅[安装 Hyper-V 角色和配置虚拟机](https://technet.microsoft.com/library/hh846766.aspx)。

### <a name="rhel-installation-notes"></a>RHEL 安装说明

* Azure Stack 不支持 VHDX 格式。 Azure 仅支持固定 VHD。 可使用 Hyper-V 管理器将磁盘转换为 VHD 格式，也可以使用 convert-vhd cmdlet。 如果使用 VirtualBox，则选择“固定大小”，而不是在创建磁盘时默认动态分配选项。
* Azure Stack 仅支持第 1 代虚拟机。 可以将第 1 代虚拟机从 VHDX 转换为 VHD 文件格式，从动态扩展磁盘转换为固定大小磁盘。 但无法更改虚拟机的代次。 有关详细信息，请参阅[是否应在 Hyper-V 中创建第 1 代或第 2 代虚拟机？](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)。
* VHD 允许的最大大小为 1,023 GB。
* 在安装 Linux 操作系统时，建议使用标准分区而不是逻辑卷管理器 (LVM)（通常是许多安装的默认设置）。 这种做法可以避免 LVM 名称与克隆的虚拟机冲突，尤其是当需要将操作系统磁盘附加到另一台相同的虚拟机进行故障排除时。
* 需要装载通用磁盘格式 (UDF) 文件系统的内核支持。 首次启动时，附加到来宾的 UDF 格式媒体会将预配配置传递给 Linux 虚拟机。 Azure Linux 代理必须装载 UDF 文件系统才能读取其配置和预配虚拟机。
* 不要在操作系统磁盘上配置交换分区。 可以配置 Linux 代理，并在临时资源磁盘上创建交换文件。 可在以下步骤中找到更多相关信息。
* Azure 上所有 VHD 的虚拟大小必须已按 1 MB 对齐。 从原始磁盘转换为 VHD 时，必须确保在转换前的原始磁盘大小是 1 MB 的倍数。 可以在以下步骤中找到更多详细信息。
* Azure Stack 不支持 cloud-init。 必须使用受支持版本的 Windows Azure Linux 代理 (WALA) 配置 VM。

### <a name="prepare-an-rhel-7-virtual-machine-from-hyper-v-manager"></a>从 HYPER-V 管理器准备 RHEL 7 虚拟机

1. 在 Hyper-V 管理器中，选择虚拟机。

1. 单击“连接”打开该虚拟机的控制台窗口。

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

1. 运行以下命令，确保网络服务在引导时启动：

    ```bash
    sudo systemctl enable network
    ```

1. 注册 Red Hat 订阅，以通过运行以下命令来启用来自 RHEL 存储库中的包的安装：

    ```bash
    sudo subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. 在 grub 配置中修改内核引导行，以使其包含 Azure 的其他内核参数。 若要执行此修改，请在文本编辑器中打开 `/etc/default/grub` 并修改 `GRUB_CMDLINE_LINUX` 参数。 例如：

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   这可以确保所有控制台消息都发送到第一个串行端口，从而协助 Azure 支持人员调试问题。 此配置还会关闭 NIC 的新 RHEL 7 命名约定。

   图形界面式引导和安静引导在云环境中不适用，在云环境中，我们希望所有日志都发送到串行端口。 如果需要，可以保留配置的 `crashkernel` 选项。 请注意，此参数可以将虚拟机中的可用内存量减少 128 MB 或更多，遇到较小的虚拟机大小时，此配置可能会有问题。 我们建议删除以下参数：

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. 完成 `/etc/default/grub` 编辑后，运行以下命令以重新生成 grub 配置：

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

1. 停止并卸载 cloud-init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. 请确保 SSH 服务器已安装且已配置为在引导时启动（默认采用此配置）。 修改 `/etc/ssh/sshd_config` 以包含以下行：

    ```sh
    ClientAliveInterval 180
    ```

1. WALinuxAgent 包 `WALinuxAgent-<version>` 已推送到 Red Hat extras 存储库。 通过运行以下命令启用 extras 存储库：

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. 通过运行以下命令来安装 Azure Linux 代理：

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```

1. 不要在操作系统磁盘上创建交换空间。

    Azure Linux 代理可使用在 Azure 上预配虚拟机后附加到虚拟机的本地资源磁盘自动配置交换空间。 本地资源磁盘是临时磁盘，可能在取消预配虚拟机时被清空。 在上一步中安装 Azure Linux 代理后，相应地在 `/etc/waagent.conf` 中修改以下参数：

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

1. 如果使用的系统是通过企业证书颁发机构部署的，则 RHEL 虚拟机不会信任 Azure Stack 根证书。 需将该证书放入受信任的根存储。 请参阅[将受信任的根证书添加到服务器](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html)。

1. 运行以下命令可取消对虚拟机的设置并且对其进行准备以便在 Azure 上进行设置：

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. 在 Hyper-V 管理器中单击“操作” > “关闭”。

1. 使用 Hyper-V 管理器的“编辑磁盘”功能或 Convert-VHD PowerShell 命令将 VHD 转换为固定大小的 VHD。 Linux VHD 现已准备好上传到 Azure。

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

1. 在 KVM 中通过 qcow2 映像创建虚拟机。 将磁盘类型设置为 **qcow2**，将虚拟网络接口设备型号设置为 **virtio**。 然后，启动该虚拟机，并以 root 身份登录。

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

1. 运行以下命令，确保网络服务在引导时启动：

    ```bash
    sudo systemctl enable network
    ```

1. 注册 Red Hat 订阅，以通过运行以下命令来启用来自 RHEL 存储库中的包的安装：

    ```bash
    subscription-manager register --auto-attach --username=XXX --password=XXX
    ```

1. 在 grub 配置中修改内核引导行，以使其包含 Azure 的其他内核参数。 若要执行此配置，请在文本编辑器中打开 `/etc/default/grub` 并修改 `GRUB_CMDLINE_LINUX` 参数。 例如：

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   此命令还将确保所有控制台消息都发送到第一个串行端口，从而可以协助 Azure 支持人员调试问题。 此命令还会禁用 NIC 的新 RHEL 7 命名约定

   在要将所有日志发送到串行端口的云环境中，图形界面式启动和静默启动不起作用。 如果需要，可以保留配置的 `crashkernel` 选项。 此参数可将虚拟机中的可用内存量减少 128 MB 或更多，当虚拟机大小较小时，这可能会造成问题。 我们建议删除以下参数：

    ```sh
    rhgb quiet crashkernel=auto
    ```

1. 完成 `/etc/default/grub` 编辑后，运行以下命令以重新生成 grub 配置：

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

1. 停止并卸载 cloud-init:

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

1. 当为 Azure Stack 中创建自定义 vhd，请记住，2.2.20 和 2.2.35 （这两个不含） 之间的 WALinuxAgent 版本不适用于 Azure Stack 环境。 您可以使用版本 2.2.20/2.2.35 版本来准备映像。 若要使用 2.2.35 以上的版本来准备自定义映像，在 Azure Stack 更新到 1903年版本或应用 1901年/1902年修补程序。 

     请按照以下说明下载 WALinuxAgent 操作：
    
   a.   下载 setuptools
    ```bash
    wget https://pypi.python.org/packages/source/s/setuptools/setuptools-7.0.tar.gz --no-check-certificate
    tar xzf setuptools-7.0.tar.gz
    cd setuptools-7.0
    ```
   b. 这是一个示例我们在其中下载"2.2.20"从 GitHub 存储库的版本。 下载并解压缩 2.2.20 从我们的 GitHub 代理的版本。 
    ```bash
    wget https://github.com/Azure/WALinuxAgent/archive/v2.2.20.zip
    unzip v2.2.20.zip
    cd WALinuxAgent-2.2.20
    ```
    c. 安装 setup.py
    ```bash
    sudo python setup.py install
    ```
    d. 重新启动 waagent
    ```bash
    sudo systemctl restart waagent
    ```
    e. 如果代理版本匹配，一个下载进行测试。 对于此示例中，它应为 2.2.20。
    
    ```bash
    waagent -version
    ```

1. 不要在操作系统磁盘上创建交换空间。

    Azure Linux 代理可使用在 Azure 上预配虚拟机后附加到虚拟机的本地资源磁盘自动配置交换空间。 本地资源磁盘是临时磁盘，可能在取消预配虚拟机时被清空。 在上一步中安装 Azure Linux 代理后，相应地在 `/etc/waagent.conf` 中修改以下参数：

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

1. 如果使用的系统是通过企业证书颁发机构部署的，则 RHEL 虚拟机不会信任 Azure Stack 根证书。 需将该证书放入受信任的根存储。 请参阅[将受信任的根证书添加到服务器](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html)。

1. 运行以下命令可取消对虚拟机的设置并且对其进行准备以便在 Azure 上进行设置：

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. 关闭 KVM 中的虚拟机。

1. 将 qcow2 映像转换为 VHD 格式。

    > [!NOTE]
    > qemu-img 版本（>=2.2.1）中有一个已知 bug，会导致 VHD 格式不正确。 QEMU 2.6 中已修复此问题。 建议使用 qemu-img 2.2.0 或更低版本，或者更新到 2.6 或更高版本。 参考： https://bugs.launchpad.net/qemu/+bug/1490611。

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

    或者，对于 qemu 版本 **2.6+**，包括 `force_size` 选项：

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>从 VMware 准备基于 Red Hat 的虚拟机

本部分假设已在 VMware 中安装了 RHEL 虚拟机。 有关如何在 VMware 中安装操作系统的详细信息，请参阅 [VMware 来宾操作系统安装指南](https://partnerweb.vmware.com/GOSIG/home.html)。

* 在安装 Linux 操作系统时，建议使用标准分区而不是 LVM，这通常是许多安装的默认设置。 这种做法可以避免 LVM 名称与克隆的虚拟机名称冲突，尤其是在需要将操作系统磁盘附加到另一台虚拟机进行故障排除时。 如果需要，可以在数据磁盘上使用 LVM 或 RAID。
* 不要在操作系统磁盘上配置交换分区。 可将 Linux 代理配置为在临时资源磁盘上创建交换文件。 可以在下面的步骤中找到有关此操作的详细信息。
* 创建虚拟硬盘时，选择“将虚拟磁盘存储为单个文件”。

### <a name="prepare-an-rhel-7-virtual-machine-from-vmware"></a>从 VMware 准备 RHEL 7 虚拟机

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

1. 在 grub 配置中修改内核引导行，以使其包含 Azure 的其他内核参数。 若要执行此修改，请在文本编辑器中打开 `/etc/default/grub` 并修改 `GRUB_CMDLINE_LINUX` 参数。 例如：

    ```sh
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

    此配置还将确保所有控制台消息都发送到第一个串行端口，从而可以协助 Azure 支持人员调试问题。 此外，还会关闭 NIC 的新 RHEL 7 命名约定。 除此之外，建议删除以下参数：

    ```sh
    rhgb quiet crashkernel=auto
    ```

    图形界面式引导和安静引导在云环境中不适用，在云环境中，我们希望所有日志都发送到串行端口。 如果需要，可以保留配置的 `crashkernel` 选项。 请注意，此参数可以将虚拟机中的可用内存量减少 128 MB 或更多，遇到较小的虚拟机大小时，此配置可能会有问题。

1. 完成 `/etc/default/grub` 编辑后，运行以下命令以重新生成 grub 配置：

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

1. 停止并卸载 cloud-init:

    ```bash
    systemctl stop cloud-init
    yum remove cloud-init
    ```

1. 请确保已安装 SSH 服务器且已将其配置为在引导时启动。 此设置通常是默认设置。 修改 `/etc/ssh/sshd_config` 以包含以下行：

    ```sh
    ClientAliveInterval 180
    ```

1. WALinuxAgent 包 `WALinuxAgent-<version>` 已推送到 Red Hat extras 存储库。 通过运行以下命令启用 extras 存储库：

    ```bash
    subscription-manager repos --enable=rhel-7-server-extras-rpms
    ```

1. 通过运行以下命令来安装 Azure Linux 代理：

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent.service
    ```

1. 不要在操作系统磁盘上创建交换空间。

    Azure Linux 代理可使用在 Azure 上预配虚拟机后附加到虚拟机的本地资源磁盘自动配置交换空间。 请注意，本地资源磁盘是临时磁盘，并可能在取消预配虚拟机时被清空。 在上一步中安装 Azure Linux 代理后，相应地在 `/etc/waagent.conf` 中修改以下参数：

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

1. 如果使用的系统是通过企业证书颁发机构部署的，则 RHEL 虚拟机不会信任 Azure Stack 根证书。 需将该证书放入受信任的根存储。 请参阅[将受信任的根证书添加到服务器](https://manuals.gfi.com/en/kerio/connect/content/server-configuration/ssl-certificates/adding-trusted-root-certificates-to-the-server-1605.html)。

1. 运行以下命令可取消对虚拟机的设置并且对其进行准备以便在 Azure 上进行设置：

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

1. 关闭虚拟机，将 VMDK 文件转换为 VHD 格式。

    > [!NOTE]
    > qemu-img 版本（>=2.2.1）中有一个已知 bug，会导致 VHD 格式不正确。 QEMU 2.6 中已修复此问题。 建议使用 qemu-img 2.2.0 或更低版本，或者更新到 2.6 或更高版本。 参考： <https://bugs.launchpad.net/qemu/+bug/1490611>。

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

    或者，对于 qemu 版本 **2.6+**，包括 `force_size` 选项：

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd
    ```

## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>使用 kickstart 文件自动从 ISO 准备基于 Red Hat 的虚拟机

1. 创建包括以下内容的 kickstart 文件，并保存该文件。 有关 kickstart 安装的详细信息，请参阅 [Kickstart 安装指南](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html)。

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

1. 在 Hyper-V 管理器中，创建新的虚拟机。 在“连接虚拟硬盘”页上，选择“稍后附加虚拟硬盘”，并完成新建虚拟机向导。

1. 打开虚拟机设置：

    a. 将新的虚拟硬盘附加到虚拟机。 请务必选择“VHD 格式”和“固定大小”。

    b. 将安装 ISO 附加到 DVD 光驱。

    c. 将 BIOS 设置为从 CD 启动。

1. 启动虚拟机。 当安装指南出现时，请按 **Tab** 键来配置启动选项。

1. 在启动选项的末尾输入 `inst.ks=<the location of the kickstart file>`，并按 **Enter**。

1. 等待安装完成。 完成后，虚拟机将自动关闭。 Linux VHD 现已准备好上传到 Azure。

## <a name="known-issues"></a>已知问题

### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>使用非 Hyper-V 虚拟机监控程序时，初始 RAM 磁盘未包含 Hyper-V 驱动程序

在某些情况下，Linux 安装程序可能无法在初始 RAM 磁盘（initrd 或 initramfs）中包含 Hyper-V 驱动程序，除非 Linux 检测到它正在 Hyper-V 环境中运行。

当你使用不同的虚拟化系统 （即，Oracle VM VirtualBox、 Xen 项目等） 来准备 Linux 映像时，您可能需要重新生成 initrd 以确保至少 hv_vmbus 和 hv_storvsc 内核模块可在初始 RAM磁盘。 至少在基于上游 Red Hat 分发的系统上这是一个已知问题。

要解决此问题，请将 Hyper-V 模块添加到 initramfs 并进行重新生成：

编辑 `/etc/dracut.conf` 并添加以下内容：

```sh
add_drivers+="hv_vmbus hv_netvsc hv_storvsc"
```

重新生成 initramfs：

```bash
dracut -f -v
```

有关详细信息，请参阅[重新生成 initramfs](https://access.redhat.com/solutions/1958)。

## <a name="next-steps"></a>后续步骤

现在，可以使用 Red Hat Enterprise Linux 虚拟硬盘在 Azure Stack 中创建新的虚拟机。 如果这是你第一次将 VHD 文件上传到 Azure Stack，请参阅[创建和发布市场项](azure-stack-create-and-publish-marketplace-item.md)。

有关经认证可运行 Red Hat Enterprise Linux 的虚拟机监控程序的详细信息，请参阅 [Red Hat 网站](https://access.redhat.com/certified-hypervisors)。
