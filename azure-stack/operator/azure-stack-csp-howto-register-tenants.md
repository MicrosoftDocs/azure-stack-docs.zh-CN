---
title: 将使用情况和计费的租户添加到 Azure Stack 中心 |Microsoft Docs
description: 了解如何向 Azure Stack 中心添加使用情况和计费的租户。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: 2b70d2e04883da963da6b410ac6e5e8c7bf7d396
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817680"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack-hub"></a>将使用情况和计费的租户添加到 Azure Stack 中心

*适用于： Azure Stack 集线器集成系统*

本文介绍如何将租户添加到由云解决方案提供商（CSP）管理的 Azure Stack 中心部署。 当新租户使用资源时，Azure Stack 集线器会将使用情况报告给其 CSP 订阅。

Csp 通常为其 Azure Stack 中心部署中的多个最终客户（租户）提供服务。 将租户添加到 Azure Stack 集线器注册可确保将每个租户的使用情况报告并计费到相应的 CSP 订阅。 如果未完成本文中所述的步骤，则租户使用情况将根据初始注册 Azure Stack 中心使用的订阅收费。 在将最终客户添加到 Azure Stack 集线器以便使用跟踪和管理其租户之前，必须将 Azure Stack 中心配置为 CSP。 有关步骤和资源，请参阅[管理 Azure Stack 中心的使用情况和计费作为云解决方案提供商](azure-stack-add-manage-billing-as-a-csp.md)。

下图显示了 CSP 需要遵循的步骤，以使新的最终客户能够使用 Azure Stack 中心，并为客户设置使用情况跟踪。 通过添加最终客户，你还可以管理 Azure Stack 集线器中的资源。 可以通过两种方法来管理资源：

- 你可以维护最终客户并为最终客户提供本地 Azure Stack 中心订阅的凭据。  
- 最终用户可以在本地使用其订阅，并添加 CSP 作为具有所有者权限的来宾。

## <a name="add-an-end-customer"></a>添加最终客户

添加最终客户之前，必须在注册时启用多租户计费。 若要启用多租户计费，请将注册订阅 ID、资源组名称和注册名称发送到 `azstcsp@microsoft.com`。 它通常需要1-2 个工作日内才能实现多租户。

执行以下步骤以添加最终客户，如下图所示：

![为使用情况跟踪设置云解决方案提供程序并管理最终客户帐户](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>在合作伙伴中心创建新客户

在合作伙伴中心，为客户创建新的 Azure 订阅。 有关说明，请参阅[添加新客户](/partner-center/add-a-new-customer)。

### <a name="create-an-azure-subscription-for-the-end-customer"></a>为最终用户创建 Azure 订阅

在合作伙伴中心中创建客户的记录后，你可以向他们销售目录中产品的订阅。 有关说明，请参阅[创建、暂停或取消客户订阅](/partner-center/create-a-new-subscription)。

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>在最终客户目录中创建来宾用户

默认情况下，作为 CSP，你将无法访问最终客户的 Azure Stack 中心订阅。 但是，如果你的客户想要管理其资源，则他们可以将你的帐户作为所有者/参与者添加到其 Azure Stack 中心订阅。 要执行此操作，需要将你的帐户作为来宾用户添加到其 Azure AD 租户。 建议你使用 Azure CSP 帐户中的其他帐户来管理客户的 Azure Stack 中心订阅，以确保你不会失去对客户的 Azure 订阅的访问权限。

### <a name="update-the-registration-with-the-end-customer-subscription"></a>更新最终客户订阅的注册

用新的客户订阅更新你的注册。 Azure 使用合作伙伴中心的客户标识报告客户使用情况。 此步骤可确保将每个客户的使用情况报告在该客户的单个 CSP 订阅下。 这样可以更轻松地跟踪使用情况和计费。 若要执行此步骤，必须先[注册 Azure Stack 集线器](azure-stack-registration.md)。

1. 使用权限提升的提示符打开 Windows PowerShell，然后运行：  

   ```powershell
   Add-AzureRmAccount
   ```

   >[!Note]
   > 如果会话过期，你的密码已更改，或者你只是想要切换帐户，请运行以下 cmdlet，然后使用 Add-azurermaccount： `Remove-AzureRmAccount-Scope Process`

2. 键入你的 Azure 凭据。
3. 在 PowerShell 会话中，运行：

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
   ```

### <a name="new-azurermresource-powershell-parameters"></a>Move-azurermresource PowerShell 参数

以下部分介绍了**move-azurermresource** cmdlet 的参数：

| 参数 | Description |
| --- | --- |
|registrationSubscriptionID | 用于初始 Azure Stack 中心注册的 Azure 订阅。|
| customerSubscriptionID | 属于要注册的客户的 Azure 订阅（不 Azure Stack 中心）。 必须在 CSP 服务中创建。 在实践中，这意味着通过合作伙伴中心。 如果客户有多个 Azure Active Directory 租户，则必须在将用于登录到 Azure Stack 中心的租户中创建此订阅。 客户订阅 ID 必须使用小写字母。 |
| resourceGroup | Azure 中存储注册的资源组。 |
| registrationName | Azure Stack 中心注册的名称。 它是存储在 Azure 中的对象。 

> [!NOTE]  
> 租户必须使用其使用的每个 Azure Stack 中心进行注册。 如果有两个 Azure Stack 中心部署，并且一个租户同时使用这两个部署，则必须使用租户订阅更新每个部署的初始注册。

### <a name="onboard-tenant-to-azure-stack-hub"></a>将租户加入到 Azure Stack 中心

配置 Azure Stack 集线器，以支持多个 Azure AD 租户中的用户使用 Azure Stack 集线器中的服务。 有关说明，请参阅[在 Azure Stack Hub 中启用多租户](azure-stack-enable-multitenancy.md)。

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack-hub"></a>在 Azure Stack Hub 中的最终客户租户中创建本地资源

将新客户添加到 Azure Stack 中心，或最终客户租户已使用所有者权限启用来宾帐户后，请验证是否可以在其租户中创建资源。 例如，他们可以[使用 Azure Stack 中心门户创建 Windows 虚拟机](../user/azure-stack-quick-windows-portal.md)。

## <a name="next-steps"></a>后续步骤

- 若要查看在你的注册过程中触发的错误消息，请参阅[租户注册错误消息](azure-stack-registration-errors.md)。
- 若要了解有关如何从 Azure Stack 中心检索资源使用情况信息的详细信息，请参阅[Azure Stack 中心中的使用情况和计费](azure-stack-billing-and-chargeback.md)。
- 若要查看最终客户如何向你添加 CSP，作为其 Azure Stack 中心租户的管理员，请参阅[启用云解决方案提供商以管理你的 Azure Stack 中心订阅](../user/azure-stack-csp-enable-billing-usage-tracking.md)。
