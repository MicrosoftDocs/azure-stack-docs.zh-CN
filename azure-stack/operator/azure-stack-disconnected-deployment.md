---
title: Azure Stack 集线器集成系统的 Azure 断开连接部署决策
description: 了解 Azure Stack 集线器集成系统的 Azure 断开连接部署，以及要考虑的规划决策。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 11/01/2019
ms.author: inhenkel
ms.reviewer: wfayed
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 4b2cdd9f663f95ddd1fc3b3a23f7fce06da2c2c9
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77699977"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-hub-integrated-systems"></a>Azure Stack 集线器集成系统的 Azure 断开连接部署规划决策
确定[将 Azure Stack 中心集成到混合云环境的方式](azure-stack-connection-models.md)后，可以完成 Azure Stack 中心部署决策。

无需连接到 internet 即可部署和使用 Azure Stack 中心。 但是，在断开连接的部署中，你只能使用 Active Directory 联合身份验证服务（AD FS）标识存储和基于容量的计费模型。 由于多租户需要使用 Azure Active Directory （Azure AD），因此不支持多租户进行断开连接的部署。

如果是以下情况，请选择此选项：
- 你具有安全或其他限制，要求你在未连接到 internet 的环境中部署 Azure Stack 中心。
- 要阻止将数据（包括使用情况数据）发送到 Azure。
- 你希望使用纯粹作为私有云解决方案的 Azure Stack 集线器，该解决方案部署到你的公司 intranet，且对混合方案不感兴趣。

> [!TIP]
> 有时，这种环境也称为*海底方案*。

断开连接的部署不会限制你稍后将 Azure Stack 集线器实例连接到 Azure 用于混合租户 VM 方案。 这意味着在部署期间未连接到 Azure，或者不希望使用 Azure AD 作为标识存储。

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>已断开连接的部署中受损或不可用的功能 
在连接到 Azure 时，Azure Stack 集线器的运行效果最好，因此请务必注意，在断开连接模式下，某些特性和功能已被削弱或完全不可用。

|Feature|断开连接模式的影响|
|-----|-----|
|用于配置 VM 后期部署的具有 DSC 扩展的 VM 部署|受损-DSC 扩展对 internet 查找最新的 WMF。|
|运行 Docker 扩展的 VM 部署|受损-Docker 将在 internet 上检查最新版本，并且此检查将失败。|
|Azure Stack 中心门户中的文档链接|不可用-"提供反馈"、"帮助" 和 "快速入门" 等链接不起作用。|
|引用联机更新指南的警报修正/缓解措施|不可用-任何使用 internet URL 的警报修正链接都不起作用。|
|Marketplace-直接从 Azure Marketplace 选择和添加库包的功能|受损-在断开连接模式下部署 Azure Stack 集线器时，无法使用 Azure Stack 中心门户下载 marketplace 项。 但是，可以使用[marketplace 联合工具](azure-stack-download-azure-marketplace-item.md)将 marketplace 项下载到具有 internet 连接的计算机，然后将其传输到 Azure Stack 中心环境。|
|使用 Azure AD 联合帐户管理 Azure Stack 中心部署|不可用-此功能要求连接到 Azure。 必须改用本地 Active Directory 实例 AD FS。|
|应用服务|受损-WebApps 可能需要对更新内容进行 internet 访问。|
|命令行接口 (CLI)|受损-CLI 已经降低了服务主体的身份验证和预配功能。|
|Visual Studio-Cloud discovery|削弱-Cloud Discovery 将发现不同的云或根本不起作用。|
|Visual Studio-AD FS|削弱的 Visual Studio Enterprise 和 Visual Studio Code 支持 AD FS 身份验证。
遥测|不可用-Azure Stack 集线器的遥测数据以及依赖于遥测数据的任何第三方库包的数据。|
|证书|不可用-HTTPS 上下文中的证书吊销列表（CRL）和联机证书状态协议（OSCP）服务需要 internet 连接。|
|Key Vault|受损-Key Vault 的一个常见用例是让应用程序在运行时读取机密。 对于此用例，应用需要目录中的服务主体。 在 Azure AD 中，默认情况下，允许普通用户（非管理员）添加服务主体。 Azure AD （使用 AD FS），则不是这样。 这种有障碍的是，在端到端体验方面有一个障碍，因为必须始终通过目录管理员来添加任何应用。

## <a name="learn-more"></a>了解详细信息
- 有关用例、购买、合作伙伴和 OEM 硬件供应商的信息，请参阅[Azure Stack 中心](https://azure.microsoft.com/overview/azure-stack/)产品页。
- 有关 Azure Stack 中心集成系统的路线图和地域可用性的信息，请参阅白皮书： [Azure Stack 中心： Azure 的扩展](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)。 
- 若要了解有关 Microsoft Azure Stack 中心打包和定价的详细信息，请[下载 .pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。 

## <a name="next-steps"></a>后续步骤
[数据中心网络集成](azure-stack-network.md)
