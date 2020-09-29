---
title: 将租户添加到 Azure Stack Hub 以获取用量和计费信息
description: 了解如何将租户添加到 Azure Stack Hub 以获取用量和计费信息。
author: sethmanheim
ms.topic: article
ms.date: 9/02/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 5/28/2020
ms.openlocfilehash: e0d426ac19645cbbc318f66b827946534539d125
ms.sourcegitcommit: 7c01ab4b2e2250a7acd67d1c5ba27d15c1e8bce0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89448565"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack-hub"></a>将租户添加到 Azure Stack Hub 以获取用量和计费信息

本文介绍如何将租户添加到云解决方案提供商 (CSP) 管理的 Azure Stack Hub 部署。 当新租户使用资源时，Azure Stack Hub 会向其 CSP 订阅报告用量。

CSP 通常向其 Azure Stack Hub 部署中的多个最终客户（租户）提供服务。 将租户添加到 Azure Stack Hub 注册可确保向相应的 CSP 订阅报告每个租户的用量并计收费用。 如果未完成本文中的步骤，则会向 Azure Stack Hub 初始注册中使用的订阅计收租户使用费。 在将最终用户添加到 Azure Stack Hub 以跟踪用量并管理其租户之前，必须将 Azure Stack Hub 配置为 CSP。 有关步骤和资源，请参阅[管理充当云解决方案提供商的 Azure Stack Hub 的用量和计费](azure-stack-add-manage-billing-as-a-csp.md)。

下图演示了 CSP 需要执行哪些步骤才能让新的最终客户使用 Azure Stack Hub，并针对客户设置用量跟踪。 添加最终用户还可以管理 Azure Stack Hub 中的资源。 可通过两个选项来管理这些资源：

- 可以保留最终客户，并向最终客户提供本地 Azure Stack Hub 订阅的凭据。  
- 最终客户可以在本地使用其订阅，并将 CSP 添加为拥有所有者权限的来宾。

## <a name="add-an-end-customer"></a>添加最终客户

在添加最终客户之前，必须在注册中启用多租户计费。 若要启用多租户计费，请将注册订阅 ID、资源组名称和注册名称发送到 `azstcsp@microsoft.com`。 启用多租户通常需要 1-2 个工作日。

执行以下步骤以添加最终客户，如下图所示：

![设置云解决方案提供商以进行用量跟踪，以及管理最终客户帐户](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>在合作伙伴中心创建新客户

在合作伙伴中心，为客户创建新的 Azure 订阅。 有关说明，请参阅[添加新客户](/partner-center/add-a-new-customer)。

### <a name="create-an-azure-subscription-for-the-end-customer"></a>为最终客户创建 Azure 订阅

在合作伙伴中心创建客户记录后，可向客户销售目录中产品的订阅。 有关说明，请参阅[创建、暂停或取消客户订阅](/partner-center/create-a-new-subscription)。

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>在最终客户目录中创建来宾用户

默认情况下，作为 CSP 你将无权访问最终客户的 Azure Stack Hub 订阅。 但是，如果客户希望你管理其资源，那么他们可以将你的帐户作为所有者/参与者添加到其 Azure Stack Hub 订阅。 为此，他们必须将你的帐户作为来宾用户添加到其 Azure AD 租户。 建议你使用与 Azure CSP 帐户不同的帐户来管理客户的 Azure Stack Hub 订阅，以确保你不会失去访问客户的 Azure 订阅的权限。

### <a name="update-the-registration-with-the-end-customer-subscription"></a>更新最终客户订阅中的注册

更新在最终客户订阅中的注册 Azure 将使用合作伙伴中心的客户标识来报告客户用量。 此步骤可确保在每个客户的个人 CSP 订阅下报告该客户的用量。 这样可以简化用量跟踪和计费。 若要执行此步骤，必须先[注册 Azure Stack Hub](azure-stack-registration.md)。

1. 在权限提升的提示符窗口中打开 Windows PowerShell 并运行：  

   ```powershell
   Add-AzureRmAccount
   ```

   >[!NOTE]
   > 如果会话过期，密码已更改，或者只是希望切换帐户，请在使用 Add-AzureRmAccount 登录之前运行以下 cmdlet：`Remove-AzureRmAccount-Scope Process`****。

2. 键入 Azure 凭据。
3. 在 PowerShell 会话中运行：

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
   ```

### <a name="new-azurermresource-powershell-parameters"></a>New-AzureRmResource PowerShell 参数

以下部分描述 **New-AzureRmResource** cmdlet 的参数：

| 参数 | 说明 |
| --- | --- |
|registrationSubscriptionID | 用于 Azure Stack Hub 初始注册的 Azure 订阅。|
| customerSubscriptionID | 属于要注册的客户的 Azure 订阅（非 Azure Stack Hub）。 必须在 CSP 套餐中创建。 实际上，这意味着通过合作伙伴中心。 如果客户有多个 Azure Active Directory 租户，则必须在用于登录 Azure Stack Hub 的租户中创建此订阅。 客户订阅 ID 区分大小写。 |
| resourceGroup | Azure 中用于存储注册的资源组。 |
| registrationName | Azure Stack Hub 的注册名称。 它是 Azure 中存储的对象。

> [!NOTE]  
> 租户必须注册到它们使用的每个 Azure Stack Hub。 如果有两个 Azure Stack Hub 部署，并且某个租户要使用这两个部署，则必须使用租户订阅更新每个部署的初始注册。

### <a name="onboard-tenant-to-azure-stack-hub"></a>将租户加入到 Azure Stack Hub

配置 Azure Stack Hub，以支持多个 Azure AD 租户中的用户使用 Azure Stack Hub 中的服务。 有关说明，请参阅[在 Azure Stack Hub 中启用多租户](azure-stack-enable-multitenancy.md)。

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack-hub"></a>在 Azure Stack Hub 中的最终客户租户内创建本地资源

将新客户添加到 Azure Stack Hub，或者最终客户租户已启用拥有所有者特权的来宾帐户后，请验证是否可在其租户中创建资源。 例如，他们可以[使用 Azure Stack Hub 门户创建 Windows 虚拟机](../user/azure-stack-quick-windows-portal.md)。

## <a name="next-steps"></a>后续步骤

- 若要查看注册过程中触发的错误消息，请参阅[租户注册错误消息](azure-stack-registration-errors.md)。
- 若要详细了解如何从 Azure Stack Hub 检索资源用量信息，请参阅 [Azure Stack Hub 中的用量和计费](azure-stack-billing-and-chargeback.md)。
- 若要了解最终客户如何将你 (CSP) 添加为其 Azure Stack Hub 租户的管理员，请参阅[让云解决方案提供商管理 Azure Stack Hub 订阅](../user/azure-stack-csp-enable-billing-usage-tracking.md)。
