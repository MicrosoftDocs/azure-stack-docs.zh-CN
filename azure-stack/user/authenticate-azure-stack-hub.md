---
title: 如何获取 Azure Stack 集线器的身份验证信息 |Microsoft Docs
description: 了解如何获取 Azure Stack 集线器的身份验证信息
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/13/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/13/2019
ms.openlocfilehash: d04fcc37df7b610c85a2ba27a90f288bce5feae3
ms.sourcegitcommit: 7dd685fddf2f5d7a0c0a20fb8830ca5a061ed031
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76259896"
---
# <a name="get-authentication-information-for-azure-stack-hub"></a>获取 Azure Stack 集线器的身份验证信息

若要对 Azure Stack 中心进行身份验证，需要提供订阅 ID、租户 ID 和位置以及 Azure Stack 中心资源管理器终结点。 可以从 Azure Stack 中心的[Azure Stack 中心资源管理器终结点](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1910#the-azure-stack-hub-resource-manager-endpoint)获取这些值。 或者，可以使用本文中的步骤获取它们。

## <a name="values-needed-to-authenticate"></a>进行身份验证所需的值

需要以下信息：

-   **订阅 ID**  

    使用订阅 ID 可以访问 Azure Stack 集线器中的产品/服务。

-   **租户 ID**

    目录是保存有关用户、应用程序、组和服务主体的信息的容器。 目录租户是一个组织，例如 Microsoft 或你自己的公司。

-   **位置**

    位置（或区域）是在延迟定义的外围中部署的一组数据中心，并通过专用的区域低延迟网络进行连接。 使用 Azure Stack 集线器，你的位置可能包含本地数据中心，而不是 Azure 区域。

-   **Azure Stack 中心资源管理器终结点**

    Microsoft Azure 资源管理器是一种管理框架，管理员可通过它来部署、管理和监视 Azure 资源。 Azure 资源管理器可以通过单个操作以组任务而不是单个任务的形式处理这些任务。

## <a name="get-the-subscription-id"></a>获取订阅 ID

获取订阅 ID：

1.  登录到 Azure Stack 集线器用户门户。

2.  选择“所有服务”。

    > ![Azure Stack 中心身份验证信息订阅 ID 租户 ID](./media/authenticate-azure-stack-hub/azure-stack-hub-auth-info.png)

3.  选择 **订阅**。

4.  选择要使用的订阅。

5.  从 "**概述**" 中复制**订阅 ID** 。

## <a name="get-the-tenant-id"></a>获取租户 ID

获取租户 ID：

1.  登录到 Azure Stack 集线器用户门户。

2.  将鼠标悬停在边栏选项卡右上方的 "用户名" 上。

3.  **目录 id**是租户 id。

## <a name="get-the-azure-resource-manager-endpoint"></a>获取 Azure 资源管理器终结点

Azure 资源管理器终结点是用于 Azure Stack 中心的部署和管理服务的元数据终结点。 它提供一个管理层用于在 Azure 订阅中创建、更新和删除资源。

对于集成系统，Azure 资源管理器终结点的 URL 是：<br>`https://management.<location>.<fqdn>.com`

若要获取指向属性（如库终结点、图形终结点、门户终结点、登录终结点和访问群体）的元数据终结点，URL 为： `<ResourceManager>/metadata/endpoints?api-version=1.0`

## <a name="next-steps"></a>后续步骤

详细了解如何将[Azure Stack 中心资源管理器](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1910)与 Azure Stack Hub 配合使用。
