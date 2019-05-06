---
title: 如何为 Azure Stack 创建注册角色
description: 如何创建自定义角色，以避免使用全局管理员身份进行注册。
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
ms.date: 02/13/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: f5ccc5fc7a280cd8d0832edfe1be6f4ff35dba1d
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985346"
---
# <a name="create-a-registration-role-for-azure-stack"></a>为 Azure Stack 创建注册角色

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

对于您不想要在 Azure 订阅中提供所有者权限的情况下，可以创建自定义角色将权限分配给要注册 Azure Stack 的用户帐户。

> [!WARNING]
> 这不是一种安全态势功能。 如果你想要实施约束来防止意外更改 Azure 订阅，可以使用此功能。 向某个用户委托此自定义角色的权限时，该用户有权编辑权限和提升权限。 请只将受信任的用户分配到自定义角色。

注册 Azure Stack 时，注册帐户需要以下 Azure Active Directory 权限和 Azure 订阅权限：

* **Azure Active Directory 租户中的应用程序注册权限：** 管理员拥有应用程序注册权限。 用户的权限是租户中所有用户的全局设置。 若要查看或更改该设置，请参阅 [创建 Azure AD 应用程序和服务主体可以访问 resources]((/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

    “用户可以注册应用程序”设置必须设置为“是”才能让用户帐户注册 Azure Stack。 如果“应用注册”设置指定为“否”，则你无法使用用户帐户，而必须使用全局管理员帐户来注册 Azure Stack。

* **一组足够高的 Azure 订阅权限：**“所有者”组中的用户拥有足够高的权限。 对于其他帐户，可以通过分配自定义角色来分配权限集，如以下部分所述。

## <a name="create-a-custom-role-using-powershell"></a>使用 PowerShell 创建自定义角色

若要创建自定义角色，必须具有`Microsoft.Authorization/roleDefinitions/write`对所有权限`AssignableScopes`，如 [Owner]((/azure/role-based-access-control/built-in-roles#owner) 或 [用户访问管理员] ((/azure/role-based-access-control/built-in-roles#用户访问权限的管理员）。 使用以下 JSON 模板来简化自定义角色的定义。 该模板创建允许对 Azure Stack 注册进行必要读取和写入访问的自定义角色。

1. 创建一个 JSON 文件。 例如 `C:\CustomRoles\registrationrole.json`
2. 将以下 JSON 添加到该文件。 将 `<SubscriptionID>` 替换为你的 Azure 订阅 ID。

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. 在 PowerShell 中，连接到 Azure 以使用 Azure 资源管理器。 出现提示时，身份验证使用具有足够的权限，如 [Owner]((/azure/role-based-access-control/built-in-roles#owner) 或 [用户访问管理员] ((/azure/role-based-access-control/built-in-roles# 的帐户用户访问权限的管理员）。

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. 若要将角色添加到订阅，请使用 **New-AzureRmRoleDefinition** 并指定 JSON 模板文件。

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>将用户分配到注册角色

创建注册自定义角色后，分配用于注册 Azure Stack 的角色用户。

1. 使用 Azure 订阅上具有足够的权限的帐户登录，以委派权限-如 [Owner]((/azure/role-based-access-control/built-in-roles#owner) 或 [用户访问管理员] ((/ azure/的基于角色的访问权限的控制 /内置角色 #user 的访问权限的管理员）。
2. 在“订阅”中，选择“访问控制(IAM)”>“添加角色分配”。
3. 在“角色”中，选择创建的自定义角色“Azure Stack 注册角色”。
4. 选择要分配到该角色的用户。
5. 选择“保存”，将选定的用户分配到该角色。

    ![选择要分配到角色的用户](media/azure-stack-registration-role/assign-role.png)

使用自定义角色的详细信息，请参阅 [管理使用 RBAC 和 Azure portal]((/azure/role-based-access-control/role-assignments-portal) 的访问权限。

## <a name="next-steps"></a>后续步骤

[将 Azure Stack 注册到 Azure](azure-stack-registration.md)
