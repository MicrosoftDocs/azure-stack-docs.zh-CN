---
title: 排查 Azure Stack 集线器上的网络虚拟设备问题
description: 在 Microsoft Azure Stack 集线器中使用网络虚拟设备（NVA）时解决 VM 或 VPN 连接问题。
author: sethmanheim
ms.author: sethm
ms.date: 05/12/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: f933e9c4e70f533d4194b48c7b9e4d6e4bf380b0
ms.sourcegitcommit: d5d89bbe8a3310acaff29a7a0cd7ac4f2cf5bfe7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83554958"
---
# <a name="troubleshoot-network-virtual-appliance-problems"></a>排查网络虚拟设备问题

在 Azure Stack 集线器中使用网络虚拟设备（NVA）的虚拟机或 Vpn 可能会遇到连接问题。

本文介绍了一些步骤，可帮助你验证 Azure Stack 集线器对于 NVA 配置的基本平台要求。

NVA 的供应商为 NVA 及其与 Azure Stack 中心平台的集成提供技术支持。

> [!NOTE]
> 如果有涉及 NVA 的连接或路由问题，则应直接[联系 NVA 供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

如果本文未解决 Azure Stack 中心的 NVA 问题，请创建[Azure Stack 中心支持票证](../operator/azure-stack-manage-basics.md#where-to-get-support)。

## <a name="checklist-for-troubleshooting-with-an-nva-vendor"></a>用于 NVA 供应商的故障排除清单

- NVA VM 软件的更新。
- 服务帐户的设置和功能。
- 虚拟网络子网中的用户定义路由（Udr），用于将流量定向到 NVA。
- Udr，用于将流量定向到 NVA 的虚拟网络子网。
- 在 NVA 内路由表和规则（例如，从 NIC1 到 NIC2）。
- 跟踪 NVA Nic 以验证接收和发送网络流量。

## <a name="basic-troubleshooting-steps"></a>基本故障排除步骤

1. 检查基本配置。
1. 检查 NVA 性能。
1. 进行高级网络故障排除。

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>检查 Azure 上 NVA 的最低配置要求

每个 NVA 都必须满足基本配置要求才能在 Azure Stack 集线器上正常运行。 本部分介绍验证这些基本配置的步骤。 有关详细信息，请[与 NVA 供应商联系](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

> [!IMPORTANT]
> 当数据包使用 S2S 隧道时，它们将与其他标头进一步封装。 此封装增加了每个数据包的总大小。
>
> 在这种情况下，必须将 TCP MSS 固定到1350字节。 如果 VPN 设备不支持 MSS 钳位，可以改为将隧道接口上的 MTU 设置为1400字节。
>
> 有关详细信息，请参阅[虚拟网络 tcp/ip 性能优化](/azure/virtual-network/virtual-network-tcpip-performance-tuning)。

### <a name="check-whether-ip-forwarding-is-enabled-on-the-nva"></a>检查是否已在 NVA 上启用 IP 转发

#### <a name="use-the-azure-stack-hub-portal"></a>使用 Azure Stack 中心门户

1. 在 Azure Stack 中心门户中找到 NVA 资源，选择 "**网络**"，然后选择网络接口。
1. 在 "**网络接口**" 页上，选择 " **IP 配置**"。
1. 确保已启用 IP 转发。

#### <a name="use-powershell"></a>使用 PowerShell

1. 运行以下命令。 将尖括号中的值替换为你的信息。

   ```powershell
   Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

1. 检查“EnableIPForwarding”属性****。
1. 如果未启用 IP 转发，请运行以下命令将其启用：

   ```powershell
   $nic2 = Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzureRMNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

### <a name="check-whether-traffic-can-be-routed-to-the-nva"></a>检查是否可以将流量路由到 NVA

1. 找到配置为将流量重定向到 NVA 的虚拟机。
1. 若要检查 NVA 是否为下一个跃点，请运行** \< NVA \> For Windows 的 Tracert 专用 ip**或** \< NVA \> 的 Traceroute 专用 ip**。
1. 如果 NVA 未列为下一跃点，请检查并更新 Azure Stack 中心路由表。

某些来宾级别的操作系统可能会设置防火墙策略来阻止 ICMP 通信。 更新这些防火墙规则，以使前面的命令正常工作。

### <a name="check-whether-traffic-can-reach-the-nva"></a>检查流量是否可以访问 NVA

1. 找到应连接到 NVA 的虚拟机。
1. 检查是否有任何网络安全组（Nsg）阻止流量。 对于 Windows，请运行**ping** （ICMP）或 NVA ** \< \> （TCP）的 test-netconnection 专用 IP** 。 对于 Linux，请**运行 \< NVA \> 的 Tcpping 专用 IP**。
1. 如果你的 Nsg 阻止流量，请将其修改为允许流量。

### <a name="check-whether-the-nva-and-vms-are-listening-for-expected-traffic"></a>检查 NVA 和 Vm 是否正在侦听预期流量

1. 使用 RDP 或 SSH 连接到 NVA，并运行以下命令：

   **Windows**

   ```shell
   netstat -an
   ```

   **Linux**

   ```shell
   netstat -an | grep -i listen
   ```

1. 查找结果中列出的 NVA 软件所使用的 TCP 端口。 如果看不到这些应用程序，请在 NVA 和 VM 上配置该应用程序，以便侦听和响应到达这些端口的流量。 [请与 NVA 供应商联系以获得帮助](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

## <a name="check-nva-performance"></a>检查 NVA 性能

### <a name="validate-vm-cpu-usage"></a>验证 VM CPU 使用情况

如果 CPU 使用率接近100%，则可能会遇到影响网络数据包丢弃的问题。

在 CPU 高峰期间，调查来宾 VM 上哪个进程导致 CPU 使用率高。 如果可能，请缓解使用。

你可能还需要将 VM 大小调整为更大的 SKU 大小，或者，对于虚拟机规模集，增加实例计数。

如果需要帮助，请[与 NVA 供应商联系](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

### <a name="validate-vm-network-statistics"></a>验证 VM 网络统计信息

如果 VM 网络使用峰值或显示较高使用率，请考虑增加 VM 的 SKU 大小以获得更高的吞吐量。

## <a name="advanced-network-administrator-troubleshooting"></a>高级网络管理员故障排除

### <a name="capture-a-network-trace"></a>捕获网络跟踪

运行[**PsPing**](/sysinternals/downloads/psping)或**Nmap**时，请在源 vm 和目标 vm 以及 NVA 上捕获同时进行的网络跟踪。 然后停止跟踪。

1. 若要捕获同步网络跟踪，请运行以下命令：

   **Windows**

   ```shell
   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection
   ```

   **Linux**

   ```shell
   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap
   ```

2. 从源 VM 向目标 VM 使用**PsPing**或**Nmap** 。 示例包括**PsPing 10.0.0.4： 80**或**Nmap-p 80 10.0.0.4**。

3. 使用**tcpdump**或所选的数据包分析器从目标 VM 打开网络跟踪。 为运行**PsPing**或**NMAP**的源 VM 的 IP 应用显示筛选器。 Windows **netmon**示例为**IPv4. address = = 10.0.0.4**。 Linux 示例是**tcpdump-nn-r vmtrace.cap src**和**dst host 10.0.0.4**。

### <a name="analyze-traces"></a>分析跟踪

如果看不到数据包进入后端 VM 跟踪，则可能是 NSG 或 UDR，或者 NVA 路由表不正确。

如果看到数据包传入但没有响应，则可能需要解决 VM 应用程序或防火墙的问题。

如果需要帮助，请[与 NVA 供应商联系](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

### <a name="create-a-support-ticket"></a>创建支持票证

如果上述步骤无法解决问题，请创建[支持票证](../operator/azure-stack-manage-basics.md#where-to-get-support)，并使用按[需日志收集工具](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md)来提供日志。
