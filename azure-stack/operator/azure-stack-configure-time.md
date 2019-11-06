---
title: 为 Azure Stack 配置时间服务器 |Microsoft Docs
description: 了解如何配置 Azure Stack 的时间服务器。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: a70eaaf46988524f5323052a3f2ca90f5b7719e1
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73636810"
---
# <a name="configure-the-time-server-for-azure-stack"></a>为 Azure Stack 配置时间服务器

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*  

你可以使用特权终结点（PEP）来更新 Azure Stack 中的时间服务器。 使用可解析为两个或多个 NTP 服务器 IP 地址的主机名。

Azure Stack 使用网络时间协议（NTP）连接到 Internet 上的时间服务器。 NTP 服务器提供准确的系统时间。 时间跨 Azure Stack 的物理网络交换机、硬件生命周期主机、基础结构服务和虚拟机使用。 如果时钟未同步，Azure Stack 可能会遇到严重的网络和身份验证问题。 日志文件、文档和其他文件的时间戳可能不正确。

需要提供一台时间服务器（NTP）才能使 Azure Stack 同步时间。 部署 Azure Stack 时，请提供 NTP 服务器的地址。 时间是关键的数据中心基础结构服务。 如果服务发生更改，你将需要更新时间。

> [!NOTE]
> Azure Stack 支持仅与一个时间服务器（NTP）同步时间。 不能为 Azure Stack 提供多个 NTPs 来使时间与同步。

## <a name="configure-time"></a>配置时间

1. [连接到 PEP](azure-stack-privileged-endpoint.md)。 
    > [!Note]  
    > 无需通过打开支持票证来解锁特权终结点。

2. 运行以下命令以查看当前配置的 NTP 服务器：

    ```PowerShell
    Get-AzsTimeSource
    ```

3. 运行以下命令，将 Azure Stack 更新为使用新的 NTP 服务器并立即同步时间。

    > [!Note]  
    > 此过程不会更新物理交换机上的时间服务器

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

4. 查看命令输出中是否存在任何错误。


## <a name="next-steps"></a>后续步骤

[查看准备情况报表](azure-stack-validation-report.md)  
[一般 Azure Stack 集成注意事项](azure-stack-datacenter-integration.md)  
