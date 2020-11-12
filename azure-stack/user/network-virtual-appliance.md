---
title: 排查 Azure Stack Hub 上的网络虚拟设备问题
description: 排查在 Microsoft Azure Stack Hub 中使用网络虚拟设备 (NVA) 时遇到的 VM 或 VPN 连接问题。
author: sethmanheim
ms.author: sethm
ms.date: 09/08/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: 0facc0cc06ad3ff672531f1eeb7e31eee2f56ee0
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546882"
---
# <a name="troubleshoot-network-virtual-appliance-problems"></a>排查网络虚拟设备问题

在 Azure Stack Hub 中使用网络虚拟设备 (NVA) 的虚拟机或 VPN 可能会出现连接问题。

本文可帮助你验证 Azure Stack Hub 的基本平台要求（针对 NVA 配置）。

NVA 的供应商为 NVA 及其与 Azure Stack Hub 平台的集成提供技术支持。

> [!NOTE]
> 如果遇到涉及 NVA 的连接或路由问题，则应直接[联系 NVA 供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

如果本文未解决你在 Azure Stack Hub 中遇到的 NVA 问题，请创建 [Azure Stack Hub 支持票证](../operator/azure-stack-manage-basics.md#where-to-get-support)。

## <a name="checklist-for-troubleshooting-with-an-nva-vendor"></a>与 NVA 供应商合作进行故障排除时的清单

- NVA VM 软件的更新。
- 服务帐户设置和功能。
- 虚拟网络子网上用户定义的路由 (UDR)，用于将流量定向到 NVA。
- 虚拟网络子网上的 UDR，用于定向来自 NVA 的流量。
- NVA 内的路由表和规则（例如，从 NIC1 到 NIC2）。
- 在 NVA NIC 上进行跟踪以验证网络流量的接收和发送。

## <a name="basic-troubleshooting-steps"></a>基本故障排除步骤

1. 检查基本配置。
2. 检查 NVA 性能。
3. 进行高级网络故障排除。

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>检查 Azure 上 NVA 的最低配置要求

每个 NVA 都必须满足基本配置要求才能在 Azure Stack Hub 上正常运行。 此部分展示了验证这些基本配置的步骤。 有关详细信息，请[联系 NVA 供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

> [!IMPORTANT]
> 当数据包使用 S2S 隧道时，它们会与其他标头一起进一步进行封装。 此封装会增加每个数据包的总大小。
>
> 在这种情况下，必须将 TCP MSS 固定在 1,350 字节。 如果 VPN 设备不支持 MSS 钳位，则可以改为将隧道接口上的 MTU 设置为 1,400 字节。
>
> 有关详细信息，请参阅[虚拟网络 TCP/IP 性能优化](/azure/virtual-network/virtual-network-tcpip-performance-tuning)。

### <a name="check-whether-ip-forwarding-is-enabled-on-the-nva"></a>检查是否在 NVA 上启用了 IP 转发

#### <a name="use-the-azure-stack-hub-portal"></a>使用 Azure Stack Hub 门户

1. 在 Azure Stack Hub 门户中找到 NVA 资源，选择“网络”，然后选择网络接口。
2. 在“网络接口”页上，选择“IP 配置”。 
3. 确保已启用 IP 转发。

#### <a name="use-powershell"></a>使用 PowerShell

1. 运行以下命令。 将尖括号中的值替换为你的信息。

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

2. 检查“EnableIPForwarding”属性。

3. 如果未启用 IP 转发，请运行以下命令将其启用：

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

### <a name="check-whether-traffic-can-be-routed-to-the-nva"></a>检查流量是否可路由到 NVA

1. 找到一个已配置为将流量重定向到 NVA 的 VM。
1. 若要检查 NVA 是否为下一个跃点，请运行 `Tracert <Private IP of NVA>`（适用于 Windows）或 `Traceroute <Private IP of NVA>`。
1. 如果 NVA 未列为下一跃点，请检查并更新 Azure Stack Hub 路由表。

某些来宾级别的操作系统可能会设置防火墙策略来阻止 ICMP 通信。 请更新这些防火墙规则，使前面的命令生效。

### <a name="check-whether-traffic-can-reach-the-nva"></a>检查流量是否可到达 NVA

1. 找到一个应该能够连接到 NVA 的 VM。
1. 检查是否有任何网络安全组 (NSG) 阻止了流量。 对于 Windows，请运行 `ping` (ICMP) 或 `Test-NetConnection <Private IP of NVA>` (TCP)。 对于 Linux，请运行 `Tcpping <Private IP of NVA>`。
1. 如果你的 NSG 阻止流量，请将其修改为允许流量。

### <a name="check-whether-the-nva-and-vms-are-listening-for-expected-traffic"></a>检查 NVA 和 VM 是否正在侦听预期的流量

1. 使用 RDP 或 SSH 连接到 NVA，然后运行以下命令：

   **Windows**

   ```shell
   netstat -an
   ```

   **Linux**

   ```shell
   netstat -an | grep -i listen
   ```

1. 查找结果中列出的 NVA 软件所使用的 TCP 端口。 如果未看到它们，请在 NVA 和 VM 上配置应用程序，以侦听并响应到达这些端口的流量。 [联系 NVA 供应商以获取帮助](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

## <a name="check-nva-performance"></a>检查 NVA 性能

### <a name="validate-vm-cpu-usage"></a>验证 VM CPU 使用情况

如果 CPU 使用率接近 100%，那么可能会出现造成网络数据包丢失的问题。

在 CPU 峰值期间，调查来宾 VM 上的哪个进程导致 CPU 使用率过高， 然后在可能的情况下缓解该使用率问题。

可能还需将 VM 大小重设为更大的 SKU 大小；或者，对于虚拟机规模集，增加实例计数。

如需帮助，请[联系 NVA 供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

### <a name="validate-vm-network-statistics"></a>验证 VM 网络统计信息

如果 VM 网络使用情况出现高峰或显示高使用率时段，请考虑增加 VM 的 SKU 大小以获得更高的吞吐量。

## <a name="advanced-network-administrator-troubleshooting"></a>高级网络管理员故障排除

### <a name="capture-a-network-trace"></a>捕获网络跟踪

运行 [`PsPing`](/sysinternals/downloads/psping) 或 `Nmap` 时，请在源 VM、目标 VM 和 NVA 上捕获同步网络跟踪， 然后停止跟踪。

1. 若要捕获同步网络跟踪，请运行以下命令：

   **Windows**

   ```shell
   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection
   ```

   **Linux**

   ```shell
   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap
   ```

2. 使用从源 VM 到目标 VM 的 PsPing 或 Nmap`PsPing` `Nmap`。 例如 `PsPing 10.0.0.4:80` 或 `Nmap -p 80 10.0.0.4`。

3. 使用 **tcpdump** 或所选数据包分析器从目标 VM 打开网络跟踪。 为运行 PsPing 或 Nmap 的源 VM 的 IP 应用一个显示筛选器`PsPing` `Nmap`。 `IPv4.address==10.0.0.4` 是 Windows **netmon** 示例。 Linux 示例为 `tcpdump -nn -r vmtrace.cap src` 和 `dst host 10.0.0.4`。

### <a name="analyze-traces"></a>分析跟踪

如果看不到数据包传入到后端 VM 跟踪中，原因可能是存在 NSG 或 UDR 干扰或是 NVA 路由表不正确。

如果看到数据包传入但没有响应，则可能需要解决 VM 应用程序或防火墙问题。

如需帮助，请[联系 NVA 供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

### <a name="create-a-support-ticket"></a>创建支持票证

如果前面的步骤无法解决问题，请创建[支持票证](../operator/azure-stack-manage-basics.md#where-to-get-support)并使用[按需日志收集工具](../operator/azure-stack-diagnostic-log-collection-overview.md)来提供日志。
