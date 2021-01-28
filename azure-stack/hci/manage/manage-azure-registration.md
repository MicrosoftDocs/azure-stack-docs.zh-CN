---
title: 管理 Azure Stack HCI 的 Azure 注册
description: 如何管理 Azure 注册以进行 Azure Stack HCI、了解注册状态，以及在你准备好解除群集时注销群集。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 01/27/2021
ms.openlocfilehash: c16216a52b0955277bc6d30725f88d0555908685
ms.sourcegitcommit: dc11aabd3b97c505c5b3cecd3bdb2d5c8e8496aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2021
ms.locfileid: "98981168"
---
# <a name="manage-azure-registration"></a>管理 Azure 注册

> 适用于 Azure Stack HCI v20H2

创建 Azure Stack HCI 群集后，必须[向 Azure Arc 注册该群集](../deploy/register-with-azure.md)。群集注册后，会定期在本地群集和云之间同步信息。 本主题说明如何了解你的注册状态，如何授予 Azure Active Directory 权限，并在你准备好解除群集的授权时注销群集。

## <a name="understanding-registration-status-using-windows-admin-center"></a>了解使用 Windows 管理中心的注册状态

使用 Windows 管理中心连接到群集时，你将看到 "仪表板"，其中显示了 Azure 连接状态。 "**已连接**" 是指群集已注册到 Azure 并已成功同步到过去一天中的云。

   :::image type="content" source="media/manage-azure-registration/registration-status.png" alt-text="Windows 管理中心仪表板将始终显示群集连接状态" lightbox="media/manage-azure-registration/registration-status.png":::

可以通过选择左侧 "**工具**" 菜单底部的 "**设置**"，然后选择 " **Azure Stack HCI 注册**" 来获取详细信息。

   :::image type="content" source="media/manage-azure-registration/azure-stack-hci-registration.png" alt-text="有关详细信息，请选择 &quot;设置&quot; > 工具 > Azure Stack HCI 注册" lightbox="media/manage-azure-registration/azure-stack-hci-registration.png":::

## <a name="understanding-registration-status-using-powershell"></a>使用 PowerShell 了解注册状态

若要了解使用 Windows PowerShell 的注册状态，请使用 `Get-AzureStackHCI` PowerShell cmdlet 和 `ClusterStatus` 、 `RegistrationStatus` 和 `ConnectionStatus` 属性。 例如，在安装 Azure Stack HCI 操作系统之后、创建或加入群集之前，`ClusterStatus` 属性显示为“尚未”状态：

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="创建群集前的 Azure 注册状态":::

创建群集后，只有 `RegistrationStatus` 显示“尚未”状态：

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="创建群集后的 Azure 注册状态":::

根据 Azure 在线服务条款，Azure Stack HCI 需要在安装后 30 天内进行注册。 如果在 30 天后未群集化，则 `ClusterStatus` 将显示 `OutOfPolicy`，如果 30 天后未注册，则 `RegistrationStatus` 将显示 `OutOfPolicy`。

注册群集后，你可以查看 `ConnectionStatus` 和 `LastConnected` 时间（通常在最后一天内，除非群集暂时从 internet 断开连接）。 Azure Stack HCI 群集最多可以连续 30 天完全脱机运行。

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="注册后的 Azure 注册状态":::

如果超出允许的最长时间，则 `ConnectionStatus` 将显示 `OutOfPolicy`。

## <a name="azure-active-directory-app-permissions"></a>Azure Active Directory 应用权限

除了在订阅中创建 Azure 资源外，注册 Azure Stack HCI 还可以在 Azure Active Directory 租户中创建一个概念类似于用户的应用标识。 应用标识会继承群集名称。 此标识代表订阅中的 Azure Stack HCI 云服务（如果适用）执行操作。

如果注册群集的用户是 Azure Active Directory 管理员或被委派了足够的权限，则这一切都将自动发生，无需执行其他操作。 否则，可能需要 Azure Active Director 管理员的批准才能完成注册。 你的管理员可以向应用显式授予同意，也可以委派权限，使你可以向应用授予同意：

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

向 Azure Active Directory 管理员寻求批准可能需要一些时间，因此 `Register-AzStackHCI` cmdlet 会退出，并将注册状态保持为“待管理员同意”，即完成部分注册。 授予同意后，只需重新运行 `Register-AzStackHCI` 即可完成注册。

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

向用户分配内置的 "云应用程序管理" Azure AD 角色。 这将允许用户注册和注销群集，而无需额外的 AD 管理员许可。

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

## <a name="unregister-azure-stack-hci-using-windows-admin-center"></a>使用 Windows 管理中心取消注册 Azure Stack HCI

准备好解除 Azure Stack HCI 群集的授权后，只需使用 Windows 管理中心连接到群集，并选择左侧 "**工具**" 菜单底部的 "**设置**"。 然后选择 **AZURE STACK HCI 注册**，并单击 " **取消注册** " 按钮。 如果组是在注册期间创建的，并且不包含任何其他资源) 和 Azure AD 应用标识，则注销过程会自动清除表示群集的 Azure 资源，Azure 资源组 (。 这会通过 Azure Arc 停止所有监视、支持和计费功能。

   > [!NOTE]
   > 取消注册 Azure Stack HCI 群集需要 Azure Active Directory 管理员或已被委派足够权限的其他用户。 请参阅 [Azure Active Directory 用户权限](#azure-active-directory-user-permissions)。

## <a name="unregister-azure-stack-hci-using-powershell"></a>使用 PowerShell 注销 Azure Stack HCI

你还可以使用 `Unregister-AzStackHCI` cmdlet 取消注册 AZURE STACK HCI 群集。 可以在群集节点上或从管理 PC 上运行 cmdlet。

可能需要安装最新版本的 `Az.StackHCI` 模块。 系统可能会提示你 "是否确实要安装 ' PSGallery ' 中的模块？"，你应该 (Y) 中回答 "是"。

```PowerShell
Install-Module -Name Az.StackHCI
```

### <a name="unregister-from-a-cluster-node"></a>从群集节点注销

如果在 `Unregister-AzStackHCI` 群集中的服务器上运行此 cmdlet，请使用此语法并指定你的 Azure 订阅 ID 以及要取消注册 AZURE STACK HCI 群集的资源名称：

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

系统将提示你在另一台设备 (例如你的电脑或手机) 上访问 microsoft.com/devicelogin，输入代码，并登录到此处，以通过 Azure 进行身份验证。

### <a name="unregister-from-a-management-pc"></a>从管理 PC 中注销

如果从管理电脑运行 cmdlet，则还需要指定群集中服务器的名称：

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

将弹出一个交互式 Azure 登录窗口。 显示的确切提示将因安全设置（例如双重身份验证）而异。 按照提示进行登录。

## <a name="cleaning-up-after-a-cluster-that-was-not-properly-unregistered"></a>在未正确注销群集后进行清理

如果用户在不取消注册的情况下销毁 Azure Stack HCI 群集，例如通过重新映像主机服务器或删除虚拟群集节点，则项目将保留在 Azure 中。 这些都是无害的，不会产生计费或使用资源，但会打乱 Azure 门户。 若要清理它们，可以手动将其删除。

若要删除 Azure Stack HCI 资源，请导航到 Azure 门户中的页面，然后从顶部的操作栏中选择 " **删除** "。 键入资源名称以确认删除，然后选择“删除”  。 若要删除 Azure AD 应用标识，请导航到 **Azure AD**，然后导航到 " **应用注册**"，你会在 " **所有应用程序**" 下找到它。 选择 " **删除** 并确认"。

## <a name="next-steps"></a>后续步骤

如需相关信息，另请参阅：

- [将 Azure Stack HCI 连接到 Azure](../deploy/register-with-azure.md)
