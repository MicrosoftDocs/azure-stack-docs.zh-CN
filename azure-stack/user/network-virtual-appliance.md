---
title: 排查 Azure Stack 集线器上的网络虚拟设备问题
description: 排查 Microsoft Azure Stack 集线器中使用第三方网络虚拟设备（NVA）时的 VM 或 VPN 连接问题和错误。
author: sethmanheim
ms.author: sethm
ms.date: 05/12/2020
ms.topic: article
ms.reviewer: sranthar
ms.lastreviewed: 05/12/2020
ms.openlocfilehash: 5e42f9e1b099df2ab1dc1063b3bf2710f59ad66f
ms.sourcegitcommit: 999c6cd0ab64cd2d695feb8405a9c720c9ae755b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/13/2020
ms.locfileid: "83342889"
---
# <a name="troubleshoot-network-virtual-appliance-issues"></a>排查网络虚拟设备问题

在 Microsoft Azure Stack 集线器中使用第三方网络虚拟设备（NVA）时，可能会遇到 VM 或 VPN 连接问题和错误。 本文提供了一些基本步骤，可帮助你验证 NVA 配置的基本 Azure Stack 集线器平台要求。

NVA 供应商提供了第三方 Nva 的技术支持以及与 Azure Stack 中心平台的集成。

> [!NOTE]
> 如果遇到涉及 NVA 的连接或路由问题，则应直接[联系 NVA 的供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

如果本文未解决 Azure Stack 中心 NVA 问题，请创建[Azure Stack 中心支持票证](../operator/azure-stack-manage-basics.md#where-to-get-support)。

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>与 NVA 供应商合作进行故障排除时的清单

- NVA VM 软件的软件更新。
- 服务帐户的设置和功能。
- 虚拟网络子网中的用户定义路由（Udr），用于将流量定向到 NVA。
- Udr，用于将流量定向到 NVA 的虚拟网络子网。
- 在 NVA 内路由表和规则（例如，从 NIC1 到 NIC2）。
- 跟踪 NVA Nic 以验证接收和发送网络流量。

## <a name="basic-troubleshooting-steps"></a>基本故障排除步骤

- 检查基本配置。
- 检查 NVA 性能。
- 高级网络故障排除。

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>检查 Azure 上 NVA 的最低配置要求

每个 NVA 都有基本的配置要求，以便在 Azure Stack 集线器上正常运行。 以下部分提供了验证这些基本配置的步骤。 有关详细信息，请[联系 NVA 的供应商](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

> [!IMPORTANT]
> 使用 S2S 隧道时，数据包将与其他标头进一步封装。 此封装增加了数据包的总大小。 在这些情况下，必须将 TCP **MSS**固定在**1350**。 如果 VPN 设备不支持 MSS 钳位，则可以改为将隧道接口上的 MTU 设置为**1400**字节。 有关详细信息，请参阅[虚拟网络 TCPIP 性能优化](/azure/virtual-network/virtual-network-tcpip-performance-tuning)。

### <a name="check-whether-ip-forwarding-is-enabled-on-nva"></a>检查 NVA 是否启用了 IP 转发

#### <a name="use-the-azure-stack-hub-portal"></a>使用 Azure Stack 中心门户

- 在 Azure Stack 中心门户中找到 NVA 资源，选择 "**网络**"，然后选择网络接口。
- 在 "**网络接口**" 页上，选择 " **IP 配置**"。
- 确保已启用 IP 转发。

#### <a name="use-powershell"></a>使用 PowerShell

- 运行以下命令（用你的信息替换括号中的值）：

   ```powershell
   Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   ```

- 检查“EnableIPForwarding”属性****。
- 如果未启用 IP 转发，请运行以下命令将其启用：

   ```powershell
   $nic2 = Get-AzureRMNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NIC name>
   $nic2.EnableIPForwarding = 1
   Set-AzureRMNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

### <a name="check-whether-the-traffic-can-be-routed-to-the-nva"></a>检查是否可以将流量路由到 NVA。

- 找到配置为将流量重定向到 NVA 的 VM
- 运行 `Tracert <Private IP of NVA>` For Windows，或 `Traceroute <Private IP of NVA>` 检查 NVA 是否为下一跃点。
- 如果 NVA 未作为下一个跃点列出，请检查并更新 Azure Stack 中心路由表。

某些来宾级别的操作系统可能会设置防火墙策略来阻止 ICMP 通信。 要使前面的命令正常工作，必须更新这些防火墙规则。

### <a name="check-whether-the-traffic-can-reach-the-nva"></a>检查流量是否可到达 NVA

- 找到应连接到 NVA 的虚拟机。
- 为 `ping (ICMP)` `Test-NetConnection (TCP) <Private IP of NVA>` Windows 和 Linux 运行或 `Tcpping <Private IP of NVA>` ，以检查流量是否被任何网络安全组阻止。
- 如果流量被阻止，请修改 Nsg 以允许流量。

### <a name="check-whether-nva-and-vms-are-listening-for-expected-traffic"></a>检查 NVA 和 VM 是否正在侦听预期的流量

- 使用 RDP 或 SSH 连接到 NVA，然后运行以下命令：

   Windows  ：
   ```shell
   netstat -an
   ```

   **Linux**：
   ```shell
   netstat -an | grep -i listen
   ```

- 如果看不到结果中列出的 NVA 软件所使用的 TCP 端口，则必须在 NVA 和 VM 上配置该应用程序，以便侦听和响应到达这些端口的流量。 [如有需要，请联系 NVA 供应商以获取帮助](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

## <a name="check-nva-performance"></a>检查 NVA 性能

### <a name="validate-vm-cpu"></a>验证 VM CPU

如果 CPU 使用率接近 100%，则可能会遇到造成网络数据包丢失的问题。 在 CPU 高峰期间，调查来宾 VM 上哪个进程导致高 CPU，并在可能的情况下将其缓解。 你可能还必须将 VM 大小调整为更大的 SKU 大小，或者，对于虚拟机规模集，增加实例计数。 对于上述任意问题，如有需要[请联系 NVA 供应商以获取帮助](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

### <a name="validate-vm-network-statistics"></a>验证 VM 网络统计信息

如果 VM 网络使用峰值或显示较高使用率，你可能还需要增加 VM 的 SKU 大小以获得更高的吞吐量功能。

## <a name="advanced-network-administrator-troubleshooting"></a>高级网络管理员故障排除

### <a name="capture-network-trace"></a>捕获网络跟踪

运行 **PsPing** 或 Nmap 时，请在源 VM、NVA 和目标 VM 上捕获同步网络跟踪，并停止跟踪[](/sysinternals/downloads/psping)****。

- 若要捕获同步网络跟踪，请运行以下命令：

   **Windows**

   ```shell
   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection
   ```

   **Linux**

   ```shell
   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap
   ```

- 使用从源 VM 到目标 VM 的 PsPing 或 Nmap（例如：`PsPing 10.0.0.4:80` 或 `Nmap -p 80 10.0.0.4`）********。

- 使用**tcpdump**或所选的数据包分析器从目标 VM 打开网络跟踪。 将显示筛选器应用于运行**PsPing**或**NMAP**的源 VM 的 IP，如 `IPv4.address==10.0.0.4` （Windows netmon）或 `tcpdump -nn -r vmtrace.cap src` 或 `dst host 10.0.0.4` （Linux）。

### <a name="analyze-traces"></a>分析跟踪

如果看不到后端 VM 跟踪的数据包，可能是 NSG 或 UDR，或者 NVA 路由表不正确。

如果看到数据包传入但没有响应，则可能需要解决 VM 应用程序或防火墙问题。 对于上述任意问题，如有需要[请联系 NVA 供应商以获取帮助](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)。

### <a name="create-a-support-ticket"></a>创建支持票证

如果前面的步骤都无法解决你的问题，请创建[支持票证](../operator/azure-stack-manage-basics.md#where-to-get-support)，并使用按[需日志收集工具](../operator/azure-stack-configure-on-demand-diagnostic-log-collection.md)来提供日志。
