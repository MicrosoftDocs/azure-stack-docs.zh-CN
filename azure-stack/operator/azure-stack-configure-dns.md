---
title: 在 Azure Stack Hub 中更新 DNS 转发器
description: 了解如何在 Azure Stack Hub 中更新 DNS 转发器。
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: ede13e98893fcf30c0e1640d65271554410f659a
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836081"
---
# <a name="update-the-dns-forwarder-in-azure-stack-hub"></a>在 Azure Stack Hub 中更新 DNS 转发器

Azure Stack Hub 基础结构至少需要一个可访问的 DNS 转发器来解析外部名称。 必须提供 DNS 转发器才能部署 Azure Stack Hub。 该输入用于作为转发器的 Azure Stack 集线器内部 DNS 服务器，并对身份验证、marketplace 管理或使用等服务启用外部名称解析。

DNS 是一种关键的数据中心基础结构服务，可以更改。 如果是这样，则必须更新 Azure Stack 中心。

本文介绍如何使用特权终结点（PEP）更新 Azure Stack 集线器中的 DNS 转发器。 建议使用两个可靠 DNS 转发器 IP 地址。

## <a name="steps-to-update-the-dns-forwarder"></a>更新 DNS 转发器的步骤

1. 连接到[特权终结点](azure-stack-privileged-endpoint.md)。 无需通过打开支持票证来解锁特权终结点。

2. 运行以下命令，查看当前配置的 DNS 转发器。 作为替代方法，还可以使用管理员门户区域属性：

   ```powershell
   Get-AzsDnsForwarder
   ```

3. 运行以下命令以更新 Azure Stack 集线器以使用新的 DNS 转发器：

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. 请查看命令输出中是否有错误。

## <a name="next-steps"></a>后续步骤

[防火墙集成](azure-stack-firewall.md)
