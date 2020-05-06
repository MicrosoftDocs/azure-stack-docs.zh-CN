---
title: 在 Azure Stack 集线器中配置时间服务器
description: 了解如何在 Azure Stack Hub 中配置时间服务器。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 2/19/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: f1627be5d6986cd82fe21a58163f22eb0873eda6
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836098"
---
# <a name="configure-the-time-server-for-azure-stack-hub"></a>配置 Azure Stack Hub 的时间服务器

可以使用特权终结点 (PEP) 来更新 Azure Stack Hub 中的时间服务器。 使用可解析为两个或更多个 NTP （网络时间协议）服务器 IP 地址的主机名。

Azure Stack 中心使用 NTP 连接到 internet 上的时间服务器。 NTP 服务器提供准确的系统时间。 Azure Stack Hub 的物理网络交换机、硬件生命周期主机、基础结构服务和虚拟机都使用时间。 如果时钟未同步，Azure Stack Hub 可能会遇到严重的网络和身份验证问题。 在创建日志文件、文档和其他文件时，时间戳可能会不正确。

必须提供一个时间服务器 (NTP)，这样 Azure Stack Hub 才能同步时间。 部署 Azure Stack Hub 时，请提供 NTP 服务器的地址。 时间是重要的数据中心基础结构服务。 如果服务发生更改，则需要更新时间。

> [!NOTE]
> Azure Stack Hub 支持只与一个时间服务器 (NTP) 同步时间。 不能为 Azure Stack 中心提供多个 NTPs 以使时间与同步。

## <a name="configure-time"></a>配置时间

1. [连接到 PEP](azure-stack-privileged-endpoint.md)。
    > [!Note]  
    > 不需开具支持票证来解锁特权终结点。

2. 运行以下命令即可查看当前的已配置 NTP 服务器：

    ```PowerShell
    Get-AzsTimeSource
    ```

3. 运行以下命令，将 Azure Stack 集线器更新为使用新的 NTP 服务器并立即同步时间。

    > [!Note]  
    > 此过程不会更新物理交换机上的时间服务器。 如果您的时间服务器不是基于 Windows 的 NTP 服务器，则需要添加该标志`0x8`。

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

    对于基于 Windows 的时间服务器以外的服务器：

    ```PowerShell
    Set-AzsTimeSource -TimeServer "NEWTIMESERVERIP,0x8" -resync
    ```

4. 请查看命令输出中是否有错误。


## <a name="next-steps"></a>后续步骤

[查看就绪性报表](azure-stack-validation-report.md)  
[有关 Azure Stack Hub 集成的一般注意事项](azure-stack-datacenter-integration.md)  
