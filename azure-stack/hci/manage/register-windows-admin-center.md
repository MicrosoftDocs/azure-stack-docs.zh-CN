---
title: 将 Windows Admin Center 注册到 Azure
description: 如何将 Windows 管理中心网关注册到 Azure。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 03/05/2021
ms.openlocfilehash: 18da9a3e09ebabfc94784ff803a0d39ca90b3caf
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231653"
---
# <a name="register-windows-admin-center-with-azure"></a>将 Windows Admin Center 注册到 Azure

> 适用于 Azure Stack HCI 版本 20H2；Windows Server 2019

若要在 Windows 管理中心使用 Azure 服务，必须首先在管理 PC 上 [安装 Windows 管理中心](/windows-server/manage/windows-admin-center/deploy/install) 并注册 windows 管理中心网关。 这是将 [群集注册](../deploy/register-with-azure.md) 到 Azure 的先决条件。

   > [!IMPORTANT]
   > 使用相同的 Azure Active Directory (租户) ID 和应用程序 ID，在计划用于注册群集 () 的相同管理 PC 上注册 Windows 管理中心。

## <a name="complete-the-registration-process"></a>完成注册过程

1. 启动 Windows Admin Center，单击右上角的“设置”齿轮图标，随即会转到“帐户”页。 然后，在左侧的“网关”菜单中，选择“Azure”，然后单击“注册”  。

   :::image type="content" source="media/register-wac/register-wac.png" alt-text="选择 &quot;设置&quot; > 网关 > Azure &quot;，然后单击&quot; 注册 &quot;" lightbox="media/register-wac/register-wac.png":::

2. 系统会向你提供一个唯一代码。 单击代码右侧的“复制”按钮。 单击“输入代码”，这将打开另一个浏览器窗口，你可以在其中粘贴应用或设备上显示的代码。

   :::image type="content" source="media/register-wac/enter-code.png" alt-text="复制唯一的代码，单击 &quot;输入代码&quot;，并将其粘贴到对话框" lightbox="media/register-wac/enter-code.png":::

3. 粘贴代码后，系统提示即将登录到远程设备或服务上的 Windows Admin Center。 输入电子邮件或电话号码。 如果你的设备为托管设备，你将转到组织的登录页面进行身份验证。 按照说明进行操作并输入相应的凭据。

   :::image type="content" source="media/register-wac/sign-in.png" alt-text="使用电子邮件或电话号码登录到 Windows 管理中心" lightbox="media/register-wac/sign-in.png":::

   应会看到以下消息：“你已登录到你设备上的 Windows Admin Center 应用程序。” 关闭浏览器窗口以返回原始注册页。

4. 通过提供 Azure Active Directory (租户) ID 和应用程序 ID 来连接到 Azure Active Directory。 如果你已有一个 Azure 租户 ID 并执行了前面的步骤，则 "租户 ID" 字段可能会预先填充，并且可能包含多个选项。 选择正确的租户 ID。 

   如果 Azure AD 管理员为你提供了应用程序 ID，请单击 " **使用现有**"，将显示一个空字段，供你输入管理员提供的 ID。输入 ID 后，Windows 管理中心将确认找到具有该 ID 的帐户。 如果你有现有 ID 但不知道它是什么，请按照[这些步骤](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) 检索此 ID。 如果你的组织未提供现有 ID，请将 **Azure Active Directory 应用程序** 设置为 " **新建**"。

   :::image type="content" source="media/register-wac/connect-to-aad.png" alt-text="通过提供现有 Azure Active Directory (租户) ID 和应用程序 ID，或创建新的应用程序 ID 来连接到 Azure Active Directory" lightbox="media/register-wac/connect-to-aad.png":::

5. 单击“连接”按钮连接到 Azure。 如果你是 Azure AD 管理员或使用了现有的应用程序 ID，你应该会看到一条确认，指出你现在已连接到 Azure AD，指示该过程已完成。 否则，你可能会看到一条需要管理员批准的消息。 如果是这种情况，请选择 **"返回到应用程序而不授予许可**"，并与 Azure AD 管理员联系，以根据步骤6中的说明，向根据注册创建的新应用程序 ID 授予权限。

6. 如果你是 Azure AD 管理员，请导航到 **Azure Active Directory**，然后 **应用注册**，在 Azure 中授予权限。 选择 " **所有应用程序** " 并搜索 **WindowsAdminCenter**。 选择要注册的网关的 "显示名称"。 记下显示在页面顶部附近的 **应用程序 (客户端) ID** ，因为需要将其提供给用户。 接下来，导航到 " **API 权限**"。 在“授予许可”下，选择“授予管理员许可”。  然后，将应用程序 ID 赋给用户。

7. 此时，用户必须重复注册过程，这一次请选择 " **使用现有** 应用程序 id"，并指定 Azure AD 管理员提供的应用程序 id。

8. 选择 **"登录"** ，通过 Azure 帐户登录到 Windows 管理中心。

## <a name="next-steps"></a>后续步骤

现在可以执行以下操作：

- [将 Azure Stack HCI 连接到 Azure](../deploy/register-with-azure.md)
- [在 Windows 管理中心使用 Azure Stack HCI](../get-started.md)
- [连接到 Azure 混合服务](/windows-server/manage/windows-admin-center/azure/)