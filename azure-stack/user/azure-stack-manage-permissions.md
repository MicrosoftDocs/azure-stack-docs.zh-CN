---
title: 使用基于角色的访问控制管理对 Azure Stack 中心资源的访问
description: 了解如何在 Azure Stack Hub 中将基于角色的访问控制（RBAC）权限作为管理员或租户进行管理。
author: bryanla
ms.topic: article
ms.date: 09/13/2019
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 65e5c5fe52ec2901379571103686bd816819909f
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884699"
---
# <a name="manage-access-to-resources-in-azure-stack-hub-with-role-based-access-control"></a>使用基于角色的访问控制管理对 Azure Stack 中心资源的访问

Azure Stack 中心支持基于角色的访问控制（RBAC），这是 Microsoft Azure 使用的[访问管理的相同安全模型](/azure/role-based-access-control/overview)。 可以使用 RBAC 管理对订阅、资源和服务的用户、组或应用程序访问。

## <a name="basics-of-access-management"></a>访问管理基础知识

基于角色的访问控制（RBAC）提供可用于保护环境的精细访问控制。 通过在特定范围分配 RBAC 角色，为用户授予所需的确切权限。 角色分配的范围可以是订阅、资源组或单个资源。 有关访问管理的更多详细信息，请参阅 Azure 门户一文[中的基于角色的访问控制](/azure/role-based-access-control/overview)。

> [!NOTE]
> 使用 Active Directory 联合身份验证服务作为标识提供者部署 Azure Stack 集线器时，RBAC 方案仅支持通用组。

### <a name="built-in-roles"></a>内置角色

Azure Stack 中心具有三个可应用于所有资源类型的基本角色：

* **所有者**：可管理所有内容，包括对资源的访问权限。
* **参与者**：可管理除访问资源之外的所有内容。
* **读者**：可查看所有内容，但不能进行任何更改。

### <a name="resource-hierarchy-and-inheritance"></a>资源层次结构和继承

Azure Stack 中心具有以下资源层次结构：

* 每个订阅属于一个目录。
* 每个资源组属于一个订阅。
* 每个资源属于一个资源组。

子范围将继承在父范围授予的访问权限。 例如：

* 你向某个 Azure AD 组分配了在订阅范围内的**读者**角色。 该组成员可以查看订阅中的每个资源组和资源。
* 你可以将**参与者**角色分配给资源组范围内的应用。 应用可以管理该资源组中的所有类型的资源，但不能管理订阅中的其他资源组。

### <a name="assigning-roles"></a>分配角色

可以向一位用户分配多个角色，并且每个角色可以与不同的范围相关联。 例如：

* 将 "**读取**者" 角色分配给订阅1。
* 将 "**所有者**" 角色分配给 "TestVM-1"。

Azure [角色分配](/azure/role-based-access-control/role-assignments-portal)一文提供了有关查看、分配和删除角色的详细信息。

## <a name="set-access-permissions-for-a-user"></a>设置用户的访问权限

以下步骤介绍了如何为用户配置权限。

1. 使用对要管理的资源具有所有者权限的帐户登录。
2. 在左侧导航窗格中，选择“资源组”。
3. 选择要针对其设置权限的资源组的名称。
4. 在资源组的导航窗格中，选择“访问控制(标识和访问管理)”。<BR> **角色分配**视图将列出有权访问的资源组的项。 可以筛选并对结果进行分组。
5. 在**访问控制**菜单栏上，选择 "**添加**"。
6. 在 "**添加权限**" 窗格中：

   * 从“角色”下拉列表中选择要分配的角色。
   * 从“将访问权限分配到”下拉列表中选择要分配的资源。
   * 选择要授予访问权限的目录中的用户、组或应用。 可以通过显示名称、电子邮件地址和对象标识符搜索该目录。

7. 选择“保存”。

## <a name="next-steps"></a>后续步骤

[创建服务主体](../operator/azure-stack-create-service-principals.md)
