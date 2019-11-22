---
title: 更新 Azure Stack 中的 DNS 转发器 |Microsoft Docs
description: 了解如何更新 Azure Stack 中的 DNS 转发器。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 22e49f28dee6b4aa97b9e84cf52950dd678450e4
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308198"
---
# <a name="update-the-dns-forwarder-in-azure-stack"></a>更新中的 DNS 转发器 Azure Stack

要使 Azure Stack 基础结构解析外部名称，至少需要一个可访问的 DNS 转发器。 必须为 Azure Stack 的部署提供 DNS 转发器。 该输入用作转发器 Azure Stack 内部 DNS 服务器，并为身份验证、marketplace 管理或使用等服务启用外部名称解析。

DNS 是一种关键的数据中心基础结构服务，如果有，则必须更新 Azure Stack。

本文介绍如何使用特权终结点（PEP）更新 Azure Stack 中的 DNS 转发器。 建议使用两个可靠 DNS 转发器 IP 地址。

1. 连接到[特权终结点](azure-stack-privileged-endpoint.md)。 请注意，无需通过打开支持票证来解锁特权终结点。

2. 运行以下命令以查看当前配置的 DNS 转发器。 作为替代方法，你还可以使用管理门户区域属性：

   ```powershell
   Get-AzsDnsForwarder
   ```

3. 运行以下命令，将 Azure Stack 更新为使用新的 DNS 转发器：

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. 查看命令输出中是否存在任何错误。

## <a name="next-steps"></a>后续步骤

[防火墙集成](azure-stack-firewall.md)
