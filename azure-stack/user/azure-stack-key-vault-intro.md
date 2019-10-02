---
title: Azure Stack 中的 Key Vault 简介 | Microsoft Docs
description: 了解 Key Vault 如何管理 Azure Stack 中的密钥和机密。
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
ms.date: 10/01/2019
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 2ad70754a63660a2ad374a324e8778c405446d29
ms.sourcegitcommit: bbf3edbfc07603d2c23de44240933c07976ea550
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71714672"
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Azure Stack 中的 Key Vault 简介

## <a name="prerequisites"></a>先决条件

* 订阅包含 Azure Key Vault 服务的产品/服务。  
* PowerShell 已安装并[配置为与 Azure Stack 一起使用](azure-stack-powershell-configure-user.md)。

## <a name="key-vault-basics"></a>Key Vault 基础知识

Azure Stack 中的 Key Vault 可帮助保护云应用和服务使用的加密密钥和机密。 通过使用 Key Vault，可以加密密钥和机密，例如：

* 身份验证密钥
* 存储帐户密钥
* 数据加密密钥
* .pfx 文件
* 密码

Key Vault 简化了密钥管理过程，可让我们控制用于访问和加密数据的密钥。 开发人员可以在几分钟内创建用于开发和测试的密钥，然后无缝地将其迁移到生产密钥。 安全管理员可以根据需要授予（和撤销）密钥权限。

只要拥有 Azure Stack 订阅，任何人都可以创建和使用密钥保管库。 尽管 Key Vault 能够为开发人员和安全管理员提供便利，但管理组织的其他 Azure Stack 服务的操作员也可以实现和管理 Key Vault。 例如，Azure Stack 操作员可以使用 Azure Stack 订阅登录，并为组织创建用于存储密钥的保管库。 完成此操作后，他们可以：

* 创建或导入密钥或机密。
* 撤销或删除密钥或机密。
* 授权用户或应用访问密钥保管库，以便他们随后可以管理或使用其密钥和机密。
* 配置密钥用法（例如，签名或加密）。

然后，操作员可以向开发人员提供统一资源标识符 (URI)，以便从其应用调用。 操作员还可以向安全管理员提供密钥使用情况日志记录信息。

开发人员还可使用 API 直接管理密钥。 有关详细信息，请参阅 [Key Vault 开发人员指南](/azure/key-vault/key-vault-developers-guide)。

## <a name="scenarios"></a>方案

以下方案说明 Key Vault 如何帮助满足开发人员和安全管理员的需求。

### <a name="developer-for-an-azure-stack-app"></a>Azure Stack 应用开发人员

**问题：** 我想要编写使用密钥进行签名和加密的 Azure Stack 应用。 我希望这些密钥在应用外部，以便解决方案适用于地理位置分散的应用。

**说明：** 密钥会存储在保管库中，根据需要由 URI 调用。

### <a name="developer-for-software-as-a-service-saas"></a>软件即服务 (SaaS) 开发人员

**问题：** 对于客户的密钥和机密，我不想承担任何实际或潜在的法律责任。 我希望客户拥有并管理其密钥，这样我就可以集中精力做我最擅长的事情，即提供核心软件功能。

**说明：** 客户可以在 Azure Stack 中导入和管理自己的密钥。

### <a name="chief-security-officer-cso"></a>首席安全官 (CSO)

**问题：** 我想要确保我的组织掌控密钥生命周期，并可监视密钥的使用。

**说明：** Key Vault 设计用于确保 Microsoft 不会看到或提取你的密钥。 当应用需要使用客户密钥来执行加密操作时，Key Vault 会代表应用来使用密钥。 应用不会看到客户密钥。 即使我们使用多个 Azure Stack 服务和资源，你可以从 Azure Stack 中的单一位置管理密钥。 不论你在 Azure Stack 中有几个保管库、保管库支持哪些区域以及使用哪些应用使用这些保管库，保管库都可提供单一界面。

## <a name="next-steps"></a>后续步骤

* [通过门户管理 Azure Stack 中的 Key Vault](azure-stack-key-vault-manage-portal.md)  
* [使用 PowerShell 管理 Azure Stack 中的 Key Vault](azure-stack-key-vault-manage-powershell.md)
