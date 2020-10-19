---
title: MDC 网络配置生命周期管理
description: 了解 MDC 设备的网络配置生命周期管理。
author: justinha
ms.author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 78e4a98a606b100e42d4beb465874d4d0648c971
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182861"
---
# <a name="configuration-lifecycle-management"></a>配置生命周期管理

本主题介绍网络配置的生命周期管理。

## <a name="update-configuration"></a>更新配置


用于在字段中更新交换机配置的方法。 这适用于所有类型的配置更新。 由于控件之外的许多未知变量（如客户或 OEM 应用的手动配置），这将是一个多步骤的手动过程。 目前，在维护时段内不能保证运行时间和更新。

** \# 1-** 备份 edg1、edge2、tor1、tor2 和 BMC 交换机的当前配置文件。 将这些文件复制到开关。

** \# 2-** 记下现有配置的工具包生成号。
所有配置的 **motd** 横幅都应有此项。 搜索 *"BUILDNUMBER"*

** \# 3-** 使用步骤 \# 中的相同工具包版本重新生成初始配置pps-2.

** \# 4-** 将步骤3中的配置加载 \# 到各自的开关上。
这一点是通过交换机来使我们的工具所生成的配置 *冲蚀* ，以获取基线。 这可以由 OEM 在单独的硬件上（如 OEM 实验室中）或客户现场执行。

** \# 5-** 对步骤4中的配置文件进行备份 \# ，并将其复制到远程位置

** \# 6-** 使用您选择的差异工具比较步骤1中当前配置的备份与 \# 步骤5中的基线配置的备份 \# 。 为每个设备的已升级交换机配置提供应执行的所有差异的注释/副本。

** \# 7-** 运行将生成更新的交换机配置的新工具包。

** \# 8-** 将步骤6中的差异合并为新的交换机配置。

** \# 9-** 将新配置加载到各个交换机上，并运行工具的输出目录中提供的 post 验证命令。

** \# 10-** 保存配置。

## <a name="ntp"></a>NTP

可以使用特权终结点 (PEP) 来更新 Azure Stack 中的时间服务器。 使用一个可以解析成两个或多个 NTP 服务器 IP 地址的主机名。

Azure Stack 使用网络时间协议 (NTP) 连接到 Internet 上的时间服务器。 NTP 服务器提供准确的系统时间。 Azure Stack 的物理网络交换机、硬件生命周期主机、基础结构服务和虚拟机都使用时间。 如果时钟未同步，Azure Stack 可能会遇到严重的网络和身份验证问题。 在创建日志文件、文档和其他文件时，时间戳可能会不正确。

必须提供一个时间服务器 (NTP)，这样 Azure Stack 才能同步时间。
部署 Azure Stack 时，请提供 NTP 服务器的地址。 时间是重要的数据中心基础结构服务。 如果服务更改，则需更新时间。

Azure Stack 支持只与一个时间服务器 (NTP) 同步时间。 不能提供多个 NTP 供 Azure Stack 与其同步时间。 建议设置可解析为多台 NTP 服务器的 DNS 条目。 


### <a name="update-ntp-post-deployment"></a>更新 NTP 部署后

1.  )  (PEP 连接到特权终结点。 无需打开支持票证即可解锁特权终结点。 

2.  运行以下命令即可查看当前的已配置 NTP 服务器：

    ```powershell
    Get-AzsTimeSource
    ```

3.  运行以下命令，将 Azure Stack 更新为使用新的 NTP 服务器并立即同步时间

    ```powershell
    Set-AzsTimeSource -Timeserver NEWTIMESERVER -resync
    ```

    >[!NOTE] 
    >此过程不会更新物理交换机上的时间服务器。 


## <a name="dns"></a>DNS

本部分介绍如何更新 DNS 转发器以解析外部名称。

### <a name="update-the-dns-forwarder-in-azure-stack"></a>在 Azure Stack 中更新 DNS 转发器

Azure Stack 基础结构至少需要一个可访问的 DNS 转发器来解析外部名称。 必须提供 DNS 转发器才能部署 Azure Stack。 该输入在 Azure Stack 内部 DNS 服务器中用作转发器，并为身份验证、市场管理或使用情况等服务启用外部名称解析。

DNS 是一项可更改的关键数据中心基础结构服务，如果 DNS 更改，则必须更新 Azure Stack。

本文介绍如何使用特权终结点 (PEP) 在 Azure Stack 中更新 DNS 转发器。 建议使用两个可靠的 DNS 转发器 IP 地址。

1.  连接到特权终结点。 请注意，不必通过打开支持票证来解锁特权终结点。

2.  运行以下命令，查看当前配置的 DNS 转发器。 或者，也可以使用管理门户区域属性：

    ```powershell
    Get-AzsDnsForwarder 
    ```

3.  运行以下命令更新 Azure Stack，以使用新的 DNS 转发器：

    ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1", "IPAddress 2"
    ``` |

4.  Review the output of the command for any errors.
