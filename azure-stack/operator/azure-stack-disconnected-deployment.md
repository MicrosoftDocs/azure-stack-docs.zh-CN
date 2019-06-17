---
title: Azure Stack 集成系统的 Azure 断开连接部署决策 | Microsoft Docs
description: 确定多节点 Azure Stack Azure 连接部署的部署计划决策。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: a3986bcdff911fb70957dcb7529a07f77b586c0a
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131513"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Azure Stack 集成系统的 Azure 断开连接部署计划决策
在决定[如何将 Azure Stack 集成到混合云环境](azure-stack-connection-models.md)后，可以完成 Azure Stack 部署决策。

无需连接到 Internet 即可部署和使用 Azure Stack。 但是，使用断开连接部署，你将受限于一个 AD FS 标识存储和基于容量的计费模型。 由于多租户需要使用 Azure Active Directory (Azure AD)，因此离线部署不支持多租户。 

适合选择此选项的情况如下所述：
- 如果存在要求你在未连接到 Internet 的环境中部署 Azure Stack 的安全性或其他限制。
- 如果希望阻止将数据（包括使用情况数据）发送到 Azure。
- 如果希望单纯将 Azure Stack 用作部署到公司 Intranet 的私有云解决方案，并且不考虑在混合方案中使用。

> [!TIP]
> 有时候，这种类型的环境也称为“潜艇方案”  。

离线部署不会限制你以后将 Azure Stack 实例连接到 Azure 以实现混合租户 VM 方案。 它只意味着在部署期间不连接到 Azure，或者不希望使用 Azure AD 作为标识存储。

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>在断开连接部署中被削弱或不可用的功能 
Azure Stack 设计为在连接到 Azure 的情况下功能最佳，因此请务必注意，在离线模式下，有些功能被损坏或完全不可用。 

|Feature|断开连接模式的影响|
|-----|-----|
|VM 部署（带有用于配置 VM 后期部署的 DSC 扩展）|被削弱 - DSC 扩展从 Internet 查找最新 WMF。|
|VM 部署（带有用于运行 Docker 命令的 Docker 扩展）|被削弱 - Docker 将检查 Internet 来查找最新版本并且此检查将失败。|
|Azure Stack 门户中的文档链接|不可用 - 诸如“提供反馈”、“帮助”、“快速入门”之类的使用 Internet URL 的链接将不起作用。|
|引用联机修正指南的警报修正/缓解|不可用 - 使用 Internet URL 的任何警报修正链接都不起作用。|
|市场 - 直接从 Azure 市场中选择并添加库包的能力|被损坏 - 在离线模式下（没有任何 Internet 连接）部署 Azure Stack 时，不能通过 Azure Stack 门户下载市场项。 但是，可以使用[市场联合工具](azure-stack-download-azure-marketplace-item.md)将市场项下载到有 Internet 连接的计算机，然后再将这些项转移到 Azure Stack 环境。|
|使用 Azure Active Directory 联合身份验证帐户管理 Azure Stack 部署|不可用 - 此功能要求连接到 Azure。 必须改用具有本地 Active Directory 实例的 AD FS。|
|应用服务|被损坏 - WebApps 可能需要访问 Internet 以获取更新的内容。|
|命令行接口 (CLI)|被削弱 - CLI 在对服务主体进行身份验证和预配方面的功能已减弱。|
|Visual Studio - Cloud discovery|被削弱 - Cloud Discovery 将发现不同的云或根本不工作。|
|Visual Studio - AD FS|被削弱 - 只有 Visual Studio Enterprise 支持 AD FS。
遥测|不可用 - Azure Stack 的遥测数据以及依赖于遥测数据的任何第三方库包。|
|证书|不可用 - 在 HTTPS 上下文中，证书吊销列表 (CRL) 和在线证书状态协议 (OSCP) 服务需要使用 Internet 连接。|
|Key-Vault|被削弱 - Key Vault 的一个常见用例是让应用程序在运行时读取机密。 因此，应用程序需要目录中的一个服务主体。 在 Azure Active Directory 中，默认情况下允许常规用户（非管理员）添加服务主体。 在 AD 中（使用 ADFS）不允许。 这妨碍了端对端体验，因为用户必须始终通过目录管理员来添加任何应用程序。| 

## <a name="learn-more"></a>了解详细信息
- 有关用例、购买、合作伙伴和 OEM 硬件供应商的信息，请参阅 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 产品页。
- 有关 Azure Stack 集成系统的路线图和上市区域的信息，请参阅白皮书：[Azure Stack：Azure 的扩展](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/)。 
- 若要了解有关 Microsoft Azure Stack 打包和定价的详细信息[下载此.pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)。 

## <a name="next-steps"></a>后续步骤
[数据中心网络集成](azure-stack-network.md)
