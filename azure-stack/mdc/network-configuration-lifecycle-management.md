---
title: MDC 网络配置生命周期管理
description: 了解 Azure 模块化数据中心的网络配置生命周期管理。
author: justinha
ms.author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: b88511f47064f0ac55571c81924cca13eb65e9f6
ms.sourcegitcommit: 25b234330df4e753ed2dd480c208ec88cd90234c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94924344"
---
# <a name="configuration-lifecycle-management"></a>配置生命周期管理

本文介绍适用于 Azure 模块化数据中心的网络配置生命周期管理。

## <a name="update-configuration"></a>更新配置

此方法用于更新字段中的交换机配置，适用于所有类型的配置更新。 由于我们的控件之外的许多未知变量（如客户或 OEM 应用的手动配置），此手动过程包含多个步骤。 目前，不保证正常运行时间。 应在维护时段执行更新。

1. 对 edge1、edge2、tor1、tor2 和 BMC 交换机进行当前配置文件的备份。 在交换机中复制这些文件。

1. 记下现有配置的工具包生成号。 所有配置的 **motd** 横幅中都应有此数字。 搜索 **BUILDNUMBER**。

1. 使用步骤2中的相同工具包版本重新生成初始配置。

1. 将步骤3中的配置加载到各自的开关上。 此操作的要点是通过交换机来使我们的工具所生成的配置 *冲蚀* ，以获取基线。 此操作可由 OEM 在独立硬件上执行，如在 OEM 实验室中，或与客户现场执行。

1. 备份步骤4中的配置文件。 将其复制到远程位置。

1. 使用所选的 diff 工具，将步骤1中当前配置的备份与步骤5中的基线配置的备份进行比较。 为每个设备的已升级交换机配置提供应执行的所有差异的注释或副本。

1. 运行将生成更新的交换机配置的新工具包。

1. 将步骤6中的差异合并为新的交换机配置。

1. 将新配置加载到各个交换机上。 在工具的输出目录中运行提供的验证后命令。

1. 保存配置。

## <a name="ntp"></a>NTP

可以使用特权终结点 (PEP) 来更新 Azure Stack 中的时间服务器。 使用可解析为两个或更多网络时间协议 (NTP) 服务器 IP 地址的主机名。

Azure Stack 使用 NTP 连接到 internet 上的时间服务器。 NTP 服务器提供准确的系统时间。 Azure Stack 的物理网络交换机、硬件生命周期主机、基础结构服务和虚拟机都使用时间。 如果时钟未同步，Azure Stack 可能会遇到严重的网络和身份验证问题。 可能创建的日志文件、文档和其他文件的时间戳不正确。

必须提供一个时间服务器 (NTP)，这样 Azure Stack 才能同步时间。 部署 Azure Stack 时，请提供 NTP 服务器的地址。 时间是重要的数据中心基础结构服务。 如果该服务发生更改，则需要更新该时间。

Azure Stack 支持只与一个时间服务器 (NTP) 同步时间。 不能为 Azure Stack 提供多个 NTPs 来使时间与同步。 建议 (DNS) 条目设置域名系统，将其解析为多台 NTP 服务器。

### <a name="update-ntp-post-deployment"></a>在部署后更新 NTP

1. 连接到 PEP。 无需打开支持票证即可将其解锁。

1. 运行以下命令以查看当前配置的 NTP 服务器。

    ```powershell
    Get-AzsTimeSource
    ```

1. 运行以下命令，将 Azure Stack 更新为使用新的 NTP 服务器并立即同步时间。

    ```powershell
    Set-AzsTimeSource -Timeserver NEWTIMESERVER -resync
    ```

>[!NOTE]
>此过程不会更新物理交换机上的时间服务器。

## <a name="dns"></a>DNS

本部分介绍如何更新 DNS 转发器以解析外部名称。

### <a name="update-the-dns-forwarder-in-azure-stack"></a>在 Azure Stack 中更新 DNS 转发器

Azure Stack 基础结构至少需要一个可访问的 DNS 转发器来解析外部名称。 必须提供 DNS 转发器才能部署 Azure Stack。 该输入用作转发器 Azure Stack 内部 DNS 服务器，并为身份验证、marketplace 管理或使用等服务启用外部名称解析。

DNS 是一项可更改的关键数据中心基础结构服务。 如果确实发生更改，则必须更新 Azure Stack。

本文介绍如何使用 PEP 更新 Azure Stack 中的 DNS 转发器。 建议使用两个可靠 DNS 转发器 IP 地址。

1. 连接到 PEP。 无需通过打开支持票证来解锁 PEP。

1. 运行以下命令，查看当前配置的 DNS 转发器。 作为替代方法，你还可以使用管理门户区域属性。

    ```powershell
    Get-AzsDnsForwarder 
    ```

1. 运行以下命令，将 Azure Stack 更新为使用新的 DNS 转发器。

    ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1", "IPAddress 2"
    ```

1. 请查看命令输出中是否有错误。
