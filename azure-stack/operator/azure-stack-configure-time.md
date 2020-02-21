---
title: 为 Azure Stack 中心配置时间服务器
description: 了解如何配置 Azure Stack 集线器的时间服务器。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 2/19/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: 74781cf3e58b5ba3f2c1bb090dd15bb5aad7ca2a
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77508967"
---
# <a name="configure-the-time-server-for-azure-stack-hub"></a>为 Azure Stack 中心配置时间服务器

你可以使用特权终结点（PEP）来更新 Azure Stack 集线器中的时间服务器。 使用可解析为两个或多个 NTP 服务器 IP 地址的主机名。

Azure Stack 中心使用网络时间协议（NTP）连接到 Internet 上的时间服务器。 NTP 服务器提供准确的系统时间。 时间跨越 Azure Stack 集线器的物理网络交换机、硬件生命周期主机、基础结构服务和虚拟机。 如果时钟未同步，Azure Stack 集线器可能会遇到与网络和身份验证有关的严重问题。 日志文件、文档和其他文件的时间戳可能不正确。

若要使 Azure Stack 中心同步时间，则需要提供一台时间服务器（NTP）。 部署 Azure Stack 中心时，提供 NTP 服务器的地址。 时间是关键的数据中心基础结构服务。 如果服务发生更改，你将需要更新时间。

> [!NOTE]
> Azure Stack 集线器支持仅将时间与一个时间服务器（NTP）进行同步。 不能为 Azure Stack 中心提供多个 NTPs 以使时间与同步。

## <a name="configure-time"></a>配置时间

1. [连接到 PEP](azure-stack-privileged-endpoint.md)。 
    > [!Note]  
    > 无需通过打开支持票证来解锁特权终结点。

2. 运行以下命令以查看当前配置的 NTP 服务器：

    ```PowerShell
    Get-AzsTimeSource
    ```

3. 运行以下命令，将 Azure Stack 集线器更新为使用新的 NTP 服务器并立即同步时间。

    > [!Note]  
    > 此过程不会更新物理交换机上的时间服务器。 如果时间服务器不是基于 Windows 的 NTP 服务器，则需要将标志添加 `0x8`。

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

    对于基于 Windows 的时间服务器以外的服务器：

    ```PowerShell
    Set-AzsTimeSource -TimeServer "NEWTIMESERVERIP,0x8" -resync
    ```

4. 查看命令输出中是否存在任何错误。


## <a name="next-steps"></a>后续步骤

[查看准备情况报表](azure-stack-validation-report.md)  
[一般 Azure Stack 集线器集成注意事项](azure-stack-datacenter-integration.md)  
