---
title: Azure Stack HCI 上的 Azure Kubernetes 服务要求
description: 开始使用 Azure Stack HCI 上的 Azure Kubernetes 服务之前
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 12/02/2020
ms.openlocfilehash: 71c842cf44963988da7926003646b246bf80f802
ms.sourcegitcommit: 8776cbe4edca5b63537eb10bcd83be4b984c374a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/13/2021
ms.locfileid: "98175730"
---
# <a name="system-requirements-for-azure-kubernetes-service-on-azure-stack-hci"></a>Azure Stack HCI 上的 Azure Kubernetes 服务的系统要求

> 适用于： Azure Stack HCI、Windows Server 2019 Datacenter

本文介绍了在 Azure Stack HCI 或 Windows Server 2019 Datacenter 上设置 Azure Kubernetes 服务并使用它创建 Kubernetes 群集的要求。 有关 Azure Stack HCI 上的 Azure Kubernetes 服务的概述，请参阅 [Azure Stack HCI 上的 AKS 概述](overview.md)。

## <a name="determine-hardware-requirements"></a>确定硬件要求

Microsoft 建议从我们的合作伙伴购买经验证的 Azure Stack HCI 硬件/软件解决方案。 这些解决方案是依据我们的参考体系结构设计和汇编的，并且经过了验证，能够确保兼容性和可靠性，因此你可以快速起步和运行。 检查所用的系统、组件、设备和驱动程序是否是通过 Windows Server 目录认证的 Windows Server 2019。 请访问 [Azure Stack HCI 解决方案](https://azure.microsoft.com/overview/azure-stack/hci)网站获取经验证的解决方案。

## <a name="general-requirements"></a>一般要求

若要在 Active Directory 环境中 Azure Stack HCI 或 Windows Server 2019 Datacenter 上的 Azure Kubernetes 服务以最佳方式工作，请确保满足以下要求： 

 - 请确保设置了时间同步，并确保在所有群集节点和域控制器上的不同偏差不超过2分钟。 有关设置时间同步的信息，请参阅 [Windows 时间服务](/windows-server/networking/windows-time-service/windows-time-service-top)。 

 - 请确保在 Azure Stack HCI 或 Windows Server 2019 Datacenter 群集上添加更新并管理 Azure Kubernetes 服务的用户帐户 () 在 Active Directory 中具有正确的权限。 如果使用组织单位 (OU) 管理服务器和服务的组策略，则用户帐户需要针对 OU 中所有对象的列出、读取、修改和删除权限。 

 - 建议在 Azure Stack HCI 或 Windows Server 2019 Datacenter 群集上将 Azure Kubernetes 服务添加到的服务器和服务使用单独的 OU。 使用单独的 OU 可以更细致地控制访问权限和权限。

 - 如果要在 Active Directory 中的容器上使用 GPO 模板，请确保从策略中免除部署 AKS。 后续预览版本将提供服务器强化。

## <a name="compute-requirements"></a>计算要求

 - 群集中最多有四个服务器的 Azure Stack HCI 群集或 Windows Server 2019 Datacenter 故障转移群集。 建议群集中的每个服务器至少有24个 CPU 核心和至少 256 GB RAM。

 - 虽然在技术上可以在单节点 Windows Server 2019 Datacenter 上运行 Azure Kubernetes 服务，但我们不建议这样做。 但是，可以在单个节点 Windows Server 2019 Datacenter 上运行 Azure Kubernetes 服务，以便进行评估。

 - Azure Stack HCI 上的 Azure Kubernetes 服务的其他计算要求与 Azure Stack HCI 的要求一致。 有关 Azure Stack HCI 服务器要求的详细信息，请访问 [Azure Stack HCI 系统需求](../hci/concepts/system-requirements.md#server-requirements)。

 - 此预览版本需要使用 EN-US 区域和语言选择在群集中的每台服务器上安装 Azure Stack HCI 操作系统；目前在安装之后更改它们还不充分。

## <a name="network-requirements"></a>网络要求 

以下要求适用于 Azure Stack HCI 群集以及 Windows Server 2019 Datacenter 群集： 

 - 如果使用 Windows Admin Center，请验证是否已配置了现有的外部虚拟交换机。 对于 Azure Stack HCI 群集，此交换机及其名称在所有群集节点上必须相同。 

 - 验证是否在所有网络适配器上禁用了 IPv6。 

 - 对于成功的部署，Azure Stack HCI 群集节点和 Kubernetes 群集 Vm 必须具有外部 Internet 连接。
  
 - 请确保 Azure Stack HCI 主机和租户 Vm 之间存在网络连接。

 - 若要使所有节点都能够相互通信，需要 DNS 名称解析。 对于 Kubernetes 外部名称解析，请使用 DHCP 服务器在获取 IP 地址时提供的 DNS 服务器。 对于 Kubernetes 内部名称解析，请使用基于 DNS 的默认 Kubernetes 解决方案。 

 - 对于此预览版，我们只为整个部署提供单一 VLAN 支持。 

 - 对于此预览版，我们对通过 PowerShell 创建的 Kubernetes 群集提供了有限的代理支持。 
 
### <a name="ip-address-assignment"></a>IP 地址分配  
 
作为 Azure Stack HCI 部署的成功 AKS 的一部分，我们建议你将虚拟 IP 池范围配置为 DHCP 服务器。 还建议为所有工作负荷群集配置三到五个高度可用的控制平面节点。 

> [!NOTE]
> 不支持单独使用静态 IP 地址分配。 在此预览版本中，你必须配置 DHCP 服务器。

#### <a name="dhcp"></a>DHCP
在整个群集中使用 DHCP 分配 IP 地址时，请遵循以下要求：  

 - 网络必须具有可用的 DHCP 服务器，以便为 Vm 和 VM 主机提供 TCP/IP 地址。 DHCP 服务器还应包含网络时间协议 (NTP) 和 DNS 主机信息。
 
 - 具有专用 IPv4 地址范围的 DHCP 服务器 Azure Stack HCI 群集可访问该服务器。
 
 - DHCP 服务器提供的 IPv4 地址应可路由，并且具有30天的租约过期时间，以避免在 VM 更新或重新设置时丢失 IP 连接。  

至少应保留以下数量的 DHCP 地址：

| 群集类型  | 控制平面节点 | 工作器节点 | 更新 | 负载均衡器  |
| ------------- | ------------------ | ---------- | ----------| -------------|
| AKS 主机 |  1  |  0  |  2  |  0  |
| 工作负荷群集  |  每个节点1个  | 每个节点1个 |  5  |  1  |

你可以根据环境中的工作负荷群集和控制面和辅助角色节点的数量，查看所需 IP 地址的数量如何变化。 建议在 DHCP IP 池中保留256个 (/24 子网) 的 IP 地址。
  
    
#### <a name="vip-pool-range"></a>VIP 池范围

对于 Azure Stack HCI 部署上的 AKS，强烈建议使用虚拟 IP (VIP) 池。 VIP 池是一系列保留的静态 IP 地址，用于长期部署，以确保始终能够访问你的部署和应用程序工作负荷。 目前，我们仅支持 IPv4 地址，因此必须验证是否已在所有网络适配器上禁用了 IPv6。 此外，请确保虚拟 IP 地址不是 DHCP IP 预留的一部分。

至少应为每个群集保留一个 IP 地址 (工作负荷和 AKS 主机) ，并为每个 Kubernetes 服务保留一个 IP 地址。 VIP 池范围中所需的 IP 地址数因环境中的工作负荷群集和 Kubernetes 服务的数量而异。 建议为 AKS 部署保留16个静态 IP 地址。 

设置 AKS 主机时，请使用中的 `-vipPoolStartIp` 和 `-vipPoolEndIp` 参数 `Set-AksHciConfig` 创建 VIP 池。

#### <a name="mac-pool-range"></a>MAC 池范围
建议在范围中至少有16个 MAC 地址，以允许每个群集中有多个控制平面节点。 设置 AKS 主机时，请使用中的 `-macPoolStart` 和 `-macPoolEnd` 参数 `Set-AksHciConfig` 来保留用于 KUBERNETES 服务的 DHCP MAC 池的 mac 地址。
  
### <a name="network-port-and-url-requirements"></a>网络端口和 URL 要求 

在 Azure Stack HCI 上创建 Azure Kubernetes 群集时，会在群集中的每个服务器上自动打开以下防火墙端口。 


| 防火墙端口               | 说明     | 
| ---------------------------- | ------------ | 
| 45000           | wssdagent GPRC   服务器端口     |
| 45001             | wssdagent GPRC 身份验证端口  | 
| 55000           | wssdcloudagent GPRC   服务器端口      |
| 65000            | wssdcloudagent GPRC 身份验证端口  | 


Windows Admin Center 计算机和 Azure Stack HCI 群集中的所有节点都需要防火墙 URL 例外。 

| URL        | 端口 | 服务 | 注释 |
| ---------- | ---- | --- | ---- |
https://helm.sh/blog/get-helm-sh/  | 443 | 下载代理、WAC | 用于下载 Helm 二进制文件 
https://storage.googleapis.com/  | 443 | Cloud Init | 下载 Kubernetes 二进制文件 
https://azurecliprod.blob.core.windows.net/ | 443 | Cloud Init | 下载二进制文件和容器 
https://aka.ms/installazurecliwindows | 443 | WAC | 下载 Azure CLI 
https://:443 | 443 | TCP | 用于支持 Azure Arc 代理  
\* .blob.core.windows.net | 443 | TCP | 下载所需
*.api.cdp.microsoft.com、*.dl.delivery.mp.microsoft.com、*.emdl.ws.microsoft.com | 80、443 | 下载代理 | 下载元数据 
*.dl.delivery.mp.microsoft.com、*.do.dsp.mp.microsoft.com. | 80、443 | 下载代理 | 下载 VHD 映像 
ecpacr.azurecr.io | 443 | Kubernetes | 下载容器映像 
git://:9418 | 9418 | TCP | 用于支持 Azure Arc 代理 

## <a name="storage-requirements"></a>存储要求 

Azure Stack HCI 上的 Azure Kubernetes 服务支持以下存储实现： 

|  名称                         | 存储类型 | 所需容量 |
| ---------------------------- | ------------ | ----------------- |
| Azure Stack HCI 群集          | CSV          | 1 TB              |
| Windows Server 2019 Datacenter 故障转移群集          | CSV          | 1 TB              |
| 单节点 Windows Server 2019 Datacenter | 直接连接的存储 | 500 GB|

## <a name="review-maximum-supported-hardware-specifications"></a>查看支持的最大硬件规格 

超出以下规格的 Azure Stack HCI 上的 Azure Kubernetes 服务部署不受支持： 

| 资源                     | 最大值 |
| ---------------------------- | --------|
| 每个群集的物理服务器数 | 4       |
| Kubernetes 群集            | 4       |
| VM 的总量          | 200     |

## <a name="windows-admin-center-requirements"></a>Windows 管理中心要求

Windows Admin Center 是用于创建和管理 Azure Stack HCI 上的 Azure Kubernetes 服务的用户界面。 若要将 Windows Admin Center 与 Azure Stack HCI 上的 Azure Kubernetes 服务一起使用，必须满足以下列表中的所有条件。 

下面是运行 Windows 管理中心网关的计算机的要求： 

 - Windows 10 或 Windows Server 计算机 (我们目前不支持在 Azure Stack HCI 或 Windows Server 2019 Datacenter 群集上运行 Windows 管理中心) 
 - 60 GB 可用空间
 - 已向 Azure 注册
 - 在 Azure Stack HCI 或 Windows Server 2019 Datacenter 群集所在的同一域中

## <a name="next-steps"></a>后续步骤 

满足上述所有先决条件之后，可以使用以下各项设置 Azure Stack HCI 上的 Azure Kubernetes 服务主机：
 - [Windows 管理中心](setup.md)
 - [PowerShell](setup-powershell.md)