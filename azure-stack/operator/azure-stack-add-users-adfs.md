---
title: 为 Azure Stack ADFS 添加用户 | Microsoft Docs
description: 了解如何为 Azure Stack 的 ADFS 部署添加用户
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
ms.date: 02/21/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 93541cd02c3d4110e008e5f3f7011f53be897475
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986232"
---
# <a name="add-azure-stack-users-in-ad-fs"></a>在 AD FS 中添加 Azure Stack 用户
可以使用“Active Directory 用户和计算机”管理单元将其他用户添加到 Azure Stack 环境中，并利用 AD FS 作为其标识提供者。

## <a name="add-windows-server-active-directory-users"></a>添加 Windows Server Active Directory 用户
> [!TIP]
> 此示例使用默认的 azurestack.local ASDK Active Directory。 

1. 使用允许访问 Windows 管理工具的帐户登录计算机，然后打开新的 Microsoft 管理控制台 (MMC)。
2. 选择“文件”>“添加或删除管理单元”。
3. 选择“Active Directory 用户和计算机” > “AzureStack.local” > “用户”。
4. 选择“操作” > “新建” > “用户”。
5. 在“新建对象 - 用户”中，提供用户详细信息。 选择“**下一步**”。
6. 提供并确认密码。
7. 选择“下一步”以最终确定值。 选择“完成”以创建用户。


## <a name="next-steps"></a>后续步骤
[创建服务主体](azure-stack-create-service-principals.md)