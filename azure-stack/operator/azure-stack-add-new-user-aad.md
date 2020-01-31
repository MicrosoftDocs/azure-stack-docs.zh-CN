---
title: 在 Azure Active Directory 中添加新的 Azure Stack 集线器用户帐户
description: 了解如何在 Azure Active Directory 中创建用户帐户，以便你可以浏览用户门户。
author: JustinHall
ms.topic: article
ms.date: 05/20/2019
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: b7bfcf97df22f5ca0d1dcaa7c9687079656af840
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890147"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-azure-active-directory-azure-ad"></a>在 Azure Active Directory 中添加新的 Azure Stack 集线器用户帐户（Azure AD）

你需要一个用户帐户，然后才能测试产品/服务和计划，并创建资源。 使用 Azure 门户或 PowerShell 在 Azure AD 租户中创建用户帐户。

## <a name="create-user-account-using-the-azure-portal"></a>使用 Azure 门户创建用户帐户

你必须拥有 Azure 订阅才能使用 Azure 门户。

1. 登录到[Azure](https://portal.azure.com)。
2. 在左侧导航栏中，选择 " **Active Directory** "，并切换到要用于 Azure Stack 中心的目录（或创建一个新的目录）。
3. 选择 " **Azure Active Directory** > **用户**" > "**新建用户**"。

    ![用户 - 突出显示新用户的所有用户页面](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. 在 "**用户**" 页上，填写所需的信息。

    ![添加新用户，具有用户信息的用户页](media/azure-stack-add-new-user-aad/new-user-user.png)

   - **名称（必需）** ：新用户的名字和姓氏。 例如，Mary Parker。
   - **用户名（必需）** ：新用户的用户名。 例如，mary@contoso.com 。
       用户名的域名部分必须是初始默认域名“<yourdomain name>.onmicrosoft.com”，或自定义域名（例如“contoso.com”）。 有关如何创建自定义域名的详细信息，请参阅[如何将自定义域名添加到 Azure AD](/azure/active-directory/fundamentals/add-custom-domain)。
   - **配置文件**：可以选择添加有关用户的详细信息。 你还可以在以后添加用户信息。 有关添加用户信息的详细信息，请参阅[如何添加或更改用户配置文件信息](/azure/active-directory/fundamentals/active-directory-users-profile-azure-portal)。
   - **目录角色**：选择 "**用户**"。

5. 选中 "**显示密码**"，并复制 "**密码**" 框中提供的自动生成的密码。 初始登录过程需要此密码。

6. 选择“创建”。

    用户已创建并添加到 Azure AD 租户。

7. 用新帐户登录到 Azure 门户。 在出现提示时更改密码。
8. 用新帐户登录到 `https://portal.local.azurestack.external` 以查看用户门户。

## <a name="create-a-user-account-using-powershell"></a>使用 PowerShell 创建用户帐户

如果没有 Azure 订阅，则不能使用 Azure 门户来添加租户用户帐户。 在这种情况下，可以改为使用适用于 Windows PowerShell 的 Azure AD 模块。

> [!NOTE]
> 如果使用 Microsoft 帐户部署 ASDK，则不能使用 Azure AD PowerShell 创建租户帐户。

1. 安装[适用于 IT 专业人员 RTW 的 Microsoft Online Services 登录助手](https://go.microsoft.com/fwlink/p/?LinkId=286152) **64 位**版本。

2. 按照以下步骤安装用于 Windows PowerShell 的 Microsoft Azure AD 模块：

    - 打开提升的 Windows PowerShell 命令提示符（以管理员身份运行 Windows PowerShell）。
    - 运行**MSOnline**命令。
    - 如果系统提示你安装 NuGet 提供程序，请选择 " **Y** "，然后**输入**。
    - 如果系统提示从 PSGallery 安装模块，请选择 " **Y** "，然后**输入**。

3. 运行以下 cmdlet：

    ```powershell
    # Provide the Azure AD credential you use to deploy the ASDK.

            $msolcred = get-credential

    # Add a user account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. 用新帐户登录到 Azure。 在出现提示时更改密码。
2. 用新帐户登录到 `https://portal.local.azurestack.external` 以查看用户门户。

## <a name="next-steps"></a>后续步骤

[向 AD FS 中添加 Azure Stack 集线器用户](azure-stack-add-users-adfs.md)
