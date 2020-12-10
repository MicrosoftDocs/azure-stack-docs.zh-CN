---
title: 集成的 Dell 远程访问控制器凭据
description: 说明如何为集成的 Dell 远程访问控制器更新凭据
author: troettinger
ms.author: thoroet
ms.service: azure-stack
ms.topic: article
ms.date: 04/28/2020
ms.reviewer: justinha
ms.lastreviewed: 04/28/2020
ms.openlocfilehash: 2c6e7f0aa0e6e03af061083259234e49275a83de
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939423"
---
# <a name="update-credentials-for-the-integrated-dell-remote-access-controller"></a>更新集成 Dell 远程访问控制器的凭据

本部分介绍如何更改集成的 Dell 远程访问控制器 (iDRAC) 凭据。 

## <a name="prerequisites"></a>先决条件

在运行该过程之前： 

- 使用远程桌面连接到管理虚拟机。 
- 确保你拥有帐户或帐户的新凭据。 
 
## <a name="update-the-idrac-credentials"></a>更新 iDRAC 凭据

若要更新环境中) 的所有 PowerEdge 服务器 (的 iDRAC 凭据，请执行以下操作：

1. 在 web 浏览器中，登录到 https://<iDRAC_IP>。 
1. 请参阅 **iDRAC Settings**  >  **Users**。 
1. 选择要修改的用户，然后单击 " **编辑**"。 
1. 在 " **编辑用户** " 窗口中，在 " **密码** " 和 " **确认密码**" 中输入新密码，如下图所示： 

   ![显示用户信息的屏幕截图](../operator/media/idrac-credentials/enter-user.png)

1. 单击 " **保存**"，然后单击 **"确定"**。 

## <a name="next-steps"></a>后续步骤

[在 Azure Stack Hub 中轮换机密](../../operator/azure-stack-rotate-secrets.md)