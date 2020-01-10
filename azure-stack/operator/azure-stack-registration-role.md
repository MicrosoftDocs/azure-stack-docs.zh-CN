---
title: 为 Azure Stack 中心注册创建自定义角色
titleSuffix: Azure Stack Hub
description: 了解如何创建自定义角色，以避免使用全局管理员进行 Azure Stack 集线器注册。
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
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: d678332e10f219d4e37783668154098da2c2a8b5
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75809804"
---
# <a name="create-a-custom-role-for-azure-stack-hub-registration"></a>为 Azure Stack 中心注册创建自定义角色

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

> [!WARNING]
> 这不是一项安全措施。 在希望限制阻止对 Azure 订阅进行意外更改的情况下使用此方法。 如果用户被委派了此自定义角色的权限，则该用户具有编辑权限和提升权限的权限。 仅将你信任的用户分配给自定义角色。

在 Azure Stack Hub 注册过程中，必须使用 Azure Active Directory （Azure AD）帐户进行登录。 帐户需要以下 Azure AD 权限和 Azure 订阅权限：

* **Azure AD 租户中的应用注册权限：** 管理员具有 "应用注册" 权限。 用户的权限是租户中所有用户的全局设置。 若要查看或更改设置，请参阅[创建可访问资源的 Azure AD 应用和服务主体](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)。

    要使用户帐户能够注册 Azure Stack 中心，"*用户可以注册应用程序*" 设置必须设置为 **"是"** 。 如果 "应用注册" 设置设置为 "**否**"，则不能使用用户帐户注册 Azure Stack 中心，必须使用全局管理员帐户。

* **一组足够的 Azure 订阅权限：** 属于所有者角色的用户具有足够的权限。 对于其他帐户，你可以通过分配自定义角色来分配权限集，如以下各节所述。

你可以创建自定义角色来向权限较低的用户帐户分配权限，而不是使用在 Azure 订阅中拥有所有者权限的帐户。 然后，可以使用此帐户注册 Azure Stack 中心。

## <a name="create-a-custom-role-using-powershell"></a>使用 PowerShell 创建自定义角色

若要创建自定义角色，必须拥有所有 `AssignableScopes` 的 `Microsoft.Authorization/roleDefinitions/write` 权限，例如[所有者](/azure/role-based-access-control/built-in-roles#owner)或[用户访问权限管理员](/azure/role-based-access-control/built-in-roles#user-access-administrator)。 使用以下 JSON 模板可以简化自定义角色的创建。 模板创建一个自定义角色，该角色允许 Azure Stack 中心注册所需的读取和写入访问权限。

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
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. 在 PowerShell 中，连接到 Azure 以使用 Azure 资源管理器。 出现提示时，使用具有足够权限的帐户进行身份验证，如[所有者](/azure/role-based-access-control/built-in-roles#owner)或[用户访问管理员](/azure/role-based-access-control/built-in-roles#user-access-administrator)。

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. 若要创建自定义角色，请使用**get-azurermroledefinition**指定 JSON 模板文件。

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>将用户分配到注册角色

创建注册自定义角色后，将角色分配给将用于注册 Azure Stack 集线器的用户帐户。

1. 使用对 Azure 订阅具有足够权限的帐户登录，以委托权限，如 "[所有者](/azure/role-based-access-control/built-in-roles#owner)" 或 "[用户访问管理员](/azure/role-based-access-control/built-in-roles#user-access-administrator)"。
2. 在 "**订阅**" 中，选择 "**访问控制（IAM）" > 添加角色分配**。
3. 在 "**角色**" 中，选择你创建的自定义角色： *Azure Stack 中心注册角色*。
4. 选择要分配给角色的用户。
5. 选择 "**保存**"，将选定的用户分配给角色。

    ![选择要分配到 Azure 门户中的自定义角色的用户](media/azure-stack-registration-role/assign-role.png)

有关使用自定义角色的详细信息，请参阅[使用 RBAC 管理访问权限和 Azure 门户](/azure/role-based-access-control/role-assignments-portal)。

## <a name="next-steps"></a>后续步骤

[向 Azure 注册 Azure Stack 集线器](azure-stack-registration.md)
