---
title: 使用基于角色的访问控制设置访问权限 |Microsoft Docs
description: 了解如何在 Azure Stack 中使用基于角色的访问控制（RBAC）设置访问权限。
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
ms.date: 05/16/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: fa4e836a2c7cd5b59a6234a05efcc1cface12620
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277048"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>使用基于角色的访问控制设置访问权限

适用范围：*Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 中的用户可以是订阅、资源组或服务的每个实例的读取者、所有者或参与者。 例如，用户 A 可能对订阅 1 具有读者权限，但对虚拟机 7 则具有所有者权限。

 - 读者：用户可以查看所有内容，但不能进行任何更改。
 - 参与者：用户可以管理所有内容（对资源的访问权限除外）。
 - 所有者：用户可以管理所有内容，包括对资源的访问权限。

## <a name="set-access-permissions-for-a-user"></a>设置用户的访问权限

1. 使用对要管理的资源具有所有者权限的帐户登录。
2. 在“资源”边栏选项卡中，单击“访问”图标 ![](media/azure-stack-manage-permissions/image1.png)。
3. 在“用户”边栏选项卡中，单击“角色”。
4. 在“角色”边栏选项卡中，单击“添加”即可添加用户的权限。

## <a name="set-access-permissions-for-a-universal-group"></a>设置通用组的访问权限 

> [!Note]
> 仅适用于 Active Directory 联合身份验证服务 (AD FS)。

1. 使用对要管理的资源具有所有者权限的帐户登录。
2. 在“资源”边栏选项卡中，单击“访问”图标 ![](media/azure-stack-manage-permissions/image1.png)。
3. 在“用户”边栏选项卡中，单击“角色”。
4. 在“角色”边栏选项卡中，单击“添加”即可添加通用组 Active Directory 组的权限。

## <a name="next-steps"></a>后续步骤

[添加 Azure Stack 租户](azure-stack-add-new-user-aad.md)