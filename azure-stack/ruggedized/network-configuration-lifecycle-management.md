---
title: Azure Stack 中心耐用网络配置生命周期管理
description: 了解 Azure Stack 集线器耐用设备的网络配置生命周期管理。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/14/2020
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 7fecbd264c5165cf05f494d9a24cb9462f8527ca
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939456"
---
# <a name="azure-stack-hub-ruggedized-network-configuration-lifecycle-management"></a>Azure Stack 中心耐用网络配置生命周期管理

本主题介绍网络配置的生命周期管理。

## <a name="update-configuration"></a>更新配置


一种在字段中更新交换机配置的方法。 这适用于所有类型的配置更新。 由于许多未知变量超出我们的控制范围（例如客户或 OEM 应用的手动配置），因此这将是一个多步手动过程。 目前无法保证正常运行时间，因此应在维护时段内执行更新。

**\# 1-** 备份 tor1、tor2 和 BMC 交换机的当前配置文件。 在交换机中复制这些文件。

**\#2 -** 记下现有配置的工具包生成号。
所有配置都应在 motd 横幅中包含此项。 搜索“BUILDNUMBER”

**\#3 -** 使用步骤 \#2 中的相同工具包版本重新生成初始配置。

**\#4-** 将步骤 \#3 中的配置加载到各自的交换机。
这样做的目的是通过交换机清洗我们的工具生成的配置来获得基线。 这可以由 OEM 在单独的硬件上（如 OEM 实验室中）或客户现场执行。

**\#5 -** 对步骤 \#4 中的配置文件进行备份，并将其复制到远程位置

**\#6 -** 使用所选的差异工具，比较步骤 \#1 中当前配置的备份与步骤 \#5 中基线配置的备份。 记录/复制每个设备应转移到已升级的交换机配置的所有差异内容。

- **\#7 -** 运行新的工具包，它将生成更新的交换机配置。

**\#8 -** 将步骤 6 中的差异合并到新的交换机配置中。

**\#9 -** 将新配置加载到相应的交换机上，并运行工具的输出目录中提供的后验证命令。

**\#10 -** 保存配置。

## <a name="ntp"></a>NTP

可以使用特权终结点 (PEP) 来更新 Azure Stack 中的时间服务器。 使用一个可以解析成两个或多个 NTP 服务器 IP 地址的主机名。

Azure Stack 使用网络时间协议 (NTP) 连接到 Internet 上的时间服务器。 NTP 服务器提供准确的系统时间。 Azure Stack 的物理网络交换机、硬件生命周期主机、基础结构服务和虚拟机都使用时间。 如果时钟未同步，Azure Stack 可能会遇到严重的网络和身份验证问题。 在创建日志文件、文档和其他文件时，时间戳可能会不正确。

必须提供一个时间服务器 (NTP)，这样 Azure Stack 才能同步时间。
部署 Azure Stack 时，请提供 NTP 服务器的地址。 时间是重要的数据中心基础结构服务。 如果服务更改，则需更新时间。

Azure Stack 支持只与一个时间服务器 (NTP) 同步时间。 不能提供多个 NTP 供 Azure Stack 与其同步时间。 建议设置可解析为多个 NTP 服务器的 DNS 条目。 

### <a name="update-ntp-post-deployment"></a>在部署后更新 NTP

1.  连接到特权终结点 (PEP)。 无需开具支持票证即可解锁特权终结点。 |

2.  运行以下命令即可查看当前的已配置 NTP 服务器：

    ```powershell
    Get-AzsTimeSource
    ```

3.  运行以下命令，将 Azure Stack 更新为使用新的 NTP 服务器并立即同步时间：

    ```powershell
    Set-AzsTimeSource -Timeserver NEWTIMESERVER -resync
    ```

    >[!NOTE] 
    > 此过程不会更新物理交换机上的时间服务器。 

## <a name="dns"></a>DNS

### <a name="update-the-dns-forwarder-in-azure-stack"></a>在 Azure Stack 中更新 DNS 转发器

Azure Stack 基础结构至少需要一个可访问的 DNS 转发器来解析外部名称。 必须提供 DNS 转发器才能部署 Azure Stack。 该输入在 Azure Stack 内部 DNS 服务器中用作转发器，并为身份验证、市场管理或使用情况等服务启用外部名称解析。

DNS 是一项可更改的关键数据中心基础结构服务，如果 DNS 更改，则必须更新 Azure Stack。

本文介绍如何使用特权终结点 (PEP) 在 Azure Stack 中更新 DNS 转发器。 建议使用两个可靠的 DNS 转发器 IP 地址。

1.  连接到特权终结点 (PEP)。 无需开具支持票证即可解锁特权终结点。 

2.  运行以下命令，查看当前配置的 DNS 转发器。 或者，也可以使用管理门户区域属性：

    ```powershell
    Get-AzsDnsForwarder
    ```

3.  运行以下命令更新 Azure Stack，以使用新的 DNS 转发器：

    ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1", "IPAddress 2" 
    ```

4.  请查看命令输出中是否有错误。