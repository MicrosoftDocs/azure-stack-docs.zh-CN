---
title: 为 Azure Stack Hub 注册创建自定义角色
titleSuffix: Azure Stack Hub
description: 了解如何创建自定义角色，以避免使用全局管理员身份注册 Azure Stack Hub。
author: BryanLa
ms.topic: how-to
ms.date: 08/05/2020
ms.author: bryanla
ms.reviewer: rtiberiu
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: cb2da0dc57069647ecefd8ccc278526f7f35f57d
ms.sourcegitcommit: b69c8334571094721b26e6bdebd639f4fd294dd0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87839226"
---
# <a name="create-a-custom-role-for-azure-stack-hub-registration"></a>为 Azure Stack Hub 注册创建自定义角色

> [!WARNING]
> 这不是一种安全态势功能。 如果你想要实施约束来防止意外更改 Azure 订阅，可以使用此功能。 向某个用户委托此自定义角色的权限时，该用户有权编辑权限和提升权限。 请只将受信任的用户分配到自定义角色。

在 Azure Stack Hub 注册期间，必须使用 Azure Active Directory (Azure AD) 帐户登录。 该帐户需要以下 Azure AD 权限和 Azure 订阅权限：

* **Azure AD 租户中的应用注册权限：** 管理员具有应用注册权限。 用户的权限是租户中所有用户的全局设置。 若要查看或更改设置，请参阅[创建可访问资源的 Azure AD 应用和服务主体](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

    “用户可以注册应用程序”设置必须设置为“是”才能让用户帐户注册 Azure Stack Hub。   如果 "应用注册" 设置设置为 " **否**"，则不能使用用户帐户注册 Azure Stack 中心，必须使用全局管理员帐户。

* **一组足够高的 Azure 订阅权限：** 属于“所有者”角色的用户具有足够的权限。 对于其他帐户，可以通过分配自定义角色来分配权限集，如以下部分所述。

你可以创建自定义角色，为权限较低的用户帐户分配权限，而不是使用 Azure 订阅中具有“所有者”权限的帐户。 然后，可以使用此帐户注册 Azure Stack Hub。

## <a name="create-a-custom-role-using-powershell"></a>使用 PowerShell 创建自定义角色

若要创建自定义角色，必须拥有所有 `AssignableScopes` 的 `Microsoft.Authorization/roleDefinitions/write` 权限，例如[所有者](/azure/role-based-access-control/built-in-roles#owner)或[用户访问权限管理员](/azure/role-based-access-control/built-in-roles#user-access-administrator)。 使用以下 JSON 模板来简化自定义角色的创建。 该模板创建允许对 Azure Stack Hub 注册进行必要读取和写入访问的自定义角色。

1. 创建一个 JSON 文件。 例如，`C:\CustomRoles\registrationrole.json`。
2. 将以下 JSON 添加到该文件。 将 `<SubscriptionID>` 替换为你的 Azure 订阅 ID。

    ```json
    {
      "Name": "Azure Stack Hub registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack Hub",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read",
        "Microsoft.Authorization/locks/read",
        "Microsoft.Authorization/locks/write"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. 在 PowerShell 中，连接到 Azure 以使用 Azure 资源管理器。 出现提示时，请使用[所有者](/azure/role-based-access-control/built-in-roles#owner)或[用户访问管理员](/azure/role-based-access-control/built-in-roles#user-access-administrator)等拥有足够权限的帐户进行身份验证。

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. 若要创建自定义角色，请使用 **New-AzureRmRoleDefinition** 并指定 JSON 模板文件。

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>将用户分配到注册角色

创建了注册自定义角色之后，将该角色分配给将用于注册 Azure Stack Hub 的用户帐户。

1. 使用对 Azure 订阅具有足够权限的帐户登录，以委托权限，如 " [所有者](/azure/role-based-access-control/built-in-roles#owner) " 或 " [用户访问管理员](/azure/role-based-access-control/built-in-roles#user-access-administrator)"。
2. 在“订阅”中，选择“访问控制(IAM)”>“添加角色分配”。  
3. 在“角色”  中，选择已创建的自定义角色：Azure Stack Hub 注册角色  。
4. 选择要分配到该角色的用户。
5. 选择“保存”，将选定的用户分配到该角色。 

    ![在 Azure 门户中选择要分配到自定义角色的用户](media/azure-stack-registration-role/assign-role.png)

有关使用自定义角色的详细信息，请参阅[使用 RBAC 和 Azure 门户管理访问权限](/azure/role-based-access-control/role-assignments-portal)。

## <a name="next-steps"></a>后续步骤

[将 Azure Stack Hub 注册到 Azure](azure-stack-registration.md)
