---
title: 让云解决方案提供商管理 Azure Stack Hub 订阅
description: 了解如何让云解决方案提供商 (CSP) 为你管理 Azure Stack Hub 订阅。
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: 95cc91a2f9443271b0e89d6dbf22a67137924627
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77704295"
---
# <a name="let-your-cloud-solution-provider-manage-your-azure-stack-hub-subscription"></a>让云解决方案提供商管理 Azure Stack Hub 订阅

如果你通过云解决方案提供商 (CSP) 使用 Azure Stack Hub，则可以选择管理自己的订阅以访问 Azure 和 Azure Stack Hub 中的资源。 还可以让提供商管理你的订阅。 本文介绍如何：

* 让服务提供商访问你的订阅。
* 确保服务提供商可以管理你的服务。

> [!NOTE]
> 如果 CSP 没有管理你的帐户，并且你跳过以下步骤，则 CSP 将无法为你管理 Azure Stack Hub 订阅。

## <a name="manage-your-subscription-with-a-csp"></a>使用 CSP 管理订阅

将 CSP 作为**用户**添加到你的订阅。

1. 将 CSP 作为具有**用户**角色的来宾用户添加到你的租户目录。 有关添加用户的帮助，请参阅[将新用户添加到 Azure Active Directory](/azure/active-directory/add-users-azure-active-directory)。

2. CSP 将为你创建本地 Azure Stack Hub 订阅。 你就可以开始使用 Azure Stack Hub。

3. CSP 应在你的订阅中创建资源，以确认他们还可以管理你的资源。 例如，他们可以[使用 Azure Stack Hub 门户创建 Windows 虚拟机](azure-stack-quick-windows-portal.md)。

## <a name="let-the-csp-manage-your-subscription-using-rbac-rights"></a>让 CSP 使用 RBAC 权限管理你的订阅

将 CSP 作为**所有者**添加到你的订阅。

1. 将 CSP 作为来宾用户添加到你的租户目录。 有关添加用户的信息，请参阅[向 Azure Active Directory 中添加新用户](/azure/active-directory/add-users-azure-active-directory)。

2. 将“所有者”  角色添加到 CSP 来宾用户。 有关将 CSP 用户添加到订阅的信息，请参阅[使用基于角色的访问控制管理对 Azure 订阅资源的访问权限](/azure/role-based-access-control/role-assignments-portal)。 CSP 将为你创建本地 Azure Stack Hub 订阅。 你就可以开始使用 Azure Stack Hub。
3. CSP 应在你的订阅中创建资源，以确认他们可以管理你的资源。

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何从 Azure Stack Hub 检索资源用量信息，请参阅 [Azure Stack Hub 中的用量和计费](../operator/azure-stack-billing-and-chargeback.md)。
