---
title: 旋转 Azure Stack 中心机密和证书上的应用服务
description: 了解如何在 Azure Stack 中心旋转 Azure App Service 使用的机密和证书
author: BryanLa
ms.topic: article
ms.date: 01/10/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: f1a42f5b04ea83d9ff9130fb63ba6833cd7d2914
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76876535"
---
# <a name="rotate-app-service-on-azure-stack-hub-secrets-and-certificates"></a>旋转 Azure Stack 中心机密和证书上的应用服务

这些说明仅适用于 Azure Stack 中心的 Azure App Service。  Azure Stack 中心的集中式秘密旋转过程不包含 Azure Stack 中心机密 Azure App Service 的旋转。  操作员可以监视系统中机密的有效性、上次更新的日期和在机密过期之前的剩余时间。

> [!Important]
> 由于 Azure Stack 集线器上的 Azure App Service 未与 Azure Stack 集线器警报服务集成，因此操作员不会收到 Azure Stack 中心仪表板上的机密过期警报。  操作员必须使用 Azure Stack 中心管理员门户中 Azure Stack 中心管理体验的 Azure App Service 定期监视其机密。

本文档包含轮换以下机密的过程：

* Azure Stack 中心 Azure App Service 中使用的加密密钥;
* Azure App Service 在 Azure Stack 集线器上用于与宿主数据库和计量数据库进行交互的数据库连接凭据;
* 用于保护终结点的 Azure Stack 中心 Azure App Service 使用的证书;
* Azure Stack 中心基础结构角色 Azure App Service 的系统凭据。

## <a name="rotate-encryption-keys"></a>旋转加密密钥

若要在 Azure Stack 中心旋转 Azure App Service 中使用的加密密钥，请完成以下步骤：

1. 在 Azure Stack 中心管理员门户中，请参阅应用服务管理体验。

1. 导航到**机密**菜单选项

1. 单击 "加密密钥" 部分中的 "**旋转**" 按钮

1. 单击 **"确定"** 以启动轮换过程。

1. 加密密钥经过旋转，并更新所有角色实例。 操作员可以使用 "**状态**" 按钮监视过程的状态。

## <a name="rotate-connection-strings"></a>旋转连接字符串

若要为应用服务托管和计量数据库的数据库连接字符串更新凭据，请完成以下步骤：

1. 在 Azure Stack 中心管理员门户中，请参阅应用服务管理体验。

1. 导航到**机密**菜单选项

1. 单击 "连接字符串" 部分中的 "**旋转**" 按钮

1. 提供**SQL SA 用户名**和**密码**，然后单击 **"确定"** 以启动轮换过程。 

1. 凭据将在 Azure App Service 角色实例中进行旋转。 操作员可以使用 "**状态**" 按钮监视过程的状态。

## <a name="rotate-certificates"></a>轮换证书

若要在 Azure Stack 中心旋转 Azure App Service 中使用的证书，请完成以下步骤：

1. 在 Azure Stack 中心管理员门户中，请参阅应用服务管理体验。

1. 导航到**机密**菜单选项

1. 单击 "证书" 部分中的 "**旋转**" 按钮

1. 为要旋转的证书提供**证书文件**和关联的**密码**，然后单击 **"确定"** 。

1. 在 Azure Stack 中心角色实例的 Azure App Service 中，证书将按需进行旋转。  操作员可以使用 "**状态**" 按钮监视过程的状态。

## <a name="rotate-system-credentials"></a>轮换系统凭据

若要在 Azure Stack 中心旋转 Azure App Service 中使用的系统凭据，请完成以下步骤：

1. 在 Azure Stack 中心管理员门户中，请参阅应用服务管理体验。

1. 导航到**机密**菜单选项

1. 单击 "系统凭据" 部分中的 "**旋转**" 按钮

1. 选择要旋转的系统凭据的**作用域**。  操作员可以选择为所有角色或单个角色轮换系统凭据。

1. 指定 "**本地管理员用户名**"、"新**密码**" 并确认**密码**，然后单击 **"确定"**

1. 在 Azure Stack 中心角色实例的相应 Azure App Service 中，凭据将按需进行旋转。  操作员可以使用 "**状态**" 按钮监视过程的状态。



