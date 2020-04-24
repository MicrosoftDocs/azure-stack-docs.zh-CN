---
title: 使用基于角色的访问控制设置访问权限
description: 了解如何在 Azure Stack Hub 中使用基于角色的访问控制 (RBAC) 设置访问权限。
author: justinha
ms.topic: article
ms.date: 12/23/2019
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: f74f35a8b1124ed9678e6b4bc254d4d2cd05a84e
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77699263"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>使用基于角色的访问控制设置访问权限

Azure Stack Hub 中的用户可以是订阅、资源组或服务的每个实例的读者、所有者或参与者。 例如，用户 A 可能对订阅 1 具有读者权限，但对虚拟机 7 则具有所有者权限。

 - 读者：用户可以查看所有内容，但不能进行任何更改。
 - 参与者：用户可以管理所有内容（对资源的访问权限除外）。
 - 所有者：用户可以管理所有内容，包括对资源的访问权限。
 - 自定义：用户对资源具有受限的特定访问权限。

 有关创建自定义角色的详细信息，请参阅 [Azure 资源的自定义角色](https://docs.microsoft.com/azure/role-based-access-control/custom-roles)。

## <a name="set-access-permissions-for-a-user"></a>设置用户的访问权限

1. 使用对要管理的资源具有所有者权限的帐户登录。
2. 在“资源”边栏选项卡中，单击“访问”  图标 ![](media/azure-stack-manage-permissions/image1.png)。
3. 在“用户”  边栏选项卡中，单击“角色”  。
4. 在“角色”  边栏选项卡中，单击“添加”  即可添加用户的权限。

## <a name="set-access-permissions-for-a-universal-group"></a>设置通用组的访问权限 

> [!Note]
> 仅适用于 Active Directory 联合身份验证服务 (AD FS)。

1. 使用对要管理的资源具有所有者权限的帐户登录。
2. 在“资源”边栏选项卡中，单击“访问”  图标 ![](media/azure-stack-manage-permissions/image1.png)。
3. 在“用户”  边栏选项卡中，单击“角色”  。
4. 在“角色”  边栏选项卡中，单击“添加”  即可添加通用组 Active Directory 组的权限。

## <a name="next-steps"></a>后续步骤

[添加 Azure Stack Hub 租户](azure-stack-add-new-user-aad.md)