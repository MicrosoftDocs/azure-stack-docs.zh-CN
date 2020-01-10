---
title: 如何通过 Azure Stack 中心连接到 iSCSI 存储 |Microsoft Docs
description: 了解如何通过 Azure Stack 中心连接到 iSCSI 存储。
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: a8cb3ad8e45f6effc593b8c5b2cc9e59dd176f5f
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75819686"
---
# <a name="how-to-connect-to-iscsi-storage-with-azure-stack-hub"></a>如何通过 Azure Stack 中心连接到 iSCSI 存储

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

你可以使用本文中的模板将 Azure Stack 中心虚拟机（VM）连接到本地 iSCSI 目标，将 VM 设置为使用托管在我们的 Azure Stack 中心以外的其他位置，并将其存储在数据中心中的其他位置。 本文介绍如何使用 Windows 计算机作为 iSCSI 目标。

可以在[Azure 智能边缘模式](https://github.com/lucidqdreams/azure-intelligent-edge-patterns)GitHub 存储库的**lucidqdreams**分叉中找到该模板。 该模板位于**存储 iSCSI**文件夹中。 此模板旨在设置在 Azure Stack 中心端连接到 iSCSI 目标所需的基础结构。 这包括一个虚拟机，该虚拟机将用作 iSCSI 发起程序及其随附的 VNet、NSG、PIP 和存储。 部署模板后，需要运行两个 PowerShell 脚本来完成配置。 一个脚本将在本地 VM （目标）上运行，一个将在 Azure Stack 中心 VM （发起程序）上运行。 完成这些工作后，会将本地存储添加到 Azure Stack 中心 VM。 

## <a name="overview"></a>概述

此关系图显示了 Azure Stack 集线器上托管的 VM，该 VM 具有从本地 Windows 计算机（物理或虚拟）的 iSCSI 装入的磁盘，允许 Azure Stack 中心外部的存储通过 iSCSI 协议装载到 Azure Stack 中心托管的 VM。

![替换文字](./media/azure-stack-network-howto-iscsi-storage/overview.png)

### <a name="requirements"></a>要求

- 运行 Windows Server 2016 Datacenter 或 Windows Server 2019 Datacenter 的本地计算机（物理或虚拟）。
- 必需 Azure Stack 中心市场项：
    -  Windows Server 2016 Datacenter 或 Windows Server 2019 Datacenter （建议使用最新版本）。
    -  PowerShell DSC 扩展。
    -  自定义脚本扩展。
    -  现有虚拟机或物理计算机。 理想情况下，此计算机将有两个网络适配器。 这也可能是其他 iSCSI 目标（例如，SAN）。

### <a name="things-to-consider"></a>注意事项

- 将网络安全组应用到模板子网。 查看此设置并根据需要进行额外的补偿。
- RDP 拒绝规则应用于隧道 NSG，如果你打算通过公共 IP 地址访问 Vm，则需要将其设置为 "允许"。
- 此解决方案不考虑 DNS 解析。
- 应更改 Chapusername 和 Chappassword。 Chappassword 的长度必须为12到16个字符。
- 此模板正在为 VM 使用静态 IP 地址，因为 iSCSI 连接使用配置中的本地地址。
- 此模板正在使用 BYOL Windows 许可证。
- 你还可以将基于 Linux 的系统连接到 iSCSI 目标。 可在 ubuntu 文档中的[ISCSI 发起程序](https://help.ubuntu.com/lts/serverguide/iscsi-initiator.html)一文中找到相关说明。

### <a name="options"></a>选项

- 你可以使用自己的 Blob 存储帐户和 SAS 令牌并使用 **_artifactsLocation** ， **_artifactsLocationSasToken**参数能够将自己的存储 Blob 与 SAS 令牌配合使用。
- 此模板提供 VNet 命名和 IP 寻址的默认值。
- 此配置仅有一个来自 iSCSI 客户端的 iSCSI nic。 我们已经测试了几种配置来利用单独的子网和 Nic，但是遇到了多个网关的问题，并尝试创建一个单独的存储子网来隔离流量，实际上是真正的冗余。 
- 请注意在法律子网和地址范围内保留这些值，因为部署可能会失败。 
- PowerShell DSC 包的主要用途是检查是否有挂起的重新启动。 如果需要，可以进一步自定义此 DSC。 有关详细信息，请参阅[omputerManagementDsc](https://github.com/PowerShell/ComputerManagementDsc/)。

### <a name="resource-group-template-iscsi-client"></a>资源组模板（iSCSI 客户端）

此图显示了从模板部署的资源，用于创建可用于连接到 iSCSI 目标的 iSCSI 客户端。 此模板将部署 VM 和其他资源，此外，它还将运行 prepare-iSCSIClient 并重新启动 VM。

![替换文字](./media/azure-stack-network-howto-iscsi-storage/iscsi-file-server.png)

### <a name="the-deployment-process"></a>部署过程

资源组模板生成输出，该输出旨在作为下一步的输入。 它主要侧重于服务器名称和 iSCSI 流量源自的 Azure Stack 中心公共 IP 地址。 对于本示例：

1. 部署基础结构模板。
2. 将 Azure Stack 中心 VM 部署到在你的数据中心内的其他位置托管的 VM。 
3. 运行 `Create-iSCSITarget.ps1` 使用来自模板的 IP 地址和服务器名称输出作为 iSCSI 目标（可以是虚拟机或物理服务器）上脚本的输出参数。
4. 使用 iSCSI 目标服务器的外部 IP 地址作为输入来运行 `Connect-toiSCSITarget.ps1` 脚本。 

![替换文字](./media/azure-stack-network-howto-iscsi-storage/process.png)

### <a name="inputs-for-azuredeployjson"></a>Azuredeploy.json 的输入

|**参数**|default|**description**|
|------------------|---------------|------------------------------|
|WindowsImageSKU         |2019-Datacenter   |请选择基本 Windows VM 映像
|VMSize                  |Standard_D2_v2    |请输入 VM 大小
|VMName                  |FileServer        |VM 名称
|adminUsername           |storageadmin      |新 VM 的管理员名称
|adminPassword           |                  |新 Vm 的管理员帐户的密码。 默认值为订阅 ID
|VNetName                |存储空间           |VNet 的名称。 这将用于标记资源
|VNetAddressSpace        |10.10.0.0/23      |VNet 的地址空间
|VNetInternalSubnetName  |内部          |VNet 内部子网名称
|VNetInternalSubnetRange |10.10.1.0/24      |VNet 内部子网的地址范围
|InternalVNetIP          |10.10.1.4         |文件服务器的内部 IP 的静态地址。
|_artifactsLocation      ||
|_artifactsLocationSasToken||

### <a name="deployment-steps"></a>部署步骤

1. 使用 `azuredeploy.json` 部署 iSCSI 客户端基础结构
2. 在本地服务器 iSCSI 目标上运行 `Create-iSCSITarget.ps1`。 模板完成后，你将需要在本地服务器 iSCSI 目标上运行 Create-iSCSITarget，其中包含第一步的输出
3. 在 iSCSI 客户端上运行 `Connect-toiSCSITarget.ps1`。 通过 iSCSI 目标的详细信息，在 iSCSI 客户端上取消 Connect-toiSCSITarget

## <a name="adding-iscsi-storage-to-existing-vms"></a>向现有 Vm 添加 iSCSI 存储

你还可以在现有虚拟机上运行脚本，以从 iSCSI 客户端连接到 iSCSI 目标。 如果要自行创建 iSCSI 目标，则此流程为。 此图显示了 PowerShell 脚本的执行流。 可以在脚本目录中找到这些脚本：

![替换文字](./media/azure-stack-network-howto-iscsi-storage/script-flow.png)

### <a name="prepare-iscsiclientps1"></a>Prepare-iSCSIClient

`Prepare-iSCSIClient.ps1` 脚本在 iSCSI 客户端上安装先决条件，这包括：
- 安装多路径 IO 服务
- 将 iSCSI 发起程序服务设置为自动启动
- 为 iSCSI 启用多路径 MPIO 支持
- 启用所有 iSCSI 卷的自动申报
- 将磁盘超时设置为60秒

在安装这些先决条件之后，请务必重新启动系统。 MPIO 负载平衡策略需要重新启动，以便能够对其进行设置。

### <a name="create-iscsitargetps1"></a>Create-iSCSITarget

`Create-iSCSITarget.ps1 `脚本将在为存储提供服务的系统上运行。 可以创建多个受发起方限制的磁盘和目标。 你可以多次运行此脚本，以创建多个可附加到不同目标的虚拟磁盘。 可以将多个磁盘连接到一个目标。 

|**输入**|default|**description**|
|------------------|---------------|------------------------------|
|RemoteServer         |FileServer               |连接到 iSCSI 目标的服务器的名称
|RemoteServerIPs      |1.1.1.1                  |ISCSI 流量将来自的 IP 地址
|DiskFolder           |C:\iSCSIVirtualDisks     |将存储虚拟磁盘的文件夹和驱动器
|DiskName             |DiskName                 |磁盘 VHDX 文件的名称
|DiskSize             |5GB                      |VHDX 磁盘大小
|目标名称           |RemoteTarget01           |用于定义 iSCSI 客户端目标配置的目标名称。 
|ChapUsername         |username                 |Chap 身份验证的用户名名称
|ChapPassword         |userP@ssw0rd!            |Chap 身份验证的密码名称。 它必须为12到16个字符

### <a name="connect-toiscsitargetps1"></a>Connect-toiSCSITarget

`Connect-toiSCSITarget.ps1` 是最终脚本，它在 iSCSI 客户端上运行，并将 iSCSI 目标提供的磁盘装载到 iSCSI 客户端。

|**输入**|default|**description**|
|------------------|---------------|------------------------------|
|TargetiSCSIAddresses   |"2.2.2.2"、"2.2.2.3"    |ISCSI 目标的 IP 地址
|LocalIPAddresses       |"10.10.1.4"            |这是 iSCSI 流量将来自的内部 IP 地址
|LoadBalancePolicy      |C:\iSCSIVirtualDisks   |ISCSI 流量将来自的 IP 地址
|ChapUsername           |username               |Chap 身份验证的用户名名称
|ChapPassword           |userP@ssw0rd!          |Chap 身份验证的密码名称。 它必须为12到16个字符

## <a name="next-steps"></a>后续步骤

[Azure Stack 集线器网络的差异和注意事项](azure-stack-network-differences.md)  