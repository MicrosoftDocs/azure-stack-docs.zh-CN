---
title: 使用 SDN Express 部署 SDN 基础结构
description: 了解如何使用 SDN Express 部署 SDN 基础结构
author: v-dasis
ms.topic: how-to
ms.date: 02/17/2021
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e367602252207a673316caf3482d7805bff02ba8
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647804"
---
# <a name="deploy-an-sdn-infrastructure-using-sdn-express"></a>使用 SDN Express 部署 SDN 基础结构

> 适用于 Azure Stack HCI 版本 20H2

本主题介绍如何使用 SDN Express PowerShell 脚本将端到端软件定义的网络 (SDN) 基础结构部署。 基础结构可能包含高可用性 (HA) 网络控制器 (NC) 、高可用软件负载平衡器 (SLB) 和高度可用的网关 (GW) 。  脚本支持分阶段部署，在此部署中，你只需部署网络控制器组件即可实现一组核心功能，同时降低网络要求。

你还可以使用 System Center Virtual Machine Manager (VMM) 部署 SDN 基础结构。 有关详细信息，请参阅 [在 VMM 构造中管理 SDN 资源](/system-center/vmm/network-sdn)。

## <a name="before-you-begin"></a>在开始之前

在开始 SDN 部署之前，请规划并配置物理和主机网络基础结构。 参考以下文章：

- [物理网络要求](../concepts/physical-network-requirements.md)
- [主机网络要求](../concepts/host-network-requirements.md)
- [使用 Windows 管理中心创建群集](../deploy/create-cluster.md)
- [使用 Windows PowerShell 创建群集](../deploy/create-cluster-powershell.md)
- [规划软件定义的网络基础结构](../concepts/plan-software-defined-networking-infrastructure.md)

不必部署所有 SDN 组件。 请参阅[规划软件定义的网络基础结构](../concepts/plan-software-defined-networking-infrastructure.md)中的[分阶段部署](../concepts/plan-software-defined-networking-infrastructure.md#phased-deployment)部分来确定所需的基础结构组件，并相应地运行脚本。

请确保所有主机服务器都安装了 Azure Stack HCI 操作系统。 有关如何执行此操作，请参阅 [部署 AZURE STACK HCI 操作系统](../deploy/operating-system.md) 。

## <a name="requirements"></a>要求

成功的 SDN 部署必须满足以下要求：

- 所有主机服务器都必须启用 Hyper-v
- 所有主机服务器都必须加入到 Active Directory
- 必须创建虚拟交换机
- 对于配置文件中定义的子网和 Vlan，必须配置物理网络
- 在配置文件中指定的 VHDX 文件必须可从运行 SDN Express 脚本的部署计算机访问

## <a name="create-the-vdx-file"></a>创建 VDX 文件

SDN 使用包含 Azure Stack HCI 操作系统 (OS) 的 VHDX 文件作为) Vm 创建 SDN 虚拟 (机的源。 VHDX 中 OS 的版本必须与 Azure Stack HCI Hyper-v 主机使用的版本相匹配。 此 VHDX 文件由所有 SDN 基础结构组件使用。

如果已从 ISO 下载并安装了 Azure Stack HCI OS，则可以使用 [add-windowsimage ](https://www.powershellgallery.com/packages/Convert-WindowsImage/10.0) 实用工具创建 VHDX 文件。

下面显示了一个使用的示例 `Convert-WindowsImage` ：

 ```powershell
$wimpath = "d:\sources\install.wim"
$vhdpath = "c:\temp\WindowsServerDatacenter.vhdx"
$Edition = 4   # 4 = Full Desktop, 3 = Server Core

import-module ".\convert-windowsimage.ps1"

Convert-WindowsImage -SourcePath $wimpath -Edition $Edition -VHDPath $vhdpath -SizeBytes 500GB -DiskLayout UEFI
```

## <a name="download-the-github-repository"></a>下载 GitHub 存储库

GitHub 中的 SDN Express 脚本文件。 第一步是在部署计算机上获取所需的文件和文件夹。

1. 请参阅 [MICROSOFT SDN GitHub](https://github.com/microsoft/SDN) 存储库。

1. 在存储库中，展开 " **代码** " 下拉列表，然后选择 " **克隆** " 或 " **下载 ZIP** " 以将 SDN 文件下载到指定的部署计算机。

    > [!NOTE]
    > 指定的部署计算机必须运行 Windows Server 2016 或更高版本。

1. 提取 ZIP 文件，并将 `SDNExpress` 文件夹复制到部署计算机的 `C:\` 文件夹。

## <a name="edit-the-configuration-file"></a>编辑配置文件

PowerShell `MultiNodeSampleConfig.psd1` 配置数据文件包含 SDN Express 脚本所需的所有参数和设置，作为各种参数和配置设置的输入。 此文件包含有关根据是只部署网络控制器组件还是同时部署软件负载平衡器和网关组件而需要填写的内容的具体信息。 有关详细信息，请参阅 [规划软件定义的网络基础结构](../concepts/plan-software-defined-networking-infrastructure.md) 主题。

导航到该 `C:\SDNExpress\scripts` 文件夹，然后 `MultiNodeSampleConfig.psd1` 在最喜欢的文本编辑器中打开该文件。 更改特定参数值以适应您的基础结构和部署：

### <a name="general-settings-and-parameters"></a>常规设置和参数

对于所有部署，这些设置和参数通常由 SDN 使用：

- **VHDPath** -所有 SDN 基础结构 vm 使用的 VHD 文件路径 (NC，SLB，GW) 
- **VHDFile** -所有 SDN 基础结构 vm 使用的 VHD 文件名
- **VMLocation** -SDN 基础结构 vm 的文件路径
- **JoinDomain** -SDN 基础结构 vm 加入到的域
- **SDNMacPoolStart** -正在开始为客户端工作负荷 VM 提供 MAC 池地址
- **SDNMacPoolEnd** -客户端工作负荷 vm 的结束 MAC 池地址
- **ManagementSubnet** -NC 用于管理 hyper-v 主机、SLB 和 GW 组件的管理网络子网
- **ManagementGateway** -管理网络的网关地址
- **ManagementDNS** -管理网络的 DNS 服务器
- **ManagementVLANID** -管理网络的 VLAN ID
- **DomainJoinUsername** -管理员用户名
- **LocalAdminDomainUser** -管理员密码
- **RestName** -管理客户端使用的 DNS 名称 (例如 Windows 管理中心) 与 NC 通信
- **HyperVHosts** -要由网络控制器管理的主机服务器
- **NCUsername** -网络控制器帐户用户名
- **ProductKey** -SDN 基础结构 vm 的产品密钥
- **SwitchName** -仅当 hyper-v 主机上存在多个虚拟交换机时需要
- **Set-vmmemory** -分配给基础结构 vm 的内存 (（GB) ）。 默认值为 4 GB
- **VMProcessorCount** -分配给基础结构 vm 的处理器数。 默认值为8
- **区域设置** -如果未指定，则使用部署计算机的区域设置
- **时区** -如果未指定，则使用部署计算机的本地时区

如果将密码作为文本编码的安全字符串进行存储，则可以选择包含密码。  仅当 SDN Express 脚本在加密密码的同一台计算机上运行时，才会使用密码，否则会提示输入以下密码：

- **DomainJoinSecurePassword** -用于域帐户
- **LocalAdminSecurePassword** -用于本地管理员帐户
- **NCSecurePassword** -网络控制器帐户

> [!NOTE]
> SDN Express 脚本不支持 DHCP 寻址。 确保所有 SDN 基础结构 Vm 的 MAC 地址超出 `SDNMACPool` 参数范围。

### <a name="network-controller-vm-section"></a>网络控制器 VM 部分

`NCs = @()`部分用于网络控制器 vm。 请确保每个 NC VM 的 MAC 地址超出了 `SDNMACPool` "常规设置" 中所列的范围：

- **ComputerName** -NC VM 的名称
- **主机名** -NC VM 所在的服务器的主机名
- **ManagementIP** -NC VM 的管理网络 IP 地址
- **MACAddress** -NC VM 的 MAC 地址

### <a name="software-load-balancer-vm-section"></a>软件负载平衡器 VM 部分

`Muxes = @()`部分用于 SLB vm。 请确保每个 SLB VM 的 MAC 地址超出了 `SDNMACPool` "常规设置" 中所列的范围。 如果不部署 SLB 组件，请将本部分留空 (`Muxes = @()`) ：

- **ComputerName** -SLB VM 的名称
- **主机** 名-SLB VM 所在的服务器的主机名
- **ManagementIP** -SLB VM 的管理网络 IP 地址
- **MACAddress** -SLB VM 的 MAC 地址
- 用于 SLB VM 的 **PAIPAddress** 提供程序网络 IP 地址 (PA) 
- 用于 SLB VM 的 **PAMACAddress** 提供程序网络 IP 地址 (PA) 

### <a name="gateway-vm-section"></a>网关 VM 部分

`Gateways = @()`部分用于网关 vm。 请确保每个网关 VM 的 MAC 地址超出了 `SDNMACPool` "常规设置" 中所列的范围。 `Gateways = @()`如果不部署网关组件，请将本部分留空 () ：

- **ComputerName** -网关 VM 的名称
- **主机名** -网关 VM 所在的服务器的主机名
- **ManagementIP** -网关 VM 的管理网络 IP 地址
- **MACAddress** -网关 VM 的 MAC 地址
- **FrontEndIp** -网关 VM 的提供商网络前端 IP 地址
- **FrontEndMac** -网关 VM 的提供商网络前端 MAC 地址
- **BackEndMac** -网关 VM 的提供商网络后端 MAC 地址

### <a name="additional-settings-for-slb-and-gateway"></a>适用于 SLB 和网关的其他设置

以下附加参数由 SLB 和网关 Vm 使用。 如果不部署 SLB 或网关 Vm，请将这些值留空：

- **SDNASN** -SDN 与网络交换机一起使用的的自治系统编号 (ASN) 
- **RouterASN** -网关路由器 ASN
- **RouterIPAddress** -网关路由器 IP 地址
- **PrivateVIPSubnet** -专用子网 (VIP) 的虚拟 IP 地址
- **PublicVIPSubnet** -公共子网的虚拟 IP 地址

以下附加参数仅供网关 Vm 使用。 如果不部署网关 Vm，请将这些值留空：

- **PoolName** -所有网关 vm 使用的池名称
- **GRESubnet** -如果使用 gre 连接，则适用于 GRE (的 VIP 子网) 
- **容量** -池中每个网关 VM 的容量（以 Kbps 为单位）

### <a name="settings-for-tenant-overlay-networks"></a>租户覆盖网络的设置

如果要为租户部署和管理覆盖虚拟网络，请使用以下参数。 如果使用网络控制器来管理传统 VLAN 网络，则可以将这些值留空。

- **PASubnet** (PA) 网络的提供程序地址的子网
- **PAVLANID** -PA 网络的 VLAN ID
- **PAGateway** -PA 网络网关的 IP 地址
- **PAPoolStart** -PA 网络池的起始 IP 地址
- **PAPoolEnd** -PA 网络池的结束 IP 地址

## <a name="run-the-deployment-script"></a>运行部署脚本

SDN Express 脚本部署指定的 SDN 基础结构。 脚本完成后，SDN 基础结构即可用于 VM 工作负荷部署。

1. `README.md`有关如何运行部署脚本的最新信息，请查看该文件。  

1. 从具有群集主机服务器的管理凭据的用户帐户运行以下命令：

    ```powershell
    SDNExpress\scripts\SDNExpress.ps1 -ConfigurationDataFile MultiNodeSampleConfig.psd1 -Verbose
    ```

1. 创建 NC Vm 后，请在 DNS 服务器上为网络控制器群集名称配置动态 DNS 更新。 有关详细信息，请参阅 [如何配置 DNS 动态更新](https://docs.microsoft.com/troubleshoot/windows-server/networking/configure-dns-dynamic-updates-windows-server-2003)。

## <a name="next-steps"></a>后续步骤

管理 Vm。 有关详细信息，请参阅[管理 VM](../manage/vm.md)。
