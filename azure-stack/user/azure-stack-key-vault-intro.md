---
title: Azure Stack Key Vault 简介 | Microsoft Docs
description: 了解 Azure Stack Key Vault 如何管理密钥和机密
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/05/2019
ms.author: sethm
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 87c93f77011082d3e43b1c7d238999441f1b90c1
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "64298532"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Azure Stack 中的 Key Vault 简介

## <a name="prerequisites"></a>必备组件

* 必须订阅包含 Azure Key Vault 服务的产品/服务。  
* [PowerShell 配置为搭配 Azure Stack 使用](azure-stack-powershell-configure-user.md)。

## <a name="key-vault-basics"></a>Key Vault 基础知识

Azure Stack 中的 Key Vault 可帮助保护云应用程序和服务使用的加密密钥和机密。 通过使用 Key Vault，可以加密密钥和机密，例如：

* 身份验证密钥
* 存储帐户密钥
* 数据加密密钥
* .pfx 文件
* 密码

Key Vault 简化了密钥管理过程，可让我们控制用于访问和加密数据的密钥。 开发人员可以在几分钟内创建用于开发和测试的密钥，然后无缝地将其迁移到生产密钥。 安全管理员可以根据需要授予（和吊销）密钥权限。

只要拥有 Azure Stack 订阅，任何人都可以创建和使用密钥保管库。 尽管 Key Vault 能够为开发人员和安全管理员提供便利，但管理组织的其他 Azure Stack 服务的操作员也可以实现和管理 Key Vault。 例如，Azure Stack 操作员可以使用 Azure Stack 订阅登录、创建组织用来存储密钥的保管库，并负责执行以下操作任务：

* 创建或导入密钥或机密。
* 撤销或删除密钥或机密。
* 授权用户或应用程序访问密钥保管库，以便管理或使用其密钥和机密。
* 配置密钥用法（例如，签名或加密）。

然后，操作员可以向开发人员提供统一资源标识符 (URI)，以便从其应用程序调用。 操作员也可以向安全管理员提供密钥用法记录信息。

开发人员还可通过使用 API 直接管理密钥。 有关详细信息，请参阅 Key Vault 开发人员指南。

## <a name="scenarios"></a>方案

以下方案说明 Key Vault 如何帮助满足开发人员和安全管理员的需求。

### <a name="developer-for-an-azure-stack-application"></a>Azure Stack 应用程序开发人员

**问题：** 我想要编写使用密钥进行签名和加密的 Azure Stack 应用程序。 我希望这些密钥与应用程序分开，使解决方案适用于地理分散的应用程序。

**说明：** 密钥会存储在保管库中，可按需由 URI 调用。

### <a name="developer-for-software-as-a-service-saas"></a>软件即服务 (SaaS) 开发人员

**问题：** 对于客户的密钥和机密，我不想承担任何实际或潜在的法律责任。 我希望客户拥有并自行管理密钥，使我能够将全部精力集中在我的专长上，也就是提供核心软件功能。

**说明：** 客户可以将他们自己的密钥导入 Azure Stack 并进行管理。

### <a name="chief-security-officer-cso"></a>首席安全官 (CSO)

**问题：** 我想要确保我的组织掌控密钥生命周期，并可监视密钥的使用。

**说明：** Key Vault 设计用于确保 Microsoft 不会看到或提取你的密钥。 当应用程序必须使用客户密钥来执行加密操作时，Key Vault 会代表应用程序来使用密钥。 应用程序看不到客户密钥。 即使我们使用多个 Azure Stack 服务和资源，你可以从 Azure Stack 中的单一位置管理密钥。 不论你在 Azure Stack 中有几个保管库、保管库支持哪些区域以及使用哪些应用程序使用这些保管库，保管库都可提供单一界面。

## <a name="next-steps"></a>后续步骤

* [通过门户管理 Azure Stack 中的 Key Vault](azure-stack-key-vault-manage-portal.md)  
* [使用 PowerShell 管理 Azure Stack 中的 Key Vault](azure-stack-key-vault-manage-powershell.md)

