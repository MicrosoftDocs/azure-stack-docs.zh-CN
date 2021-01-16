---
title: 管理 Azure Stack HCI 的 Azure 注册
description: 如何使用 PowerShell 管理 Azure Stack HCI 的 Azure 注册并了解注册状态。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 12/10/2020
ms.openlocfilehash: a81a1973d7324371cb42b23ca7905d39492401cf
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254426"
---
# <a name="manage-azure-registration"></a>管理 Azure 注册

> 适用于 Azure Stack HCI v20H2

创建 Azure Stack HCI 群集后，必须[向 Azure Arc 注册该群集](../deploy/register-with-azure.md)。群集注册后，会定期在本地群集和云之间同步信息。 本主题说明如何了解你的注册状态，如何授予 Azure Active Directory 权限，并在你准备好解除群集的授权时注销群集。

## <a name="understanding-registration-status"></a>了解注册状态

若要了解注册状态，请使用 `Get-AzureStackHCI` PowerShell cmdlet 和 `ClusterStatus`、`RegistrationStatus` 和 `ConnectionStatus` 属性。 例如，在安装 Azure Stack HCI 操作系统之后、创建或加入群集之前，`ClusterStatus` 属性显示为“尚未”状态：

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="创建群集前的 Azure 注册状态":::

创建群集后，只有 `RegistrationStatus` 显示“尚未”状态：

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="创建群集后的 Azure 注册状态":::

根据 Azure 在线服务条款，Azure Stack HCI 需要在安装后 30 天内进行注册。 如果在 30 天后未群集化，则 `ClusterStatus` 将显示 `OutOfPolicy`，如果 30 天后未注册，则 `RegistrationStatus` 将显示 `OutOfPolicy`。

注册群集后，可以查看 `ConnectionStatus` 和 `LastConnected` 时间（通常在最后一天内），除非群集暂时与 Internet 断开连接。 Azure Stack HCI 群集最多可以连续 30 天完全脱机运行。

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="注册后的 Azure 注册状态":::

如果超出允许的最长时间，则 `ConnectionStatus` 将显示 `OutOfPolicy`。

## <a name="azure-active-directory-app-permissions"></a>Azure Active Directory 应用权限

除了在订阅中创建 Azure 资源外，注册 Azure Stack HCI 还可以在 Azure Active Directory 租户中创建一个概念类似于用户的应用标识。 应用标识会继承群集名称。 此标识代表订阅中的 Azure Stack HCI 云服务（如果适用）执行操作。

如果运行 `Register-AzureStackHCI` 的用户是 Azure Active Directory 管理员或已被委派了足够的权限，则这一切都会自动发生，无需执行其他操作。 否则，可能需要 Azure Active Director 管理员的批准才能完成注册。 你的管理员可以向应用显式授予同意，也可以委派权限，使你可以向应用授予同意：

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="Azure Active Directory 权限和标识图" border="false":::

若要授予许可，请打开 [portal.azure.com](https://portal.azure.com) ，并使用对 Azure Active Directory 具有足够权限的 azure 帐户登录。 依次导航到“Azure Active Directory”、“应用注册”。  选择以你的群集命名的应用标识，然后导航到“API 权限”。

 (GA) 版本 Azure Stack HCI 公开上市时，应用需要以下权限，这不同于公共预览版中所需的应用权限：

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.ReadWrite

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.ReadWrite
```

对于公共预览版，应用权限已 (这些权限现已弃用) ：

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

向 Azure Active Directory 管理员寻求批准可能需要一些时间，因此 `Register-AzureStackHCI` cmdlet 会退出，并将注册状态保持为“待管理员同意”，即完成部分注册。 授予同意后，只需重新运行 `Register-AzureStackHCI` 即可完成注册。

## <a name="azure-active-directory-user-permissions"></a>Azure Active Directory 用户权限

运行 Register-AzStackHCI 的用户需要 Azure AD 权限：

- 创建/获取/设置/删除 Azure AD 应用程序 (新建/获取/设置/删除-Get-azureadapplication) 
- 创建/获取 Azure AD 服务主体 (New/Get-azureadserviceprincipal) 
-  (新建/获取/删除-Get-azureadapplicationkeycredential) 管理 AD 应用程序机密
- 授予同意使用特定的应用程序权限 (新建/获取/删除 AzureADServiceAppRoleAssignments) 

可以通过三种方式来完成此操作。

### <a name="option-1-allow-any-user-to-register-applications"></a>选项1：允许任何用户注册应用程序

在 Azure Active Directory 中，导航到 " **用户设置" > 应用注册**"。 在 " **用户可以注册应用程序**" 下，选择 **"是"**。

这将允许任何用户注册应用程序。 但是，用户仍将要求 Azure AD 管理员在进行群集注册期间授予同意。 请注意，这是租户级别设置，因此它可能不适用于大型企业客户。

### <a name="option-2-assign-cloud-application-administration-role"></a>选项2：分配云应用程序管理角色

向用户分配内置的 "云应用程序管理" Azure AD 角色。 这将允许用户注册群集，而无需额外的 AD 管理员许可。

### <a name="option-3-create-a-custom-ad-role-and-consent-policy"></a>选项3：创建自定义 AD 角色和许可策略

最严格的选项是使用自定义同意策略创建自定义 AD 角色，该角色将租户范围内的管理员许可委托给 Azure Stack HCI 服务所需的权限。 分配此自定义角色时，用户可以注册并授予许可，而无需额外的 AD 管理员许可。

   > [!NOTE]
   > 此选项需要 Azure AD Premium 许可证，并使用当前在公共预览版中的自定义 AD 角色和自定义同意策略功能。

   1. 连接到 Azure AD：
   
      ```powershell
      Connect-AzureAD
      ```

   2. 创建自定义同意策略：

      ```powershell
      New-AzureADMSPermissionGrantPolicy -Id "AzSHCI-registration-consent-policy" -DisplayName "Azure Stack HCI registration admin app consent policy" -Description "Azure Stack HCI registration admin app consent policy"
      ```

   3. 添加一个条件，其中包括应用 ID 为1322e676-dee7-41ee-a874-ac923822781c 的 Azure Stack HCI 服务所需的应用权限。 请注意，以下权限适用于 Azure Stack HCI 的 GA 版本，除非你已将 [2020 年11月23日) 的 (预览版 ](https://support.microsoft.com/help/4595086/azure-stack-hci-release-notes-overview) 应用到群集中的每个服务器，并且下载了 StackHCI 模块版本0.4.1 或更高版本，否则不能使用公共预览版。
   
      ```powershell
      New-AzureADMSPermissionGrantConditionSet -PolicyId "AzSHCI-registration-consent-policy" -ConditionSetType "includes" -PermissionType "application" -ResourceApplication "1322e676-dee7-41ee-a874-ac923822781c" -Permissions "bbe8afc9-f3ba-4955-bb5f-1cfb6960b242","8fa5445e-80fb-4c71-a3b1-9a16a81a1966","493bd689-9082-40db-a506-11f40b68128f","2344a320-6a09-4530-bed7-c90485b5e5e2"
      ```

   4. 授予允许注册 Azure Stack HCI 的权限，请注意在步骤2：
   
      ```powershell
      $displayName = "Azure Stack HCI Registration Administrator "
      $description = "Custom AD role to allow registering Azure Stack HCI "
      $templateId = (New-Guid).Guid
      $allowedResourceAction =
      @(
             "microsoft.directory/applications/createAsOwner",
             "microsoft.directory/applications/delete",
             "microsoft.directory/applications/standard/read",
             "microsoft.directory/applications/credentials/update",
             "microsoft.directory/applications/permissions/update",
             "microsoft.directory/servicePrincipals/appRoleAssignedTo/update",
             "microsoft.directory/servicePrincipals/appRoleAssignedTo/read",
             "microsoft.directory/servicePrincipals/appRoleAssignments/read",
             "microsoft.directory/servicePrincipals/createAsOwner",
             "microsoft.directory/servicePrincipals/credentials/update",
             "microsoft.directory/servicePrincipals/permissions/update",
             "microsoft.directory/servicePrincipals/standard/read",
             "microsoft.directory/servicePrincipals/managePermissionGrantsForAll.AzSHCI-registration-consent-policy"
      )
      $rolePermissions = @{'allowedResourceActions'= $allowedResourceAction}
      ```

   5. 创建新的自定义 AD 角色：

      ```powershell
      $customADRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
      ```

   6. 按照以下 [说明](/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal?context=/azure/active-directory/roles/context/ugr-context)将新的自定义 AD 角色分配给将向 Azure 注册 Azure Stack HCI 群集的用户。

## <a name="unregister-azure-stack-hci-with-azure"></a>使用 Azure 注销 Azure Stack HCI

准备好解除 Azure Stack HCI 群集的授权后，请使用 `Unregister-AzStackHCI` cmdlet 进行注销。 这将停止通过 Azure Arc 进行的所有监视、支持和计费功能。将删除代表群集的 Azure 资源和 Azure Active Directory 应用标识，但不会删除该资源组，因为它可能包含其他不相关的资源。

如果在群集节点上运行 `Unregister-AzStackHCI` cmdlet，请使用以下语法并指定你的 Azure 订阅 ID 以及要注销的 Azure Stack HCI 群集的资源名称：

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

系统将提示你在另一台设备 (例如你的电脑或手机) 上访问 microsoft.com/devicelogin，输入代码，并登录到此处，以通过 Azure 进行身份验证。

如果从管理电脑运行 cmdlet，则还需要指定群集中服务器的名称：

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

将弹出一个交互式 Azure 登录窗口。 显示的确切提示将因安全设置（例如双重身份验证）而异。 按照提示进行登录。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [将 Azure Stack HCI 连接到 Azure](../deploy/register-with-azure.md)
- [使用 Azure Monitor 监视 Azure Stack HCI](azure-monitor.md)