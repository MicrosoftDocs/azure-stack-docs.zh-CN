---
title: 切换凭据
description: 说明如何更新 Azure Stack 集线器的交换机凭据耐用
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 10/14/2020
ms.reviewer: justinha
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: a761544c8d514fd69364d917890d284ab06e9962
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939410"
---
# <a name="switch-credentials"></a>切换凭据

本主题说明如何在所有交换机上更改管理员 (管理员) 和简单网络管理协议 (SNMP) 凭据。 

## <a name="prerequisites"></a>先决条件

在运行这些过程之前：

- 使用远程桌面连接到 HLH。
- 在 HLH 上找到 PuTTY，通常 E:\Tools\Putty\putty.exe。 如果 PuTTY 不可用，请下载并复制到 HLH。
- 请确保同时拥有交换机的当前凭据和新凭据。

## <a name="update-credentials-for-the-admin-and-enable-accounts"></a>更新管理员凭据和启用帐户 

对于缩放单位中的每个开关 (BMC、TOR1 和 TOR2) ：

1. 在 HLH 上，使用 PuTTY，使用当前凭据登录到该交换机。 
1. 运行以下命令，将替换 \<new password\> 为新管理员并启用凭据。 
   ```ini
   enable
   configuration terminal
   username admin pass
   word <new_password> privilege 15
   enable password <new_password>
   ```
1. 将当前会话保持为打开状态。
1. 在 HLH 上，使用 PuTTY，使用新凭据登录到该交换机。
1. 运行以下命令。 启用不会提示输入密码。

   ```ini
   enable
   write
   dir flash:
   ```

1. 验证启动配置是否包含当天的日期。
1. 确认后，关闭此会话和原始会话。

## <a name="update-snmp-accounts"></a>更新 SNMP 帐户

对于缩放单位中的每个开关 (BMC、TOR1 和 TOR2) ：

1. 在 HLH 上，使用 PuTTY 登录到该交换机。
1. 运行以下命令以获取当前的 SNMP 读取/写入用户和组：

   ```ini
   enable
   show run snmp | grep user
   ```

   下面的示例显示了返回的内容类型，包括用户、组名称和密码哈希：

   ```ini
   snmp-server user <user> <group> 3 encrypted auth sha <password_hash>
   ```

1. 运行以下命令， \<user\> 将和替换为 \<group\> 上一步骤中确定的详细信息，并将替换 \<password\> 为新密码：

   ```ini
   configuration terminal
   snmp-server user <user> <group> 3 auth sha <password_new>
   end
   write
   exit
   ```

## <a name="next-steps"></a>后续步骤

[在 Azure Stack Hub 中轮换机密](../operator/azure-stack-rotate-secrets.md)