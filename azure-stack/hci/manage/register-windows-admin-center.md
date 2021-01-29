---
title: 将 Windows Admin Center 注册到 Azure
description: 如何将 Windows 管理中心网关注册到 Azure。
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 01/28/2021
ms.openlocfilehash: d98cb4f0531903d27d97d9fb055c7ae2db35a65e
ms.sourcegitcommit: b461597917b768412036bf852c911aa9871264b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050053"
---
# <a name="register-windows-admin-center-with-azure"></a>将 Windows Admin Center 注册到 Azure

> 适用于 Azure Stack HCI v20H2；Windows Server 2019

若要在 Windows 管理中心使用 Azure 服务，必须首先在管理 PC 上安装 Windows 管理中心并完成 Windows 管理中心网关的一次性注册。 这是将群集注册到 Azure 的先决条件，应在计划用于完成 [群集注册](../deploy/register-with-azure.md) 过程的相同管理 PC 上，使用相同的 AZURE 订阅 id 和租户 id。

## <a name="complete-the-gateway-registration-process-using-windows-admin-center"></a>使用 Windows 管理中心完成网关注册过程

1. 启动 Windows Admin Center，单击右上角的“设置”齿轮图标，随即会转到“帐户”页。 然后，在左侧的“网关”菜单中，选择“Azure”，然后单击“注册”  。

   :::image type="content" source="media/register-wac/register-wac.png" alt-text="选择 &quot;设置&quot; > 网关 > Azure &quot;，然后单击&quot; 注册 &quot;" lightbox="media/register-wac/register-wac.png":::

2. 系统会向你提供一个唯一代码。 单击代码右侧的“复制”按钮。 单击“输入代码”，这将打开另一个浏览器窗口，你可以在其中粘贴应用或设备上显示的代码。

   :::image type="content" source="media/register-wac/enter-code.png" alt-text="复制唯一的代码，单击 &quot;输入代码&quot;，并将其粘贴到对话框" lightbox="media/register-wac/enter-code.png":::

3. 粘贴代码后，系统提示即将登录到远程设备或服务上的 Windows Admin Center。 输入电子邮件或电话号码。 如果你的设备为托管设备，你将转到组织的登录页面进行身份验证。 按照说明进行操作并输入相应的凭据。

   :::image type="content" source="media/register-wac/sign-in.png" alt-text="使用电子邮件或电话号码登录到 Windows 管理中心" lightbox="media/register-wac/sign-in.png":::

   应会看到以下消息：“你已登录到你设备上的 Windows Admin Center 应用程序。” 关闭浏览器窗口以返回原始注册页。

4. 通过提供 Azure Active Directory（租户）ID 连接到 Azure Active Directory。 如果你已有一个 Azure 租户 ID 并执行了前面的步骤，则可能会预填充 ID 字段。 如果你的组织未向你提供现有 ID，请将“Azure Active Directory 应用程序”设置为“新建” 。 如果你已有 ID，请单击“使用现有项”，系统将显示一个空字段，供你输入管理员提供的 ID。输入 ID 后，Windows Admin Center 将确认找到具有该 ID 的帐户。 如果你有现有 ID 但不知道它是什么，请按照[这些步骤](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) 检索此 ID。

   :::image type="content" source="media/register-wac/connect-to-aad.png" alt-text="通过提供现有 Azure Active Directory (租户) ID 或创建一个新的来连接到 Azure Active Directory" lightbox="media/register-wac/connect-to-aad.png":::

5. 单击“连接”按钮连接到 Azure。 应会看到一条确认消息，指示现在已经连接到 Azure AD。

6. 转到 Azure 门户中的“应用权限”，在 Azure 中授予权限。 在“授予许可”下，选择“授予管理员许可”。 

7. 关闭窗口，并通过 Azure 帐户登录到 Windows Admin Center。

## <a name="next-steps"></a>后续步骤

现在可以执行以下操作：

- [将 Azure Stack HCI 连接到 Azure](../deploy/register-with-azure.md)
- [在 Windows 管理中心使用 Azure Stack HCI](../get-started.md)
- [连接到 Azure 混合服务](/windows-server/manage/windows-admin-center/azure/)