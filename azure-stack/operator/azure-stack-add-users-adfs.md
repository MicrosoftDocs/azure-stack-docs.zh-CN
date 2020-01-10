---
title: 向 AD FS 中添加 Azure Stack 集线器用户 |Microsoft Docs
description: 了解如何为 Active Directory 联合身份验证服务（AD FS）部署添加 Azure Stack 集线器用户。
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
ms.openlocfilehash: 93e2e60e235ae26016f52a94916f429c38286511
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75804871"
---
# <a name="add-azure-stack-hub-users-in-ad-fs"></a>向 AD FS 中添加 Azure Stack 集线器用户
你可以使用 " **Active Directory 用户和计算机**" 管理单元将其他用户添加到 Azure Stack 中心环境，使用 Active Directory 联合身份验证服务（AD FS）作为其标识提供者。

## <a name="add-windows-server-active-directory-users"></a>添加 Windows Server Active Directory 用户
> [!TIP]
> 此示例使用默认的 test-azurestack ASDK active directory。 

1. 使用提供对 Windows 管理工具的访问权限的帐户登录到计算机，并打开一个新的 Microsoft 管理控制台（MMC）。
2. 选择 "**文件" > "添加或删除管理单元"** 。
3. 选择**Active Directory 用户和计算机** > **test-azurestack** > **用户**。
4. 选择 "**操作**" > **New** > **用户**"。
5. 在 "新建对象-用户" 中，提供用户详细信息。 选择“**下一页**”。
6. 提供并确认密码。
7. 选择 "**下一步**" 完成值。 选择 "**完成**" 以创建用户。


## <a name="next-steps"></a>后续步骤
[创建服务主体](azure-stack-create-service-principals.md)