---
title: Azure Stack 中心安全控制
titleSuffix: Azure Stack Hub
description: 了解 Azure Stack 中心应用的安全状况和控制。
author: JustinHall
ms.topic: article
ms.date: 06/10/2019
ms.author: justinha
ms.reviewer: fiseraci
ms.lastreviewed: 1/16/2019
ms.openlocfilehash: 63d50749d3877209dd81ca7a59a38e634b2d46e7
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77697546"
---
# <a name="azure-stack-hub-infrastructure-security-controls"></a>Azure Stack 中心基础结构安全控制

安全考虑事项和合规性要求是使用混合云的主要驱动因素之一。 Azure Stack 中心适用于这些方案。 本文介绍 Azure Stack 中心的安全控制。

Azure Stack 中心共存了两个安全措施层。 第一层是 Azure Stack 中心基础结构，其中包括 Azure 资源管理器的硬件组件。 第一层包括管理员和用户门户。 第二层包含由租户创建、部署和管理的工作负荷。 第二层包含虚拟机和应用服务网站等项。

## <a name="security-approach"></a>安全方法

Azure Stack 中心的安全状况旨在抵御新式威胁，并构建为满足主要符合性标准的要求。 因此，Azure Stack 中心基础结构的安全状况建立在两个支柱之上：

- **假定违规**  
    从假设系统已被破坏的前提下，重点*检测并限制泄露的影响*，而不是仅试图阻止攻击。

- **默认情况下已强制**  
    由于基础结构在定义完善的硬件和软件上运行，Azure Stack 集线器默认*启用、配置和验证所有安全功能*。

由于 Azure Stack 集线器作为集成系统提供，因此 Azure Stack 中心基础结构的安全状况由 Microsoft 定义。 就像在 Azure 中一样，租户负责定义其租户工作负荷的安全状况。 本文档提供了有关 Azure Stack 中心基础结构的安全状况的基础知识。

## <a name="data-at-rest-encryption"></a>静态数据加密

所有 Azure Stack 中心基础结构和租户数据都使用 BitLocker 进行静态加密。 此加密可防止 Azure Stack 中心存储组件的物理丢失或被盗。 有关详细信息，请参阅[Azure Stack 集线器中的静态数据加密](azure-stack-security-bitlocker.md)。

## <a name="data-in-transit-encryption"></a>传输加密中的数据

Azure Stack 集线器基础结构组件通过使用 TLS 1.2 加密的通道进行通信。 加密证书由基础结构自行管理。

所有外部基础结构终结点（如 REST 终结点或 Azure Stack 中心门户）都支持 TLS 1.2 以实现安全通信。 必须为这些终结点提供加密证书（来自第三方或企业证书颁发机构）。

虽然自签名证书可用于这些外部终结点，但 Microsoft 强烈建议不要使用它。
若要详细了解如何在 Azure Stack 集线器的外部端点上强制执行 TLS 1.2，请参阅[配置 Azure Stack 中心安全控制](azure-stack-security-configuration.md)。

## <a name="secret-management"></a>机密管理

Azure Stack 集线器基础结构使用多种机密（如密码）来发挥作用。 其中的大多数都是自动轮换的，因为它们是组托管服务帐户（gMSA），后者每24小时进行一次轮换。

不 gMSA 的剩余机密可以使用特权终结点中的脚本手动轮换。

Azure Stack 集线器基础结构对其所有内部证书使用4096位 RSA 密钥。 相同的密钥长度证书还可以用于外部终结点。 有关机密和证书轮换的详细信息，请参阅[在 Azure Stack 中心旋转机密](azure-stack-rotate-secrets.md)。

## <a name="windows-defender-application-control"></a>Windows Defender 应用程序控制

Azure Stack 中心利用最新的 Windows Server 安全功能。 其中一种是 Windows Defender 应用程序控制（WDAC，以前称为代码完整性），它提供可执行文件允许列表并确保只有授权代码在 Azure Stack 中心基础结构中运行。

授权代码由 Microsoft 或 OEM 合作伙伴进行签名。 已签名的授权代码包含在 Microsoft 定义的策略中指定的允许软件列表中。 换句话说，只能执行已获批准在 Azure Stack 中心基础结构中运行的软件。 任何执行未经授权代码的尝试都会被阻止，并且会生成警报。 Azure Stack 中心强制实施用户模式代码完整性（UMCI）和虚拟机监控程序代码完整性（要求 HVCI）。

WDAC 策略还阻止第三方代理或软件在 Azure Stack 中心基础结构中运行。
有关 WDAC 的详细信息，请参阅[Windows Defender 应用程序控制和基于虚拟化的代码完整性保护](https://docs.microsoft.com/windows/security/threat-protection/device-guard/introduction-to-device-guard-virtualization-based-security-and-windows-defender-application-control)。

## <a name="credential-guard"></a>Credential Guard

Azure Stack 中心中的另一项 Windows Server 安全功能是 Windows Defender Credential Guard，用于保护来自哈希传递和传递票证攻击的 Azure Stack 中心基础结构凭据。

## <a name="antimalware"></a>反恶意软件

Azure Stack 集线器中的每个组件（Hyper-v 主机和虚拟机）都受到 Windows Defender 防病毒保护。

在连接的方案中，将每天多次应用防病毒定义和引擎更新。 在断开连接的情况下，将作为每月 Azure Stack 中心更新的一部分应用反恶意软件更新。 如果在断开连接的情况下需要更频繁地更新 Windows Defender 的定义，Azure Stack 中心还支持导入 Windows Defender 更新。 有关详细信息，请参阅[更新 Azure Stack 集线器上的 Windows Defender 防病毒](azure-stack-security-av.md)。

## <a name="secure-boot"></a>安全启动

Azure Stack 中心在所有 Hyper-v 主机和基础结构虚拟机上强制实施安全启动。 

## <a name="constrained-administration-model"></a>约束的管理模型

Azure Stack 中心中的管理由三个入口点控制，每个入口点都有特定用途：

- [管理员门户](azure-stack-manage-portals.md)为日常管理操作提供点击体验。
- Azure 资源管理器通过 PowerShell 和 Azure CLI 使用的 REST API 公开管理员门户的所有管理操作。
- 对于特定的低级别操作（例如，数据中心集成或支持方案），Azure Stack 集线器公开了称为[特权终结点](azure-stack-privileged-endpoint.md)的 PowerShell 终结点。 此终结点只公开一组列表中的一组 cmdlet，并进行大量审核。

## <a name="network-controls"></a>网络控制

Azure Stack 集线器基础结构附带了多层网络访问控制列表（ACL）。 Acl 可防止对基础结构组件进行未经授权的访问，并将基础结构通信限制为仅适用于其正常运行所需的路径。

网络 Acl 在三个层中强制执行：

- 第1层：架顶交换机
- 第2层：软件定义的网络
- 第3层：主机和 VM 操作系统防火墙

## <a name="regulatory-compliance"></a>法规符合性

Azure Stack 中心已通过与第三方无关的审核公司进行了正式的功能评估。 因此，有关 Azure Stack 中心基础结构如何满足来自多种主要符合性标准的适用控件的文档。 该文档并不是 Azure Stack 集线器的认证，因为这些标准包括多个与人员相关的和与流程相关的控件。 相反，客户可以使用此文档快速开始认证过程。

评估包括以下标准：

- [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/)寻址支付卡行业。
- [CSA 云控制矩阵](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview)是跨多个标准的综合性映射，包括 FEDRAMP、ISO27001、HIPAA、HITRUST、ITAR、NIST SP800-53 等。
- 政府客户[FedRAMP 高](https://www.fedramp.gov/fedramp-releases-high-baseline/)。

可在[Microsoft 服务信任门户](https://servicetrust.microsoft.com/ViewPage/AzureStack)中找到符合性文档。 合规性指南是受保护的资源，要求你使用 Azure 云服务凭据登录。

## <a name="next-steps"></a>后续步骤

- [配置 Azure Stack 中心安全控制](azure-stack-security-configuration.md)
- [了解如何在 Azure Stack 中心旋转机密](azure-stack-rotate-secrets.md)
- [适用于 Azure Stack 集线器的 PCI DSS 和 CSA-CCM 文档](https://servicetrust.microsoft.com/ViewPage/TrustDocuments)
- [Azure Stack 中心的 DoD 和 NIST 文档](https://servicetrust.microsoft.com/ViewPage/Blueprint)
