---
title: Azure Stack HCI 要求的 Azure Kubernetes 服务
description: 在 Azure Stack HCI 上开始 Azure Kubernetes 服务之前
ms.topic: conceptual
author: abhilashaagarwala
ms.author: abha
ms.date: 09/22/2020
ms.openlocfilehash: b4f79281987b425b907126081e74ce32bf9ee14d
ms.sourcegitcommit: dabbe44c3208fbf989b7615301833929f50390ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90948842"
---
# <a name="system-requirements-for-azure-kubernetes-service-on-azure-stack-hci"></a>Azure Stack HCI 上的 Azure Kubernetes 服务的系统要求

> 适用于： Azure Stack HCI

本文介绍了在 Azure Stack HCI 上设置 Azure Kubernetes 服务并使用它创建 Kubernetes 群集的要求。 有关 Azure Stack HCI 上的 Azure Kubernetes 服务的概述，请参阅 [AZURE STACK HCI 概述上的 AKS](overview.md)。

## <a name="determine-hardware-requirements"></a>确定硬件要求

Microsoft 建议从我们的合作伙伴购买经验证的 Azure Stack HCI 硬件/软件解决方案。 针对我们的参考体系结构设计、汇编和验证这些解决方案，以确保兼容性和可靠性，使你能够快速启动和运行。 检查所用的系统、组件、设备和驱动程序是否是通过 Windows Server 目录认证的 Windows Server 2019。 请访问 [AZURE STACK HCI 解决方案](https://azure.microsoft.com/overview/azure-stack/hci) 网站获取经验证的解决方案。

### <a name="general-requirements"></a>一般要求

Azure Stack HCI 上的 Azure Kubernetes 服务在 Active Directory 环境中以最佳方式运行，请确保满足以下要求： 

 - 请确保在所有群集节点和域控制器上设置时间同步并且分歧不超过2分钟。 有关设置时间同步的信息，请访问 [Windows 时间服务](/windows-server/networking/windows-time-service/windows-time-service-top)。 

 - 确保 () 在 Azure Stack HCI 群集上添加、更新和管理 Azure Kubernetes 服务的用户帐户在 Active Directory 中具有正确的权限。 如果使用组织单位 (Ou) 管理服务器和服务的组策略，则 () 的用户帐户将需要对 OU 中所有对象的 "列出"、"读取"、"修改" 和 "删除" 权限。 

 - 对于将 Azure Stack HCI 群集上的 Azure Kubernetes 服务添加到的服务器和服务，我们建议使用单独的 OU。 这样，你就可以更细致地控制访问权限和权限。

 - 如果在 Active Directory 中的容器上使用 GPO 模板，请确保部署 AKS。 后续预览版本将提供服务器强化。

### <a name="compute-requirements"></a>计算要求

 - 群集中最多有四个服务器的 Azure Stack HCI 群集。 建议群集中的每个服务器至少有24个 CPU 核心和至少 512 GB RAM。

 - 虽然在技术上可以在 Azure Stack HCI 服务器的单个节点上运行 Azure Kubernetes 服务，但我们不建议这样做。

 - Azure Stack HCI 上的 Azure Kubernetes 服务的其他计算要求符合 Azure Stack HCI 的要求。 有关 Azure Stack HCI 服务器要求的详细信息，请访问 [AZURE STACK Hci 要求](../hci/deploy/before-you-start.md) 。  

 - 此预览版需要在群集中的每个服务器上安装 Azure Stack HCI 操作系统，使用 EN-US 区域和语言选择;现在，在安装后对其进行更改并不充分。

### <a name="network-requirements"></a>网络要求 

Azure Stack HCI 上的 Azure Kubernetes 服务需要在每个服务器节点之间进行可靠的高带宽、低延迟的网络连接。 应该验证以下各项： 

 - 如果使用的是 Windows 管理中心，请验证是否已配置了现有的外部虚拟交换机。 对于 Azure Stack HCI 群集，此开关在所有群集节点上必须相同。 

 - 验证是否在所有网络适配器上禁用了 IPv6。 

 - 网络必须具有可用的 DHCP 服务器，才能向 Vm 和 VM 主机提供 TCP/IP 地址。 DHCP 服务器还应包含 NTP 和 DNS 主机信息。 

 - 此外，我们还建议使用具有专用 IPv4 地址范围的 DHCP 服务器，Azure Stack HCI 群集可以访问这些地址。 例如，可以保留默认网关的10.0.1.1，将10.0.1.2 设置为 10.0.1.102 for Kubernetes services，并将 10.0.1.103-10.0.1.254 用于 Kubernetes 群集 Vm。 

 - DHCP 服务器提供的 IPv4 地址应可路由，并且具有7天租约过期时间，以避免在 VM 更新或重新设置时丢失 IP 连接。  

 - 建议不要使用 VLAN 标记。 在 Azure Stack HCI 群集网络交换机上使用访问或未标记的端口。 

 - 在安装过程中，我们不建议使用负载平衡器虚拟 IP 池的专用静态虚拟 IP 池。 DHCP IP 池用于虚拟机，而虚拟 IP 池用于负载均衡器，并且需要可路由。 不需要将 DHCP IP 池路由到外部 internet。

 - 要使所有节点能够相互通信，需要 DNS 名称解析。 对于 Kubernetes 外部名称解析，我们使用 DHCP 服务器在获取 IP 地址时提供的 DNS 服务器。 对于 Kubernetes 内部名称解析，我们将使用基于 DNS 的默认 Kubernetes 解决方案。 

 - 对于此预览版，我们不支持使用代理服务器将 Windows 管理中心、Azure Stack HCI 群集节点和 Azure Stack HCI 群集节点上的 Azure Kubernetes 服务连接到 internet。

### <a name="network-port-and-url-requirements"></a>网络端口和 URL 要求 

在 Azure Stack HCI 上创建 Azure Kubernetes 群集时，将在群集中的每个服务器上自动打开以下防火墙端口。 


| 防火墙端口               | 说明         | 
| ---------------------------- | ------------ | 
| 45000           | wssdagent GPRC 服务器端口           |
| 45001             | wssdagent GPRC authentication 端口  | 
| 55000           | wssdcloudagent GPRC 服务器端口           |
| 55001             | wssdcloudagent GPRC authentication 端口  | 


Windows 管理中心计算机和 Azure Stack HCI 群集中的所有节点都需要防火墙 URL 例外。 

| URL        | 端口 | 服务 | 说明 |
| ---------- | ---- | --- | ---- |
https://get.helm.sh/  | 443 | 下载代理，WAC | 用于下载 Helm 二进制文件 
https://storage.googleapis.com/  | 443 | 云初始化 | 下载 Kubernetes 二进制文件 
https://azurecliprod.blob.core.windows.net/ | 443 | 云初始化 | 下载二进制文件和容器 
https://aka.ms/installazurecliwindows | 443 | WAC | 下载 Azure CLI 
*. api.cdp.microsoft.com、*. dl.delivery.mp.microsoft.com、emdl.ws.microsoft.com | 80、443 | 下载代理 | 下载元数据 
*. dl.delivery.mp.microsoft.com、*. do.dsp.mp.microsoft.com。 | 80、443 | 下载代理 | 下载 VHD 映像 
ecpacr.azurecr.io | 443 | Kubernetes | 下载容器映像 

### <a name="storage-requirements"></a>存储要求 

Azure Stack HCI 上的 Azure Kubernetes 服务支持以下存储实现： 

|  名称                         | 存储类型 | 所需容量 |
| ---------------------------- | ------------ | ----------------- |
| Azure Stack HCI 群集          | CSV          | 1 TB              |
| 单节点 Azure Stack HCI | 直接连接的存储 | 500 GB|

### <a name="review-maximum-supported-hardware-specifications"></a>查看支持的最大硬件规格 

在 Azure Stack 超出以下规范的 HCI 部署上的 Azure Kubernetes 服务不受支持： 

| 资源                     | 最大值 |
| ---------------------------- | --------|
| 每个群集的物理服务器数 | 4       |
| Kubernetes 群集            | 4       |
| VM 的总量          | 200     |

### <a name="windows-admin-center"></a>Windows Admin Center 

Windows 管理中心是用于在 Azure Stack HCI 上创建和管理 Azure Kubernetes 服务的用户界面。 若要在 Azure Stack HCI 上将 Windows 管理中心与 Azure Kubernetes 服务一起使用，必须满足以下列表中的所有条件。 

#### <a name="on-your-windows-admin-center-system"></a>在 Windows 管理中心系统上

运行 Windows 管理中心网关的计算机必须： 

 - Windows 10 (当前不支持 Windows 管理中心服务器) 
 - 60 GB 可用空间
 - 注册到 Azure
 - 与 Azure Stack HCI 群集位于同一个域中

## <a name="next-steps"></a>后续步骤 

满足上述所有先决条件后，可以使用以下操作在 Azure Stack HCI 上设置 Azure Kubernetes 服务主机：
 - [Windows 管理中心](setup.md)
 - [PowerShell](setup-powershell.md)
