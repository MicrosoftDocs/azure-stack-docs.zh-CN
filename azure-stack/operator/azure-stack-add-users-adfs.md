---
title: 向 AD FS 中添加 Azure Stack 用户 |Microsoft Docs
description: 了解如何为 Active Directory 联合身份验证服务 (AD FS) 部署添加 Azure Stack 用户。
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 06/03/2019
ms.openlocfilehash: 4411290b075e105a827de8fb2c8295dfd84e3b50
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118635"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>在 AD FS 中添加 Azure Stack 用户
你可以使用 " **Active Directory 用户和计算机**" 管理单元将其他用户添加到 Azure Stack 环境, 使用 Active Directory 联合身份验证服务 (AD FS) 作为其标识提供者。

## <a name="add-windows-server-active-directory-users"></a>添加 Windows Server Active Directory 用户
> [!TIP]
> 此示例使用默认的 azurestack.local ASDK Active Directory。 

1. 使用提供对 Windows 管理工具的访问权限的帐户登录到计算机, 并打开一个新的 Microsoft 管理控制台 (MMC)。
2. 选择“文件”>“添加或删除管理单元”。
3. 选择“Active Directory 用户和计算机” > “AzureStack.local” > “用户”。
4. 选择“操作” > “新建” > “用户”。
5. 在 "新建对象-用户" 中, 提供用户详细信息。 选择“**下一步**”。
6. 提供并确认密码。
7. 选择 "**下一步**" 完成值。 选择“完成”以创建用户。


## <a name="next-steps"></a>后续步骤
[创建服务主体](azure-stack-create-service-principals.md)