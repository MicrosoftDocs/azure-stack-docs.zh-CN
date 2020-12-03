---
title: 如何使用 Azure Stack Hub 连接到 iSCSI 存储
description: 了解如何使用 Azure Stack Hub 连接到 iSCSI 存储。
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: f6e02aa1185070d641d946ef99a44858ce8a1e5b
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525738"
---
# <a name="connect-to-iscsi-storage-with-azure-stack-hub"></a>使用 Azure Stack Hub 连接到 iSCSI 存储

可以使用本文中的模板，将 Azure Stack Hub 虚拟机 (VM) 连接到本地 iSCSI 目标，并将 VM 设置为使用托管在 Azure Stack Hub 外部和数据中心其他位置的存储。 本文介绍如何将 Windows 计算机用作 iSCSI 目标。

可以在 [Azure 智能边缘模式](https://github.com/lucidqdreams/azure-intelligent-edge-patterns) GitHub 存储库的 **lucidqdreams** 分支中找到该模板。 该模板位于 **storage-iSCSI** 文件夹中。 该模板旨在用于设置 Azure Stack Hub 端所需的基础结构，以便连接到 iSCSI 目标。 此配置包括用作 iSCSI 发起程序的虚拟机，及其随附的 VNet、NSG、PIP 和存储。 部署该模板之后，需要运行两个 PowerShell 脚本来完成配置。 其中一个脚本在本地 VM（目标）上运行，另一个在 Azure Stack Hub VM（发起端）上运行。 这些操作完成后，本地存储即会添加到 Azure Stack Hub VM。 

## <a name="overview"></a>概述

下图显示了托管在 Azure Stack Hub 上的 VM，其中包含从本地 Windows 计算机（物理或虚拟机）装载的 iSCSI 磁盘，它允许通过 iSCSI 协议将 Azure Stack Hub 外部的存储装载到 Azure Stack Hub 托管的 VM 内部。

![此图显示了 Azure Stack Hub 上托管的、访问外部 iSCSI 已装载磁盘的 VM。](./media/azure-stack-network-howto-iscsi-storage/overview-iscsi2.svg)

### <a name="requirements"></a>要求

- 运行 Windows Server 2016 Datacenter 或 Windows Server 2019 Datacenter 的本地计算机（物理或虚拟机）。
- 所需的 Azure Stack Hub 市场项：
    -  Windows Server 2016 Datacenter 或 Windows Server 2019 Datacenter（建议使用最新内部版本）。
    -  PowerShell DSC 扩展。
    -  自定义脚本扩展。
    -  现有的虚拟机或物理机。 此计算机最好是配备了两个网络适配器。 它也可以是另一个 iSCSI 目标，例如实例的 SAN。

### <a name="things-to-consider"></a>注意事项

- 某个网络安全组将应用到模板子网。 请注意到这一点，并根据需要另行预留额度。
- RDP“拒绝”规则将应用到隧道 NSG，如果你倾向于通过公共 IP 地址访问 VM，则需要将此规则设置为“允许”。
- 此解决方案不考虑 DNS 解析。
- 请更改 Chapusername 和 Chappassword。 Chappassword 的长度必须是 12 到 16 个字符。
- 此模板对 VM 使用静态 IP 地址，因为 iSCSI 连接使用配置中的本地地址。
- 此模板使用 BYOL Windows 许可证。
- 还可以将基于 Linux 的系统连接到 iSCSI 目标。 可以在 Ubuntu 文档的 [iSCSI 发起程序](https://help.ubuntu.com/lts/serverguide/iscsi-initiator.html)一文中找到相关说明。

### <a name="options"></a>选项

- 可以通过 **_artifactsLocation** 和 **_artifactsLocationSasToken** 参数使用自己的 Blob 存储帐户和 SAS 令牌，这样就能配合 SAS 令牌使用自己的存储 Blob。
- 此模板为 VNet 命名和 IP 寻址提供默认值。
- 此配置只包含一个来自 iSCSI 客户端的 iSCSI NIC。 我们已测试过多种配置，以利用不同的子网和 NIC，但是，测试期间多个网关出现了问题，因此我们正在尝试创建单独的存储子网来隔离流量（真正冗余的配置）。 
- 请谨慎地使这些值保持在合法的子网和地址范围内，否则部署可能失败。 
- PowerShell DSC 包的主要用途是检查是否存在挂起的重新启动。 如果需要，可以进一步自定义此 DSC。 有关详细信息，请参阅 [omputerManagementDsc](https://github.com/PowerShell/ComputerManagementDsc/)。

### <a name="resource-group-template-iscsi-client"></a>资源组模板（iSCSI 客户端）

下图显示了通过模板部署的资源，这些资源用于创建可供连接到 iSCSI 目标的 iSCSI 客户端。 此模板将部署 VM 和其他资源，此外，它还运行 prepare-iSCSIClient.ps1 并重新启动 VM。

![此图显示了通过模板部署的资源，这些资源用于创建要连接到 iSCSI 目标的 iSCSI 客户端。 它显示带有内部子网和 NIC（网卡）的文件服务器、内部 PIP（专用 Internet 协议）和 NSG（网络安全组）。](./media/azure-stack-network-howto-iscsi-storage/iscsi-file-server.svg)

### <a name="the-deployment-process"></a>部署过程

资源组模板生成输出作为下一步骤的输入。 它着重于发出 iSCSI 流量的服务器名称和 Azure Stack Hub 公共 IP 地址。 对于本示例：

1. 部署基础结构模板。
2. 将 Azure Stack Hub VM 部署到托管在数据中心其他位置的 VM。 
3. 使用模板输出的 IP 地址和服务器名称作为 iSCSI 目标（可以是虚拟机或物理服务器）上脚本的输入输出参数，来运行 `Create-iSCSITarget.ps1`。
4. 使用 iSCSI 目标服务器的外部 IP 地址作为输入来运行 `Connect-toiSCSITarget.ps1` 脚本。 

![此图显示上面列出的四个步骤中的前三个步骤，并包括输入和输出。 步骤如下：部署基础结构、创建 iSCSI 目标，以及连接到 iSCSI。](./media/azure-stack-network-howto-iscsi-storage/process.svg)

### <a name="inputs-for-azuredeployjson"></a>azuredeploy.json 的输入

|**Parameters**|**default**|description|
|------------------|---------------|------------------------------|
|WindowsImageSKU         |2019-Datacenter   |请选择 Windows VM 基础映像
|VMSize                  |Standard_D2_v2    |请输入 VM 大小
|VMName                  |FileServer        |VM 名称
|adminUsername           |storageadmin      |新 VM 的管理员名称
|adminPassword           |                  |新 VM 管理员帐户的密码。 默认值为订阅 ID
|VNetName                |存储           |VNet 的名称。 用于标记资源
|VNetAddressSpace        |10.10.0.0/23      |VNet 的地址空间
|VNetInternalSubnetName  |内部          |VNet 内部子网名称
|VNetInternalSubnetRange |10.10.1.0/24      |VNet 内部子网的地址范围
|InternalVNetIP          |10.10.1.4         |文件服务器内部 IP 的静态地址。
|_artifactsLocation      ||
|_artifactsLocationSasToken||

### <a name="deployment-steps"></a>部署步骤

1. 使用 `azuredeploy.json` 部署 iSCSI 客户端基础结构
2. 在本地服务器 iSCSI 目标上运行 `Create-iSCSITarget.ps1`。 模板完成之后，需在本地服务器 iSCSI 目标上使用第一个步骤的输出来运行 Create-iSCSITarget.ps1
3. 在 iSCSI 客户端上运行 `Connect-toiSCSITarget.ps1`。 使用 iSCSI 目标的详细信息在 iSCSI 客户端上运行 Connect-toiSCSITarget.ps1

## <a name="adding-iscsi-storage-to-existing-vms"></a>将 iSCSI 存储添加到现有 VM

也可以在现有虚拟机上运行脚本，以从 iSCSI 客户端连接到 iSCSI 目标。 下面是自行创建 iSCSI 目标的流程。 下图显示 PowerShell 脚本的执行流。 这些脚本可在 Script 目录中找到：

![此图显示了下面讨论的三个脚本。 这些脚本是（按执行顺序列出）：Prepare-iSCSIClient.ps1（在客户端上执行）、Create iSCSITarget.ps1（在目标上执行）和 Connect-toiSCSITarget.ps1（在客户端上执行）。](./media/azure-stack-network-howto-iscsi-storage/script-flow.svg)

### <a name="prepare-iscsiclientps1"></a>Prepare-iSCSIClient.ps1

`Prepare-iSCSIClient.ps1` 脚本在 iSCSI 客户端上安装必备组件，包括：
- 安装多路径 IO 服务
- 将 iSCSI 发起程序服务设置为自动启动
- 启用对 iSCSI 多路径 MPIO 的支持
- 启用所有 iSCSI 卷的自动声明
- 将磁盘超时设置为 60 秒

安装这些必备组件后，必须重新启动系统。 MPIO 负载均衡策略需要重新启动才能进行设置。

### <a name="create-iscsitargetps1"></a>Create-iSCSITarget.ps1

`Create-iSCSITarget.ps1` 脚本将在存储服务器上运行。 可以创建受到发起端限制的多个磁盘和目标。 可运行此脚本多次，以创建多个可附加到不同目标的虚拟磁盘。 可将多个磁盘连接到一个目标。 

|**输入**|**default**|description|
|------------------|---------------|------------------------------|
|RemoteServer         |FileServer               |连接到 iSCSI 目标的服务器的名称
|RemoteServerIPs      |1.1.1.1                  |iSCSI 流量的来源 IP 地址
|DiskFolder           |C:\iSCSIVirtualDisks     |存储虚拟磁盘的文件夹和驱动器
|DiskName             |DiskName                 |磁盘 VHDX 文件的名称
|DiskSize             |5GB                      |VHDX 磁盘大小
|TargetName           |RemoteTarget01           |用于定义 iSCSI 客户端目标配置的目标名称。 
|ChapUsername         |username                 |用于 Chap 身份验证的用户名
|ChapPassword         |userP@ssw0rd!            |用于 Chap 身份验证的密码名称。 长度必须为 12 到 16 个字符

### <a name="connect-toiscsitargetps1"></a>Connect-toiSCSITarget.ps1

`Connect-toiSCSITarget.ps1` 是最后一个脚本，它在 iSCSI 客户端上运行，将 iSCSI 目标提供的磁盘装载到 iSCSI 客户端。

|**输入**|**default**|description|
|------------------|---------------|------------------------------|
|TargetiSCSIAddresses   |"2.2.2.2","2.2.2.3"    |iSCSI 目标的 IP 地址
|LocalIPAddresses       |"10.10.1.4"            |这是 iSCSI 流量的来源内部 IP 地址
|LoadBalancePolicy      |C:\iSCSIVirtualDisks   |iSCSI 流量的来源 IP 地址
|ChapUsername           |username               |用于 Chap 身份验证的用户名
|ChapPassword           |userP@ssw0rd!          |用于 Chap 身份验证的密码名称。 长度必须为 12 到 16 个字符

## <a name="next-steps"></a>后续步骤

[Azure Stack Hub 网络的差异和注意事项](azure-stack-network-differences.md)  