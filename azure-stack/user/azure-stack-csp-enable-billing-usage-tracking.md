---
title: 允许云服务提供商管理 Azure Stack 订阅 | Microsoft Docs
description: 允许云服务提供商访问 Azure Stack 中的订阅。
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
ms.date: 05/20/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: 209a0da31d4bd5b519771f68f4bddcbb833284fa
ms.sourcegitcommit: d2012e765c3fa5bccb4756d190349e890f9f48bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65941209"
---
# <a name="enable-a-cloud-service-provider-to-manage-your-azure-stack-subscription"></a>允许云服务提供商管理 Azure Stack 订阅

*适用于：Azure Stack 集成系统*

如果你通过云服务提供商 (CSP) 使用 Azure Stack，则可以选择管理自己的订阅以访问 Azure 和 Azure Stack 中的资源。 还可以让提供商管理你的订阅。 本文介绍以下操作：

* 向你的订阅的服务提供程序访问权限。
* 确保服务提供商可以管理你的服务。

> [!NOTE]
> 如果 CSP 没有管理你的帐户，并且你跳过以下步骤，则 CSP 将无法为你管理 Azure Stack 订阅。

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>使用云服务提供商管理订阅

将 CSP 作为**用户**添加到你的订阅。

1. 将 CSP 作为具有的来宾用户**用户**到你的租户目录角色。 有关添加用户的步骤，请参阅[将新用户添加到 Azure Active Directory](/azure/active-directory/add-users-azure-active-directory)
2. CSP 将为你创建本地 Azure Stack 订阅。 你就可以开始使用 Azure Stack。
3. CSP 应在你的订阅中创建资源，以确认他们还可以管理你的资源。 例如，他们可以[使用 Azure Stack 门户创建 Windows 虚拟机](azure-stack-quick-windows-portal.md)。

## <a name="enable-the-cloud-service-provider-to-manage-your-subscription-using-rbac-rights"></a>允许云服务提供商使用 RBAC 权限管理你的订阅

将 CSP 作为**所有者**添加到你的订阅。

1. 将 CSP 作为来宾用户添加到你的租户目录。 若要将用户添加的步骤，请参阅[将新用户添加到 Azure Active Directory](/azure/active-directory/add-users-azure-active-directory)。
2. 将“所有者”角色添加到 CSP 来宾用户。 有关将 CSP 用户添加到订阅的步骤，请参阅[使用基于角色的访问控制管理对 Azure 订阅资源的访问权限](/azure/role-based-access-control/role-assignments-portal)。 CSP 将为你创建本地 Azure Stack 订阅。 你就可以开始使用 Azure Stack。
3. CSP 应在你的订阅中创建资源，以确认他们可以管理你的资源。

## <a name="next-steps"></a>后续步骤

* 若要详细了解如何从 Azure Stack 检索资源使用情况信息，请参阅 [Azure Stack 中的使用情况和计费](../operator/azure-stack-billing-and-chargeback.md)。
