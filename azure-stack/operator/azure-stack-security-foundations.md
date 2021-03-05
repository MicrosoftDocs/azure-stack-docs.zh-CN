---
title: Azure Stack Hub 安全控制
titleSuffix: Azure Stack Hub
description: 了解应用于 Azure Stack Hub 的安全态势和控制。
author: BryanLa
ms.topic: article
ms.date: 06/10/2019
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 04/07/2020
ms.openlocfilehash: 7706b453ba9666664e6269a2ba90f4fd0f6e3ab4
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231160"
---
# <a name="azure-stack-hub-infrastructure-security-controls"></a>Azure Stack Hub 基础结构安全控制

安全考虑因素与合规性是使用混合云的主要推动因素。 Azure Stack Hub 是针对这些方案设计的。 本文介绍了针对 Azure Stack Hub 实施的安全控制措施。

两个安全态势层在 Azure Stack Hub 中共存。 第一层是 Azure Stack Hub 基础结构，其中包括 Azure 资源管理器之前的硬件组件。 第一层包括管理员门户和用户门户。 第二层由租户创建、部署和管理的工作负荷组成。 第二层包括虚拟机和应用程序服务网站等项。

## <a name="security-approach"></a>安全方法

Azure Stack Hub 的安全态势功能旨在防范新式威胁，为符合主要合规标准的要求而构建。 因此，Azure Stack Hub 基础结构的安全态势构建在两个支柱之上：

- **假想入侵**  
    我们从假设系统已被入侵的情况出发，将重点放在检测入侵并限制其影响上，而不只是尽量防止攻击。 

- **默认强化**  
    由于基础结构在妥善定义的硬件和软件中运行，因此 Azure Stack Hub 会在默认情况下启用、配置和验证所有安全功能。 

由于 Azure Stack 集线器作为集成系统提供，因此 Azure Stack 中心基础结构的安全状况由 Microsoft 定义。 如同在 Azure 中一样，租户需负责定义其租户工作负荷的安全局势。 本文档提供有关 Azure Stack Hub 基础结构安全态势的基础知识。

## <a name="data-at-rest-encryption"></a>静态数据加密

所有 Azure Stack Hub 基础结构和租户数据都使用 BitLocker 进行静态加密。 这种加密可以防范 Azure Stack Hub 存储组件的实物遗失或失窃。 有关详细信息，请参阅 [Azure Stack Hub 中的静态数据加密](azure-stack-security-bitlocker.md)。

## <a name="data-in-transit-encryption"></a>传输中数据加密

Azure Stack Hub 基础结构组件使用以 TLS 1.2 加密的通道进行通信。 加密证书由基础结构自行管理。

所有外部基础结构终结点（例如 REST 终结点或 Azure Stack Hub 门户）都支持使用 TLS 1.2 进行安全通信。 对于这些终结点，必须提供来自第三方或企业证书颁发机构的加密证书。

尽管可对这些外部终结点使用自签名证书，但 Microsoft 强烈建议不要使用此类证书。
有关如何在 Azure Stack Hub 的外部终结点上强制实施 TLS 1.2 的详细信息，请参阅[配置 Azure Stack Hub 安全控制](azure-stack-security-configuration.md)。

## <a name="secret-management"></a>机密管理

Azure Stack Hub 基础结构使用许多机密（例如密码和证书）来运行。 大多数与内部服务帐户关联的密码每 24 小时自动轮换一次，因为这些帐户是[组托管服务帐户 (gMSA)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)（一种由内部域控制器直接托管的域帐户）。

Azure Stack Hub 基础结构对其所有内部证书使用 4096 位 RSA 密钥。 相同密钥长度的证书还可用于外部终结点。 有关机密和证书轮换的详细信息，请参阅[在 Azure Stack Hub 中轮换机密](azure-stack-rotate-secrets.md)。

## <a name="windows-defender-application-control"></a>Microsoft Defender 应用程序控制

Azure Stack Hub 利用最新的 Windows Server 安全功能。 其中的一项功能是 Windows Defender 应用程序控制（WDAC，前称为“代码完整性”），它提供可执行文件筛选功能，确保只有已授权的代码可在 Azure Stack Hub 基础结构中运行。

经授权的代码是由 Microsoft 或 OEM 合作伙伴签名的。 已签名的经授权代码包括在由 Microsoft 定义的策略中指定的允许的软件列表中。 换而言之，只能执行已批准在 Azure Stack Hub 基础结构中运行的软件。 任何执行未经授权代码的尝试都会被阻止，并且会生成警报。 Azure Stack Hub 强制实施用户模式代码完整性 (UMCI) 和虚拟机监控程序代码完整性 (HVCI)。

WDAC 策略也会阻止第三方代理或软件在 Azure Stack Hub 基础结构中运行。
有关 WDAC 的详细信息，请参阅 [Windows Defender 应用程序控制和基于虚拟化的代码完整性保护](/windows/security/threat-protection/device-guard/introduction-to-device-guard-virtualization-based-security-and-windows-defender-application-control)。

## <a name="credential-guard"></a>Credential Guard

Azure Stack Hub 中的另一项 Windows Server 安全功能是 Windows Defender Credential Guard，它可用于防止 Azure Stack Hub 基础结构凭据遭到“传递哈希”和“传递票证”攻击。

## <a name="antimalware"></a>反恶意软件

Azure Stack Hub 中的每个组件（Hyper-V 主机和虚拟机）受到 Windows Defender Antivirus 的保护。

在联网场景中，防病毒定义和引擎更新每天应用多次。 在离线场景中，反恶意软件更新作为 Azure Stack Hub 的每月更新的一部分应用。 在离线场景中，如果需要更频繁地更新 Windows Defender 的定义，Azure Stack Hub 还支持导入 Windows Defender 更新。 有关详细信息，请参阅[更新 Azure Stack Hub 上的 Windows Defender Antivirus](azure-stack-security-av.md)。

## <a name="secure-boot"></a>安全启动

Azure Stack Hub 在所有 Hyper-V 主机和基础结构虚拟机上强制实施安全启动。 

## <a name="constrained-administration-model"></a>受约束的管理模型

Azure Stack Hub 中的管理通过三个入口点进行控制，每个入口点都有特定的用途：

- [管理员门户](azure-stack-manage-portals.md)针对日常管理操作提供点击式体验。
- Azure 资源管理器通过 PowerShell 和 Azure CLI 使用的 REST API 公开管理员门户的所有管理操作。
- 对于特定的低级操作（例如数据中心集成或支持方案），Azure Stack Hub 公开一个称作[特权终结点](azure-stack-privileged-endpoint.md)的 PowerShell 终结点。 此终结点只公开一组允许的 cmdlet，并且经常接受审核。

## <a name="network-controls"></a>网络控制措施

Azure Stack Hub 基础结构附带了多个网络访问控制列表 (ACL) 层。 ACL 可防止用户对基础结构组件进行未经授权的访问，并将基础结构通信限制为基础结构在运行时需要访问的路径。

在三个层中实施网络 ACL：

- 第 1 层：机架顶部交换机
- 第 2 层：软件定义的网络
- 第 3 层：主机和 VM 操作系统防火墙

## <a name="regulatory-compliance"></a>法规符合性

Azure Stack Hub 已通过了由第三方独立审核公司执行的正式功能评估。 因此，我们提供了介绍 Azure Stack Hub 基础结构如何满足多个主要合规标准的适用控制措施的文档。 此文档不是 Azure Stack Hub 的认证，因为标准包括多个与人员相关的和多个与流程相关的控制措施。 但是，客户可以使用此文档来启动其认证流程。

评估包括以下标准：

- [PCI-DSS](https://www.pcisecuritystandards.org/pci_security/) 适用于支付卡行业。
- [CSA Cloud Control Matrix](https://cloudsecurityalliance.org/group/cloud-controls-matrix/#_overview) 是跨多个标准的综合性映射，这些标准包括 FedRAMP Moderate、ISO27001、HIPAA、HITRUST、ITAR、NIST SP800-53 和其他标准。
- [FedRAMP High](https://www.fedramp.gov/fedramp-releases-high-baseline/) 适用于政府客户。

可以在 [Microsoft 服务信任门户](https://aka.ms/azurestackcompliance)上找到符合性文档。 符合性指南是受保护的资源并且要求你使用 Azure 云服务凭据进行登录。

## <a name="next-steps"></a>后续步骤

- [配置 Azure Stack Hub 安全控制](azure-stack-security-configuration.md)
- [了解如何在 Azure Stack Hub 中轮换机密](azure-stack-rotate-secrets.md)
- [适用于 Azure Stack 集线器的 PCI DSS 和 CSA-CCM 文档](https://aka.ms/azurestackcompliance)
