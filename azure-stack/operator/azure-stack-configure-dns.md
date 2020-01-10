---
title: 更新 Azure Stack 集线器中的 DNS 转发器 |Microsoft Docs
description: 了解如何更新 Azure Stack 集线器中的 DNS 转发器。
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
ms.openlocfilehash: b16eade221c51664205e865d1680e7f048fbfc7a
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817578"
---
# <a name="update-the-dns-forwarder-in-azure-stack-hub"></a>更新 Azure Stack 集线器中的 DNS 转发器

要使 Azure Stack 中心基础结构解析外部名称，至少需要一个可访问的 DNS 转发器。 必须为部署 Azure Stack 中心提供 DNS 转发器。 该输入用于作为转发器的 Azure Stack 集线器内部 DNS 服务器，并为身份验证、marketplace 管理或使用等服务启用外部名称解析。

DNS 是一种关键的数据中心基础结构服务，如果有，则必须更新 Azure Stack 中心。

本文介绍如何使用特权终结点（PEP）更新 Azure Stack 集线器中的 DNS 转发器。 建议使用两个可靠 DNS 转发器 IP 地址。

1. 连接到[特权终结点](azure-stack-privileged-endpoint.md)。 请注意，无需通过打开支持票证来解锁特权终结点。

2. 运行以下命令以查看当前配置的 DNS 转发器。 作为替代方法，你还可以使用管理门户区域属性：

   ```powershell
   Get-AzsDnsForwarder
   ```

3. 运行以下命令以更新 Azure Stack 集线器以使用新的 DNS 转发器：

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. 查看命令输出中是否存在任何错误。

## <a name="next-steps"></a>后续步骤

[防火墙集成](azure-stack-firewall.md)
