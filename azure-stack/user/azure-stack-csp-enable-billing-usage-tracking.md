---
title: 让云解决方案提供商管理 Azure Stack 中心订阅 |Microsoft Docs
description: 了解如何让云解决方案提供商（CSP）管理你的 Azure Stack 中心订阅。
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
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: 44d00381ea11ef6ac7e947c31adce41b3228d93d
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883773"
---
# <a name="let-your-cloud-solution-provider-manage-your-azure-stack-hub-subscription"></a>让云解决方案提供商管理 Azure Stack 中心订阅

如果将 Azure Stack 中心与云解决方案提供商（CSP）结合使用，则可以选择管理自己的订阅，以访问 Azure 和 Azure Stack 中心的资源。 你还可以让提供商管理你的订阅。 本文介绍如何：

* 向服务提供商提供对订阅的访问权限。
* 请确保服务提供商可以管理你的服务。

> [!NOTE]
> 如果 CSP 不管理你的帐户，并且你跳过以下步骤，则 CSP 无法为你管理你的 Azure Stack 中心订阅。

## <a name="manage-your-subscription-with-a-csp"></a>使用 CSP 管理你的订阅

将 CSP 作为**用户**添加到你的订阅。

1. 将 CSP 作为具有**用户**角色的来宾用户添加到租户目录。 有关添加用户的帮助，请参阅[将新用户添加到 Azure Active Directory](/azure/active-directory/add-users-azure-active-directory)。

2. CSP 会为你创建本地 Azure Stack 中心订阅。 你已准备好开始使用 Azure Stack 中心。

3. 你的 CSP 应在你的订阅中创建一个资源，以确认他们还可以管理你的资源。 例如，他们可以[使用 Azure Stack 中心门户创建 Windows 虚拟机](azure-stack-quick-windows-portal.md)。

## <a name="let-the-csp-manage-your-subscription-using-rbac-rights"></a>让 CSP 使用 RBAC 权限管理你的订阅

将 CSP 作为**所有者**添加到你的订阅。

1. 将 CSP 作为来宾用户添加到租户目录。 有关添加用户的信息，请参阅[将新用户添加到 Azure Active Directory](/azure/active-directory/add-users-azure-active-directory)。

2. 将 "**所有者**" 角色添加到 CSP guest 用户。 有关向订阅添加 CSP 用户的信息，请参阅[使用基于角色的访问控制管理对 Azure 订阅资源的访问权限](/azure/role-based-access-control/role-assignments-portal)。 CSP 会为你创建本地 Azure Stack 中心订阅。 你已准备好开始使用 Azure Stack 中心。
3. 你的 CSP 应在你的订阅中创建一个资源，以验证它们是否可以管理你的资源。

## <a name="next-steps"></a>后续步骤

* 若要了解有关如何从 Azure Stack 中心检索资源使用情况信息的详细信息，请参阅[Azure Stack 中心中的使用情况和计费](../operator/azure-stack-billing-and-chargeback.md)。
