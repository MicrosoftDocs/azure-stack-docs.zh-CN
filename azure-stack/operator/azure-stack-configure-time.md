---
title: 为 Azure Stack 中心配置时间服务器 |Microsoft Docs
description: 了解如何配置 Azure Stack 集线器的时间服务器。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: 2a473ab2b44362b7fc93cdd1879b1869f469ddfa
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534050"
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
    > 此过程不会更新物理交换机上的时间服务器

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

4. 查看命令输出中是否存在任何错误。


## <a name="next-steps"></a>后续步骤

[查看准备情况报表](azure-stack-validation-report.md)  
[一般 Azure Stack 集线器集成注意事项](azure-stack-datacenter-integration.md)  
