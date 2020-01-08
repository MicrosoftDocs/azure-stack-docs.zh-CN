---
title: 使用基于角色的访问控制设置访问权限 |Microsoft Docs
description: 了解如何在 Azure Stack 中使用基于角色的访问控制（RBAC）设置访问权限。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/23/2019
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: ae5b167ca807fc1e28c13c553e13ea89c7e6fe49
ms.sourcegitcommit: dc3d0b77ee77742525fa0cd72d8547d25393022f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/26/2019
ms.locfileid: "75492777"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>使用基于角色的访问控制设置访问权限

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 中的用户可以是订阅、资源组或服务的每个实例的读取者、所有者或参与者。 例如，用户 A 可能对订阅拥有 "读取者" 权限，但对虚拟机7拥有 "所有者" 权限。

 - 读者：用户可以查看所有内容，但不能进行任何更改。
 - 参与者：用户可以管理除访问资源之外的所有内容。
 - 所有者：用户可以管理所有内容，包括对资源的访问权限。
 - 自定义：用户对资源拥有有限的特定访问权限。

 有关创建自定义角色的详细信息，请参阅[Azure 资源的自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。

## <a name="set-access-permissions-for-a-user"></a>设置用户的访问权限

1. 使用对要管理的资源具有所有者权限的帐户登录。
2. 在资源的边栏选项卡中，单击 "**访问**" 图标 ![](media/azure-stack-manage-permissions/image1.png)。
3. 在 "**用户**" 边栏选项卡中单击 "**角色**"。
4. 在 "**角色**" 边栏选项卡中，单击 "**添加**" 以添加用户的权限。

## <a name="set-access-permissions-for-a-universal-group"></a>设置通用组的访问权限 

> [!Note]
> 仅适用于 Active Directory 联合服务（AD FS）。

1. 使用对要管理的资源具有所有者权限的帐户登录。
2. 在资源的边栏选项卡中，单击 "**访问**" 图标 ![](media/azure-stack-manage-permissions/image1.png)。
3. 在 "**用户**" 边栏选项卡中单击 "**角色**"。
4. 在 "**角色**" 边栏选项卡中，单击 "**添加**" 以添加通用组 Active Directory 组的权限。

## <a name="next-steps"></a>后续步骤

[添加 Azure Stack 租户](azure-stack-add-new-user-aad.md)