---
title: Azure Stack 中心中的 Key Vault 简介
description: 了解 Key Vault 如何管理 Azure Stack 中心的密钥和机密。
author: sethmanheim
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: sethm
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 09db1c2e8daa34b566512ca6daee2480a86527df
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704040"
---
# <a name="introduction-to-key-vault-in-azure-stack-hub"></a>Azure Stack 中心中的 Key Vault 简介

## <a name="prerequisites"></a>必备条件

* 订阅包含 Azure Key Vault 服务的产品/服务。  
* PowerShell 已安装并[配置为与 Azure Stack 中心一起使用](azure-stack-powershell-configure-user.md)。

## <a name="key-vault-basics"></a>Key Vault 基础知识

Azure Stack 中心 Key Vault 可帮助保护云应用和服务使用的加密密钥和机密。 通过使用 Key Vault，可以加密密钥和机密，例如：

* 身份验证密钥
* 存储帐户密钥
* 数据加密密钥
* .pfx 文件
* 密码

Key Vault 简化了密钥管理过程，可让我们控制用于访问和加密数据的密钥。 开发人员可以在几分钟内创建用于开发和测试的密钥，然后无缝地将其迁移到生产密钥。 安全管理员可以根据需要授予（和吊销）密钥权限。

拥有 Azure Stack 中心订阅的任何人都可以创建和使用密钥保管库。 尽管 Key Vault 使开发人员和安全管理员受益，但为组织管理其他 Azure Stack 中心服务的操作员可以实现和管理。 例如，Azure Stack 中心操作员可以使用 Azure Stack 中心订阅登录，并为要存储密钥的组织创建保管库。 完成此操作后，用户可以：

* 创建或导入密钥或机密。
* 吊销或删除密钥或机密。
* 授权用户或应用程序访问密钥保管库，以便他们能够管理或使用其密钥和机密。
* 配置密钥用法（例如，签名或加密）。

然后，操作员可以向开发人员提供统一资源标识符（Uri），以便从其应用程序调用。 操作员还可以为安全管理员提供密钥使用情况日志记录信息。

开发人员还可以通过使用 Api 直接管理密钥。 有关详细信息，请参阅[Key Vault 开发人员指南](/azure/key-vault/key-vault-developers-guide)。

## <a name="scenarios"></a>方案

以下方案介绍 Key Vault 如何帮助满足开发人员和安全管理员的需求。

### <a name="developer-for-an-azure-stack-hub-app"></a>Azure Stack 中心应用的开发人员

**问题：** 我要为使用密钥进行签名和加密的 Azure Stack 集线器编写应用。 我希望这些密钥在应用程序外部，以便解决方案适用于地理位置分散的应用程序。

**语句：** 密钥存储在保管库中，并在需要时由 URI 调用。

### <a name="developer-for-software-as-a-service-saas"></a>软件即服务 (SaaS) 开发人员

**问题：** 对于客户的密钥和机密，我不想承担责任或潜在责任。 我希望客户拥有并管理他们的密钥，使我能够集中精力实现我的最佳功能，这就是提供核心软件功能。

**语句：** 客户可在 Azure Stack 中心导入和管理他们自己的密钥。

### <a name="chief-security-officer-cso"></a>首席安全官（CSO）

**问题：** 我想要确保我的组织掌控关键生命周期，并可监视密钥的使用情况。

**语句：** Key Vault 旨在确保 Microsoft 不会看到或提取你的密钥。 当应用程序需要使用客户密钥执行加密操作时，Key Vault 会代表应用程序使用密钥。 应用不会看到客户密钥。 尽管我们使用多个 Azure Stack 中心服务和资源，但你可以从 Azure Stack 集线器中的单个位置管理密钥。 无论在 Azure Stack 集线器中有多少保管库、支持的区域以及哪些应用使用这些保管库，保管库都提供单个接口。

## <a name="next-steps"></a>后续步骤

* [通过门户管理 Azure Stack 中心的 Key Vault](azure-stack-key-vault-manage-portal.md)  
* [使用 PowerShell 管理 Azure Stack 中心的 Key Vault](azure-stack-key-vault-manage-powershell.md)
