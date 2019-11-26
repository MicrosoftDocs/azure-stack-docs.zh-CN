---
title: Create a custom role for Azure Stack registration
titleSuffix: Azure Stack
description: Learn how to create a custom role to avoid using global administrator for Azure Stack registration.
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
ms.openlocfilehash: 0cfbec17b2aef1f6a14615d4b69d8a5e9347e913
ms.sourcegitcommit: 284f5316677c9a7f4c300177d0e2a905df8cb478
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465420"
---
# <a name="create-a-custom-role-for-azure-stack-registration"></a>Create a custom role for Azure Stack registration

*Applies to: Azure Stack integrated systems and Azure Stack Development Kit*

> [!WARNING]
> This isn't a security posture feature. Use it in scenarios where you want constraints to prevent accidental changes to the Azure Subscription. When a user is delegated rights to this custom role, the user has rights to edit permissions and elevate rights. Only assign users you trust to the custom role.

During Azure Stack registration, you must sign in with an Azure Active Directory (Azure AD) account. The account requires the following Azure AD permissions and Azure Subscription permissions:

* **App registration permissions in your Azure AD tenant:** Admins have app registration permissions. The permission for users is a global setting for all users in the tenant. To view or change the setting, see [create an Azure AD app and service principal that can access resources](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    The *user can register applications* setting must be set to **Yes** for you to enable a user account to register Azure Stack. If the app registrations setting is set to **No**, you can't use a user account to register Azure Stack—you have to use a global admin account.

* **A set of sufficient Azure Subscription permissions:** Users that belong to the Owner role have sufficient permissions. For other accounts, you can assign the permission set by assigning a custom role as outlined in the following sections.

Rather than using an account that has Owner permissions in the Azure subscription, you can create a custom role to assign permissions to a less-privileged user account. This account can then be used to register your Azure Stack.

## <a name="create-a-custom-role-using-powershell"></a>Create a custom role using PowerShell

若要创建自定义角色，必须拥有所有 `AssignableScopes` 的 `Microsoft.Authorization/roleDefinitions/write` 权限，例如[所有者](/azure/role-based-access-control/built-in-roles#owner)或[用户访问权限管理员](/azure/role-based-access-control/built-in-roles#user-access-administrator)。 Use the following JSON template to simplify creation of the custom role. The template creates a custom role that allows the required read and write access for Azure Stack registration.

1. Create a JSON file. 例如，`C:\CustomRoles\registrationrole.json`。
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

3. In PowerShell, connect to Azure to use Azure Resource Manager. When prompted, authenticate using an account with sufficient permissions such as [Owner](/azure/role-based-access-control/built-in-roles#owner) or [User Access Administrator](/azure/role-based-access-control/built-in-roles#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. To create the custom role, use **New-AzureRmRoleDefinition** specifying the JSON template file.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Assign a user to registration role

After the registration custom role is created, assign the role to the user account that will be used for registering Azure Stack.

1. Sign in with the account with sufficient permission on the Azure subscription to delegate rights—such as [Owner](/azure/role-based-access-control/built-in-roles#owner) or [User Access Administrator](/azure/role-based-access-control/built-in-roles#user-access-administrator).
2. In **Subscriptions**, select **Access control (IAM) > Add role assignment**.
3. In **Role**, choose the custom role you created: *Azure Stack registration role*.
4. Select the users you want to assign to the role.
5. Select **Save** to assign the selected users to the role.

    ![Select users to assign to custom role in Azure portal](media/azure-stack-registration-role/assign-role.png)

For more information on using custom roles, see [manage access using RBAC and the Azure portal](/azure/role-based-access-control/role-assignments-portal).

## <a name="next-steps"></a>后续步骤

[将 Azure Stack 注册到 Azure](azure-stack-registration.md)
