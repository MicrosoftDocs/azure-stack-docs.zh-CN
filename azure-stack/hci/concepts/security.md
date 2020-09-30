---
title: Azure Stack HCI 安全注意事项
description: 本主题提供有关 Azure Stack HCI 操作系统安全注意事项的指导。
author: JohnCobb1
ms.author: v-johcob
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: 181eb53d4b0e5c95065371e6b87e470a5e413d06
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572664"
---
# <a name="azure-stack-hci-security-considerations"></a>Azure Stack HCI 安全注意事项

>适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

本主题介绍与 Azure Stack HCI 操作系统有关的安全注意事项并提供相关建议：
- 第 1 部分介绍基本安全工具和技术，它们用于增强操作系统，保护数据和身份以有效地为你的组织构建安全基础。
- 第 2 部分介绍通过 Azure 安全中心提供的资源。
- 第 3 部分介绍更高级的安全注意事项，可帮助进一步优化你的组织在这些领域的安全状况。

## <a name="why-are-security-considerations-important"></a>为什么安全注意事项非常重要？
从上层管理人员到信息工作者，安全会影响组织中的每个人。 安全性不足对于组织来说是真正的风险，因为安全漏洞可能会破坏所有正常业务，并导致组织停摆。 越早检测到潜在攻击，就可以越快减轻安全方面的任何风险。

在出于利用环境弱点的目的研究了这些弱点之后，攻击者通常可以在初步减弱环境安全防控后的 24 到 48 小时内提升权限，控制网络中的系统。 良好的安全措施可以增强环境中系统的防御能力，通过阻挡攻击者的行动，可将攻击者尝试控制系统所耗用的时间从数小时延长至数周甚至数月。 实施本主题中介绍的安全建议使你的组织能够尽快检测到和应对这类攻击。

## <a name="part-1-build-a-secure-foundation"></a>第 1 部分：构建安全基础
以下各节推荐可用于为环境中运行 Azure Stack HCI 操作系统的服务器构建安全基础的安全工具和技术。

### <a name="harden-the-environment"></a>强化环境
本部分讨论如何保护在操作系统上运行的服务和虚拟机 (VM)：
- **Azure Stack HCI 认证的硬件**提供一致的安全启动、UEFI 和现成的 TPM 设置。 将基于虚拟化的安全性和经过认证的硬件结合起来，可帮助保护对安全性敏感的工作负载。 还可以将此受信任的基础结构连接到 Azure 安全中心，激活行为分析和报告，以应对快速变化的工作负载和威胁。

    - 安全启动是电脑行业开发的安全标准，旨在确保设备仅使用原始设备制造商 (OEM) 信任的软件进行启动。 有关详细信息，请参阅[安全启动](/windows-hardware/design/device-experiences/oem-secure-boot)。
    - “统一可扩展固件接口 (UEFI)”可控制服务器的启动过程，然后将控制权传递给 Windows 或其他操作系统。 有关详细信息，请参阅 [UEFI 固件要求](/windows-hardware/design/device-experiences/oem-uefi)。
    - 受信任的平台模块 (TPM) 技术提供基于硬件的安全性相关功能。 TPM 芯片是一种安全的加密处理器，用于生成、存储加密密钥和限制密钥的使用。 有关详细信息，请参阅[受信任的平台模块技术概述](/windows/security/information-protection/tpm/trusted-platform-module-overview)。

    若要详细了解 Azure Stack HCI 认证的硬件提供程序，请参阅 [AZURE STACK HCI 解决方案](https://azure.microsoft.com/products/azure-stack/hci/) 网站。

- **Device Guard** 和 **Credential Guard**。 Device Guard 可以防止不具有已知签名的恶意软件、未签名的代码以及可以访问内核的恶意软件捕获敏感信息或损坏系统。 Windows Defender 凭据保护使用基于虚拟化的安全性来隔离密钥，以便只有特权系统软件可以访问它们。

    有关详细信息，请参阅[管理 Windows Defender Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard-manage) 并下载 [Device Guard 和 Credential Guard 硬件就绪工具](https://www.microsoft.com/download/details.aspx?id=53337)。

- **Windows** 和**固件**更新在群集、服务器（包括来宾 VM）和电脑上非常重要，可帮助确保操作系统和系统硬件免受攻击者的影响。 可以使用 Windows Admin Center 的“更新”工具将更新应用到各个系统。 如果你的硬件提供商提供用于获取驱动程序、固件和解决方案更新的 Windows Admin Center 支持，你可以在 Windows 更新的同时获取这些更新，否则需要直接从供应商处获取这些更新。

    有关详细信息，请参阅[更新群集](../manage/update-cluster.md)。

    若要一次管理多个群集和服务器上的更新，请考虑订阅与 Windows Admin Center 集成的可选的 Azure 更新管理服务。 有关详细信息，请参阅[使用 Windows Admin Center 的 Azure 更新管理](https://www.thomasmaurer.ch/2018/11/azure-update-management-windows-admin-center)。

### <a name="protect-data"></a>保护数据
本部分讨论如何使用 Windows Admin Center 来保护操作系统上的数据和工作负载：

- **用于存储空间的 BitLocker** 可保护静态数据。 可以使用 BitLocker 为操作系统上存储空间数据卷的内容进行加密。 使用 BitLocker 保护数据有助于组织遵守政府、区域和特定于行业的标准，如 FIPS 140-2 和 HIPAA。
 
    若要了解有关在 Windows 管理中心中使用 BitLocker 的详细信息，请参阅 [启用卷加密、删除重复和压缩](../manage/volume-encryption-deduplication.md)

- Windows 网络的 **SMB** 加密可保护传输中的数据。 服务器消息块 (SMB) 是一种网络文件共享协议，该协议允许计算机上的应用程序读取和写入文件，以及通过计算机网络中的服务器程序请求服务。

    若要启用 SMB 加密，请参阅 [SMB 安全性增强 ](/windows-server/storage/file-server/smb-security)。

- Windows Admin Center 中的 **Windows Defender 防病毒**功能可保护客户端和服务器上的操作系统免受病毒、恶意软件、间谍软件和其他威胁的侵害。 有关详细信息，请参阅 [Windows Server 2016 和 2019 上的 Microsoft Defender 防病毒](/windows/security/threat-protection/microsoft-defender-antivirus/microsoft-defender-antivirus-on-windows-server-2016)。

### <a name="protect-identities"></a>保护标识
本部分讨论如何使用 Windows Admin Center 来保护特权标识：

- **访问控制**可以提高环境管理的安全性。 如果使用的是 Windows Admin Center 服务器（相对于 Windows 10 电脑上运行的服务器），则可以控制对 Windows Admin Center 本身的两个级别的访问：网关用户和网关管理员。 网关管理员标识提供程序选项包括：
    - 用于强制执行智能卡身份验证的 Active Directory 或本地计算机组。
    - 用于强制执行条件访问和多重身份验证的 Azure Active Directory。
 
    有关详细信息，请参阅[用户的 Windows 管理中心访问选项](/windows-server/manage/windows-admin-center/plan/user-access-options)和[配置用户访问控制和权限](/windows-server/manage/windows-admin-center/configure/user-access-control)。

- 流往 Windows Admin Center 的**浏览器流量**使用 HTTPS。 从 Windows Admin Center 流往托管服务器的流量通过“Windows 远程管理 (WinRM)”使用标准 PowerShell 和 Windows Management Instrumentation (WMI)。 Windows Admin Center 支持本地管理员密码解决方案 (LAPS)、基于资源的约束委派、使用 Active Directory (AD) 或 Microsoft Azure Active Directory (Azure AD) 的网关访问控制，以及用于管理目标服务器的基于角色的访问控制 (RBAC)。

    Windows Admin Center 支持 Microsoft Edge （Windows 10，版本 1709 或更高版本）、Google Chrome 和 Windows 10 上的 Microsoft Edge。 可以在 Windows 10 电脑或 Windows 服务器上安装 Windows Admin Center。

    如果在服务器上安装 Windows Admin Center，则它将作为网关运行，且在主机服务器上没有 UI。 在这种情况下，管理员可以通过 HTTPS 会话登录到服务器，该会话由主机上的自签名安全证书提供保护。 但是，更好的做法是使用来自受信任的证书颁发机构的适当 SSL 证书进行登录，因为受支持的浏览器会将自签名连接视为不安全，即使通过受信任的 VPN 连接到本地 IP 地址也是如此。

    若要了解有关组织的安装选项的更多信息，请参阅[哪种类型的安装适合你？](/windows-server/manage/windows-admin-center/plan/installation-options)。

- **CredSSP** 是一种身份验证提供程序，在少数情况下，Windows Admin Center 使用该身份验证提供程序将凭据传递给你要管理的特定服务器之外的计算机。 Windows Admin Center 当前需要 CredSSP 执行以下操作：
    - 创建新群集。
    - 访问“更新”工具以使用“故障转移群集”或“群集感知更新”功能。
    - 管理 VM 中的非聚合 SMB 存储。

    有关详细地信息，请参阅 [Windows Admin Center 是否使用 CredSSP？](/windows-server/manage/windows-admin-center/understand/faq#does-windows-admin-center-use-credssp)

- Windows Admin Center 中的**基于角色的访问控制 (RBAC)** 允许用户以有限的权限访问需要管理的服务器，而不是使其完全成为本地管理员。 若要在 Windows Admin Center 中使用 RBAC，请为每个托管服务器配置一个 PowerShell Just Enough Administration 终结点。

    有关详细信息，请参阅[基于角色的访问控制](/windows-server/manage/windows-admin-center/plan/user-access-options#role-based-access-control)和 [Just Enough Administration](/powershell/scripting/learn/remoting/jea/overview)。

- Windows Admin Center 中可用于管理和保护标识的**安全工具**包括 Active Directory、证书、防火墙、本地用户和组等。

    有关详细信息，请参阅[使用 Windows Admin Center 管理服务器](/windows-server/manage/windows-admin-center/use/manage-servers)。

## <a name="part-2-use-azure-security-center"></a>第 2 部分：使用 Azure 安全中心
*Azure 安全中心*是一个统一的基础结构安全管理系统，可增强数据中心的安全态势，并跨云和本地中的混合工作负载提供高级威胁防护。 安全中心为你提供了一些工具，可用于评估你的网络的安全状态、保护工作负载、发出安全警报，并遵循特定建议来缓解攻击影响并解决未来的威胁。 通过利用 Azure 服务实现自动配置和提供保护，安全中心可以在云中高速执行所有这些服务，且没有部署开销。

安全中心通过在这些资源上安装 Log Analytics 代理来保护 Windows 服务器和 Linux 服务器的 VM。 Azure 将代理收集的事件与用于确保工作负载安全而执行的建议（强化任务）进行关联。 基于安全最佳做法的强化任务包括管理和强制实施安全策略。 然后，你可以通过安全中心的监视功能来跟踪结果，并随时间推移管理合规性和实施治理，同时减少所有资源的受攻击面。

管理用户对 Azure 资源和订阅的访问是 Azure 治理策略的重要组成部分。 Azure 基于角色的访问控制 (RBAC) 是在 Azure 中管理访问权限的主要方法。 有关详细信息，请参阅[使用基于角色的访问控制管理对 Azure 环境的访问](/azure/cloud-adoption-framework/ready/azure-setup-guide/manage-access)。

通过 Windows Admin Center 使用安全中心需要 Azure 订阅。 若要开始，请参阅[将 Azure 安全中心与 Windows 管理中心集成](/azure/security-center/windows-admin-center-integration)。

注册后，在 Windows Admin Center 中访问安全中心：在“所有连接”页上，选择服务器或 VM，在“工具”下，选择“Azure 安全中心”，然后选择“登录到 Azure”   。

有关详细信息，请参阅[什么是 Azure 安全中心？](/azure/security-center/security-center-intro)

## <a name="part-3-add-advanced-security"></a>第 3 部分：添加高级安全
以下各节推荐高级安全工具和技术，可帮助进一步强化在你的环境中运行 Azure Stack HCI 操作系统的服务器。

### <a name="harden-the-environment"></a>强化环境
- **Microsoft 安全基线** 基于 microsoft 提供的安全建议，通过与商业组织和美国政府（如防御部门）的合作关系获得。 安全基线包括推荐的用于 Windows 防火墙、Windows Defender 的安全设置，还有很多其他内容。

    安全基线作为组策略对象 (GPO) 备份提供，可以将其导入 Active Directory 域服务 (AD DS)，然后部署到已加入域的服务器以增强环境防御能力。 还可以使用本地脚本工具配置具有安全基线的独立（未加入域的）服务器。 若要开始使用安全基线，请下载 [Microsoft 安全合规性工具包 1.0](https://www.microsoft.com/download/details.aspx?id=55319)。

    有关详细信息，请参阅 [Microsoft 安全基线](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines)。

### <a name="protect-data"></a>保护数据
- **强化 Hyper-V 环境**要求对 VM 上运行的 Windows Server 进行强化，这和强化物理服务器上运行的操作系统的方式一样。 由于虚拟环境通常具有共享同一物理主机的多个 VM，因此必须同时保护物理主机和在其上运行的 VM。 导致主机受影响的攻击者可以影响多个 VM，对工作负载和服务的影响更大。 本节讨论可用于在 Hyper-V 环境中强化 Windows Server 的以下方法：

    - **受保护的结构和受防护的 VM** 通过阻止攻击者修改 VM 文件来增强 Hyper-V 环境中运行的 VM 的安全性。 *受保护的结构*由一个主机保护服务 (HGS)（通常是三个节点的群集）、一个或多个受保护的主机以及一组受防护的 VM 组成。 “证明服务”评估主机请求的有效性，而“密钥保护服务”确定是否释放可供受保护主机用于启动受防护的 VM 的密钥。

        有关详细信息，请参阅[受保护的结构和受防护的 VM 概述](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms)。
     
     - Windows Server 中的**虚拟受信任的平台模块 (vTPM)** 支持 VM 的 TPM，它使你可以使用高级安全技术，例如 VM 中的 BitLocker。 你可以使用 Hyper-V 管理器或 `Enable-VMTPM` Windows PowerShell cmdlet 在任何第 2 代 Hyper-V VM 上启用 TPM 支持。
     
        有关详细信息，请参阅 [Enable-VMTPM](/powershell/module/hyper-v/enable-vmtpm)。
     
     - Azure Stack HCI 和 Windows Server 中的**软件定义网络 (SDN)** 集中配置和管理物理和虚拟网络设备，例如数据中心中的路由器、交换机和网关。 虚拟网络元素（例如 Hyper-V 虚拟交换机、Hyper-V 网络虚拟化和 RAS 网关）的作用是充当 SDN 基础结构的构成部分。

        有关详细信息，请参阅[软件定义的网络 (SDN)](/windows-server/networking/sdn/)。

### <a name="protect-identities"></a>保护标识
- **本地管理员密码解决方案 (LAPS)** 是一种轻型机制，适用于 Active Directory 加入域的系统，会定期将每台计算机的本地管理员帐户密码设置为新的随机值和唯一值。 密码存储在 Active Directory 中的相应计算机对象上的安全机密属性中，只有专门的授权用户才能检索到它们。 LAPS 使用本地帐户进行远程计算机管理，其应用方式与使用域帐户相比具有一些优势。 有关详细信息，请参阅[远程使用本地帐户：LAPS 改变一切](/archive/blogs/secguide/remote-use-of-local-accounts-laps-changes-everything)。

    若要开始使用 LAPS，请下载[本地管理员密码解决方案 (LAPS)](https://www.microsoft.com/download/details.aspx?id=46899)。

- **Microsoft 高级威胁分析 (ATA)** 是一种本地产品，可用于帮助检测尝试破坏特权标识的攻击者。 ATA 会出于一些目的而分析网络流量，如进行身份验证、授权及遵守信息收集协议，例如 Kerberos 和 DNS。 ATA 使用这些数据来构建网络上用户和其他实体的行为配置文件，以检测异常和已知攻击模式。
    
    有关详细信息，请参阅[什么是高级威胁分析？](/advanced-threat-analytics/what-is-ata)。

- **Windows Defender 远程 Credential Guard** 通过远程桌面连接来保护凭据，方法是将 Kerberos 请求重定向回发出连接请求的设备。 它还为远程桌面会话提供单一登录 (SSO)。 在远程桌面会话期间，如果目标设备遭到入侵，凭据不会暴露，因为凭据和凭据衍生内容永远不会通过网络传递到目标设备。

    有关详细信息，请参阅[管理 Windows Defender Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard-manage)。

## <a name="next-steps"></a>后续步骤
有关安全性和法规符合性的详细信息，另请参阅：
- [安全和保障](/windows-server/security/security-and-assurance)
- [Azure 解决方案的安全性最佳做法](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/)
