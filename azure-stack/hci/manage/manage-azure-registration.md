---
title: 通过 Azure 管理 Azure Stack HCI 群集注册
description: 如何管理 Azure registration for Azure Stack HCI 群集、了解注册状态，以及在准备好取消群集时注销群集。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: 101d1cbcab097803e9c8a9fd9cd651dcf6fc80d1
ms.sourcegitcommit: e432e7f0a790bd6419987cbb5c5f3811e2e7a4a2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102515628"
---
# <a name="manage-cluster-registration-with-azure"></a>通过 Azure 管理群集注册

> 适用于 Azure Stack HCI 版本 20H2

创建 Azure Stack HCI 群集后，必须将 [Windows 管理中心注册到 azure](register-windows-admin-center.md) ，然后将 [该群集注册到 azure](../deploy/register-with-azure.md)。 注册群集后，它会定期在本地群集和云之间同步信息。 

本文介绍如何查看注册状态，如何授予 Azure Active Directory (Azure AD) 权限，并在准备好取消群集时注销群集。

## <a name="view-registration-status-in-windows-admin-center"></a>在 Windows 管理中心中查看注册状态

使用 Windows 管理中心连接到群集时，你将看到 "仪表板"，其中显示了 Azure 连接状态。 "**已连接**" 是指群集已注册到 Azure 并已成功同步到过去一天中的云。

:::image type="content" source="media/manage-azure-registration/registration-status.png" alt-text="屏幕截图，显示 Windows 管理中心仪表板上的群集连接状态。" lightbox="media/manage-azure-registration/registration-status.png":::

可以通过选择左侧 "**工具**" 菜单底部的 "**设置**"，然后选择 " **Azure Stack HCI 注册**" 来获取详细信息。

:::image type="content" source="media/manage-azure-registration/azure-stack-hci-registration.png" alt-text="显示用于获取 Azure Stack H C I 注册信息的选项的屏幕截图。" lightbox="media/manage-azure-registration/azure-stack-hci-registration.png":::

## <a name="view-registration-status-in-powershell"></a>在 PowerShell 中查看注册状态

若要使用 Windows PowerShell 查看注册状态，请使用 `Get-AzureStackHCI` PowerShell cmdlet 和 `ClusterStatus` 、 `RegistrationStatus` 和 `ConnectionStatus` 属性。 

例如，在安装 Azure Stack HCI 操作系统后，但在创建或加入群集之前，该 `ClusterStatus` 属性会显示 `NotYet` 状态：

:::image type="content" source="media/manage-azure-registration/1-get-azurestackhci.png" alt-text="显示群集创建之前 Azure 注册状态的屏幕截图。":::

创建群集后，只 `RegistrationStatus` 显示 `NotYet` 状态：

:::image type="content" source="media/manage-azure-registration/2-get-azurestackhci.png" alt-text="显示群集创建后的 Azure 注册状态的屏幕截图。":::

必须在安装后的30天内注册 Azure Stack HCI 群集，如 Azure Online Services 条款中所定义。 如果在30天后尚未创建或加入群集， `ClusterStatus` 会显示 `OutOfPolicy` 。 如果在30天后尚未注册群集， `RegistrationStatus` 会显示 `OutOfPolicy` 。

注册群集后，可以看到 `ConnectionStatus` 和 `LastConnected` 时间。 `LastConnected`除非群集暂时与 internet 断开连接，否则时间通常在最后一天内。 Azure Stack HCI 群集最多可以连续 30 天完全脱机运行。

:::image type="content" source="media/manage-azure-registration/3-get-azurestackhci.png" alt-text="显示注册后的 Azure 注册状态的屏幕截图。":::

如果超过了脱机操作的最长时间，则 `ConnectionStatus` 将显示 `OutOfPolicy` 。

## <a name="assign-azure-ad-app-permissions"></a>分配 Azure AD 应用权限

除了在订阅中创建 Azure 资源之外，注册 Azure Stack HCI 还会在 Azure AD 租户中创建应用标识。 此标识在概念上类似于用户。 应用标识会继承群集名称。 此标识代表订阅中的 Azure Stack HCI 云服务（如果适用）执行操作。

如果注册群集的用户是 Azure AD 管理员或具有足够的权限，则这一切都是自动进行的。 无需执行其他操作。 否则，你可能需要 Azure AD 管理员批准才能完成注册。 你的管理员可以向应用显式授予同意，也可以委派权限，使你可以向应用授予同意：

:::image type="content" source="media/manage-azure-registration/aad-permissions.png" alt-text="显示 Azure Active Directory 权限和标识的关系图。" border="false":::

若要授予许可，请打开 [portal.azure.com](https://portal.azure.com) ，并使用在 Azure AD 中具有足够权限的 azure 帐户登录。 转到“Azure Active Directory” > “应用注册”。  选择群集后的名为的应用标识，并访问 " **API 权限**"。

 (GA) 版本 Azure Stack HCI 公开上市时，应用需要以下权限。 它们不同于公共预览版中所需的应用程序权限。

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Cluster.ReadWrite

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.Read

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.ClusterNode.ReadWrite
```

对于公共预览版，应用权限 (现已弃用) ：

```http
https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Census.Sync

https://azurestackhci-usage.trafficmanager.net/AzureStackHCI.Billing.Sync
```

向 Azure AD 管理员寻求批准可能需要一些时间，因此该 `Register-AzStackHCI` cmdlet 将退出，并使注册处于 `pending admin consent` (部分完成) 状态。 授予同意后，重新运行 `Register-AzStackHCI` 以完成注册。

## <a name="assign-azure-ad-user-permissions"></a>分配 Azure AD 用户权限

运行的用户 `Register-AzStackHCI` 需要 Azure AD 权限来执行以下操作：

- 创建 (`New-Remove-AzureADApplication`) 、获取 (`Get-Remove-AzureADApplication`) 、设置 ()  () `Set-Remove-AzureADApplication` `Remove-AzureADApplication` 应用程序 Azure AD。
- 创建 (`New-Get-AzureADServicePrincipal`) 或获取 (`Get-AzureADServicePrincipal`) Azure AD 服务主体。
-  (`New-Remove-AzureADApplicationKeyCredential` 、或) 管理 Active Directory 应用程序机密 `Get-Remove-AzureADApplicationKeyCredential` `Remove-AzureADApplicationKeyCredential` 。
- 授予同意使用特定应用程序权限 (`New-AzureADApplicationKeyCredential` 、 `Get-AzureADApplicationKeyCredential` 或 `Remove-AzureADServiceAppRoleAssignments`) 。

有三种方法可分配这些权限。

### <a name="option-1-allow-any-user-to-register-applications"></a>选项1：允许任何用户注册应用程序

在 Azure Active Directory 中，请参阅 "**用户设置**"  >  **应用注册**。 在 " **用户可以注册应用程序**" 下，选择 **"是"**。

此选项允许任何用户注册应用程序。 但是，用户仍需要 Azure AD 管理员在进行群集注册期间授予同意。 

> [!NOTE]
> 此选项是租户级设置，因此它可能不适用于大型企业客户。

### <a name="option-2-assign-the-cloud-application-administration-role"></a>选项2：分配云应用程序管理角色

将内置的 *云应用程序管理* Azure AD 角色分配给用户。 此分配允许用户注册和注销群集，而无需额外的 Active Directory 管理员许可。

### <a name="option-3-create-a-custom-active-directory-role-and-consent-policy"></a>选项3：创建自定义 Active Directory 角色和许可策略

最严格的选项是使用自定义同意策略创建自定义的 Active Directory 角色，该策略将租户范围内的管理员许可委托给 Azure Stack HCI 服务所需的权限。 将此自定义角色分配给用户时，他们可以注册并授予许可，而无需额外的 Active Directory 管理员许可。

> [!NOTE]
> 此选项需要 Azure AD Premium 许可证。 它使用自定义 Active Directory 角色和自定义同意策略功能，这些功能现在为公共预览版。

1. 连接到 Azure AD：
   
   ```powershell
   Connect-AzureAD
   ```

2. 创建自定义同意策略：

   ```powershell
   New-AzureADMSPermissionGrantPolicy -Id "AzSHCI-registration-consent-policy" -DisplayName "Azure Stack HCI registration admin app consent policy" -Description "Azure Stack HCI registration admin app consent policy"
   ```

3. 添加一个条件，其中包括包含应用 ID 1322e676-dee7-41ee-a874-ac923822781c 的 Azure Stack HCI 服务所需的应用权限。 
   
   > [!NOTE]
   > 以下权限适用于 Azure Stack HCI 的 GA 版本。 除非已将 [2020 年11月23日)  (的预览更新 ](https://support.microsoft.com/help/4595086/azure-stack-hci-release-notes-overview) 应用到群集中的每个服务器，并且下载了 Az. StackHCI module 版本0.4.1 或更高版本，否则它们不会使用公共预览。
   
   ```powershell
   New-AzureADMSPermissionGrantConditionSet -PolicyId "AzSHCI-registration-consent-policy" -ConditionSetType "includes" -PermissionType "application" -ResourceApplication "1322e676-dee7-41ee-a874-ac923822781c" -Permissions "bbe8afc9-f3ba-4955-bb5f-1cfb6960b242","8fa5445e-80fb-4c71-a3b1-9a16a81a1966","493bd689-9082-40db-a506-11f40b68128f","2344a320-6a09-4530-bed7-c90485b5e5e2"
   ```

4. 授予权限以允许注册 Azure Stack HCI，请注意你在步骤2：
   
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

5. 创建新的自定义 Active Directory 角色：

   ```powershell
   $customADRole = New-AzureADMSRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
   ```

6. 按照 [以下说明](/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal?context=/azure/active-directory/roles/context/ugr-context) 将新的自定义 Active Directory 角色分配给将向 Azure 注册 Azure Stack HCI 群集的用户。

## <a name="unregister-azure-stack-hci-by-using-windows-admin-center"></a>使用 Windows 管理中心取消注册 Azure Stack HCI

准备好停止 Azure Stack HCI 群集后，请使用 Windows 管理中心连接到该群集。 选择左侧 "**工具**" 菜单底部的 "**设置**"。 然后选择 " **AZURE STACK HCI 注册**"，并选择 " **取消注册** " 按钮。 

注销过程会自动清理代表群集的 Azure 资源，Azure 资源组 (如果组是在注册期间创建的，并且不包含任何其他资源) 和 Azure AD 应用标识。 此清除通过 Azure Arc 停止所有监视、支持和计费功能。

> [!NOTE]
> 取消注册 Azure Stack HCI 群集需要 Azure AD 管理员或具有 [足够权限](#assign-azure-ad-user-permissions)的其他用户。 
>
> 如果你的 Windows 管理中心网关注册到了不同于最初注册群集时所用 (租户) ID 的 Azure Active Directory，则在尝试使用 Windows 管理中心取消注册群集时可能会遇到问题。 如果发生这种情况，请使用以下 PowerShell 说明。

## <a name="unregister-azure-stack-hci-by-using-powershell"></a>使用 PowerShell 注销 Azure Stack HCI

你还可以使用 `Unregister-AzStackHCI` cmdlet 取消注册 AZURE STACK HCI 群集。 可以在群集节点上或从管理 PC 上运行 cmdlet。

可能需要安装最新版本的 `Az.StackHCI` 模块。 如果系统提示你 `Are you sure you want to install the modules from 'PSGallery'?` ，请回答是 (`Y`) 。

```PowerShell
Install-Module -Name Az.StackHCI
```

### <a name="unregister-from-a-cluster-node"></a>从群集节点注销

如果在 `Unregister-AzStackHCI` 群集中的服务器上运行此 cmdlet，请使用以下语法。 指定你的 Azure 订阅 ID 以及要取消注册 Azure Stack HCI 群集的资源名称。

```PowerShell
Unregister-AzStackHCI -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

系统将提示你在另一台设备 (如电脑或手机) 上访问 microsoft.com/devicelogin。 输入代码，然后登录到 Azure，以便在 Azure 中进行身份验证。

### <a name="unregister-from-a-management-pc"></a>从管理 PC 中注销

如果是从管理 PC 运行 cmdlet，还需要在群集中指定服务器的名称：

```PowerShell
Unregister-AzStackHCI -ComputerName ClusterNode1 -SubscriptionId "e569b8af-6ecc-47fd-a7d5-2ac7f23d8bfe" -ResourceName HCI001
```

此时将显示一个交互式 Azure 登录窗口。 你看到的确切提示将因安全设置而异 (例如，双因素身份验证) 。 按照提示进行登录。

## <a name="clean-up-after-a-cluster-that-was-not-properly-unregistered"></a>在未正确注销群集后进行清理

如果用户在未取消注册 Azure Stack HCI 群集的情况下销毁该群集，例如通过重新映像主机服务器或删除虚拟群集节点，则项目将保留在 Azure 中。 这些项目无害，不会产生计费或使用资源，但会打乱 Azure 门户。 若要清理它们，可以手动将其删除。

若要删除 Azure Stack HCI 资源，请在 Azure 门户中切换到其页面，然后从顶部的操作栏中选择 " **删除** "。 输入用于确认删除的资源的名称，然后选择 " **删除**"。 

若要删除 Azure AD 应用标识，请参阅 " **Azure AD**  >  **应用注册**  >  **所有应用程序**"。 选择 " **删除** 并确认"。

还可以使用 PowerShell 删除 Azure Stack HCI 资源：

```PowerShell
Remove-AzResource -ResourceId "HCI001"
```

可能需要安装该 `Az.Resources` 模块：

```PowerShell
Install-Module -Name Az.Resources
```

如果资源组是在注册期间创建的，并且不包含任何其他资源，则也可以将其删除：

```PowerShell
Remove-AzResourceGroup -Name "HCI001-rg"
```

## <a name="next-steps"></a>后续步骤

如需相关信息，请参阅：

- [将 Windows Admin Center 注册到 Azure](register-windows-admin-center.md)
- [将 Azure Stack HCI 连接到 Azure](../deploy/register-with-azure.md)