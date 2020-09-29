---
title: 使用基于角色的访问控制管理 Azure Stack 集线器中的资源访问
description: 了解如何在 Azure Stack Hub 中作为管理员或租户管理基于角色的访问控制 (RBAC) 权限。
author: bryanla
ms.topic: article
ms.date: 09/13/2019
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 1002f45b51f4a70ddbadb8230df6e98f591fcbb6
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573745"
---
# <a name="manage-access-to-resources-in-azure-stack-hub-with-role-based-access-control"></a>使用基于角色的访问控制管理 Azure Stack Hub 中的资源访问

Azure Stack 中心支持基于角色的访问控制 (RBAC) ，这与 Microsoft Azure 使用的 [访问管理相同的安全模型](/azure/role-based-access-control/overview) 相同。 可以使用 RBAC 来管理用户、组或应用对订阅、资源和服务的访问权限。

## <a name="basics-of-access-management"></a>访问管理基础知识

基于角色的访问控制 (RBAC) 提供了精细的访问控制，可以用来保护环境安全。 通过在特定范围内分配 RBAC 角色，可以为用户授予他们所需的确切权限。 角色分配的范围可以是订阅、资源组或单个资源。 有关访问管理的更多详细信息，请参阅 [Azure 门户中基于角色的访问控制](/azure/role-based-access-control/overview)一文。

> [!NOTE]
> 使用 Active Directory 联合身份验证服务作为标识提供者部署 Azure Stack Hub 时，RBAC 方案仅支持通用组。

### <a name="built-in-roles"></a>内置角色

Azure Stack Hub 有三个可应用于所有资源类型的基本角色：

* **所有者**：可以管理所有内容，包括对资源的访问权限。
* **参与者**：可以管理除资源访问权限以外的所有内容。
* **读者**：可以查看所有内容，但不能进行任何更改。

### <a name="resource-hierarchy-and-inheritance"></a>资源层次结构和继承

Azure Stack Hub 具有以下资源层次结构：

* 每个订阅属于一个目录。
* 每个资源组属于一个订阅。
* 每个资源属于一个资源组。

子范围将继承在父范围授予的访问权限。 例如：

* 你向某个 Azure AD 组分配了在订阅范围内的**读者**角色。 该组的成员可以查看订阅中的每个资源组和资源。
* 你向某个应用分配了资源组范围内的**参与者**角色。 此应用可以管理该资源组中所有类型的资源，但不能管理订阅中的其他资源组。

### <a name="assigning-roles"></a>分配角色

可以向一位用户分配多个角色，并且每个角色可以与不同的范围相关联。 例如：

* 你向 TestUser-A 分配 Subscription-1 的**读者**角色。
* 向 TestUser-A 分配 TestVM-1 的**所有者**角色。

Azure [角色分配](/azure/role-based-access-control/role-assignments-portal)一文提供了有关查看、分配和删除角色的详细信息。

## <a name="set-access-permissions-for-a-user"></a>设置用户的访问权限

以下步骤介绍了如何为用户配置权限。

1. 使用对要管理的资源具有所有者权限的帐户登录。
2. 在左侧导航窗格中，选择“资源组”  。
3. 选择要针对其设置权限的资源组的名称。
4. 在资源组的导航窗格中，选择“访问控制(标识和访问管理)”  。<BR> “角色分配”  视图会列出对该资源组具有访问权限的项。 可以对结果进行筛选和分组。
5. 在“访问控制”  菜单栏上，选择“添加”  。
6. 在“添加权限”  窗格上：

   * 从“角色”下拉列表中选择要分配的角色。 
   * 从“将访问权限分配到”下拉列表中选择要分配的资源。 
   * 在你的目录中选择要向其授予访问权限的用户、组或应用。 可以通过显示名称、电子邮件地址和对象标识符搜索该目录。

7. 选择“保存”  。

## <a name="next-steps"></a>后续步骤

[创建服务主体](../operator/azure-stack-create-service-principals.md)
