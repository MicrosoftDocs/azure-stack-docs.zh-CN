---
title: 在 Azure Stack HCI 上部署可信企业虚拟化
description: 本主题提供有关如何在 Azure Stack HCI 操作系统上计划、配置和部署使用可信企业虚拟化的高安全基础结构的指导。
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/07/2021
ms.openlocfilehash: bd9675efc87929c020f3c1514d220fece54099a7
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254766"
---
# <a name="deploy-trusted-enterprise-virtualization-on-azure-stack-hci"></a>在 Azure Stack HCI 上部署可信企业虚拟化

>适用于：Azure Stack HCI，版本 20H2

本主题提供有关如何在 Azure Stack HCI 操作系统上计划、配置和部署使用可信企业虚拟化的高安全基础结构的指导。 利用 Azure Stack HCI 投资，通过 Windows 管理中心和 Azure 门户，在使用 [基于虚拟化的安全 (VBS) ](/windows-hardware/design/device-experiences/oem-vbs) 和混合云服务的硬件上运行安全工作负荷。

## <a name="overview"></a>概述
VBS 是 [AZURE STACK HCI 中安全投资](/windows-server/get-started-19/whats-new-19#security) 的关键组件，用于保护主机和虚拟机 (vm) 安全威胁。 例如， [安全技术实现指南 (STIG) ](https://nvd.nist.gov/ncp/checklist/914)，它作为一项工具发布，以改善国防 (DoD) 信息系统的安全，列出了 VBS 和 [受虚拟机监控程序保护的代码完整性 (要求 hvci) ](/windows-hardware/drivers/bringup/device-guard-and-credential-guard) 作为一般安全要求。 必须使用为 VBS 和要求 HVCI 启用的主机硬件来保护 Vm 上的工作负荷，因为受攻击的主机不能保证 VM 保护。

VBS 使用硬件虚拟化功能来创建安全区域，并将其与操作系统隔离开来。 你可以使用 Windows 中 [ (VSM) 的虚拟安全模式 ](/virtualization/hyper-v-on-windows/tlfs/vsm) 来托管多个安全解决方案，从而大大提高对操作系统漏洞和恶意攻击的保护。

VBS 使用 Windows 虚拟机监控程序在操作系统软件中创建和管理安全边界，强制实施限制以保护重要的系统资源，并保护安全资产，例如经过身份验证的用户凭据。 使用 VBS，即使恶意软件能够访问操作系统内核，你也可以极大地限制并包含可能的攻击，因为虚拟机监控程序阻止恶意软件执行代码或访问平台机密。

虚拟机监控程序（系统软件的最高特权级别）在所有系统内存中设置并强制实施页面权限。 在 VSM 中，页面只能在传递代码完整性检查后执行。 即使存在允许恶意软件尝试修改内存的漏洞（例如缓冲区溢出），也不能修改代码页，也无法执行修改后的内存。 VBS 和要求 HVCI 大大增强了代码完整性策略的实施。 所有内核模式驱动程序和二进制文件在开始之前都将进行检查，并阻止未签名的驱动程序或系统文件加载到系统内存中。

## <a name="deploy-trusted-enterprise-virtualization"></a>部署可信企业虚拟化
本部分详细介绍如何获取硬件，以便部署在 Azure Stack HCI 和 Windows 管理中心进行管理时使用受信任企业虚拟化的高安全基础结构。

### <a name="step-1-acquire-hardware-for-trusted-enterprise-virtualization-on-azure-stack-hci"></a>步骤1：在 Azure Stack HCI 上获取可信企业虚拟化的硬件
首先，需要购买硬件。 要执行此操作，最简单的方法是在 [AZURE STACK HCI 目录](https://hcicatalog.azurewebsites.net) 中查找首选的 Microsoft 硬件合作伙伴，并使用预装的 Azure Stack HCI 操作系统购买集成系统。 在目录中，可以进行筛选，以查看针对此类型的工作负荷进行了优化的供应商硬件。

否则，你将需要在你自己的硬件上部署 Azure Stack HCI 操作系统。 有关 Azure Stack HCI 部署选项和安装 Windows 管理中心的详细信息，请参阅 [部署 AZURE STACK HCI 操作系统](./operating-system.md)。

接下来，使用 Windows 管理中心 [创建 AZURE STACK HCI 群集](./create-cluster.md)。

适用于 Azure Stack HCI 的所有合作伙伴硬件都通过硬件保证附加资格进行认证。 限定过程将测试所有必需的 [VBS](/windows-hardware/design/device-experiences/oem-vbs) 功能。 但是，不会在 Azure Stack HCI 中自动启用 VBS 和要求 HVCI。 有关硬件保证附加限制的详细信息，请参阅 [Windows Server 目录](https://www.windowsservercatalog.com/content.aspx?ctf=AQinfo-systems.htm#:~:text=Hardware%20Assurance%20Windows%20Server%20systems%20that%20are%20awarded,of%20Windows%20Server%2C%20starting%20with%20Windows%20Server%202016)中的 **系统** 下的 "硬件保证"。

   >[!WARNING]
   > 要求 HVCI 可能与 Azure Stack HCI 目录中未列出的硬件设备不兼容。 强烈建议使用受信任企业虚拟化基础结构的合作伙伴提供的 Azure Stack HCI 验证硬件。

### <a name="step-2-enable-hvci"></a>步骤2：启用要求 HVCI
在服务器硬件和 Vm 上启用要求 HVCI。 有关详细信息，请参阅 [启用基于虚拟化的代码完整性保护](/windows/security/threat-protection/device-guard/enable-virtualization-based-protection-of-code-integrity)。

### <a name="step-3-set-up-azure-security-center-in-windows-admin-center"></a>步骤3：在 Windows 管理中心中设置 Azure 安全中心
在 Windows 管理中心，设置 [Azure 安全中心](/azure/security-center/security-center-introduction) 来添加威胁防护，并快速评估工作负荷的安全状况。

若要了解详细信息，请参阅 [通过安全中心保护 Windows 管理中心资源](/azure/security-center/windows-admin-center-integration)。

若要开始安全中心：
- 首先需要订阅 Microsoft Azure。 如果没有订阅，可以注册 [免费试用版](https://azure.microsoft.com/free)。
- 在 Azure 门户中访问 Azure 安全中心仪表板后，就会在所有当前的 Azure 订阅中启用安全中心的免费定价层，或通过 API 以编程方式启用。
若要利用高级安全管理和威胁检测功能，必须启用 Azure Defender。 可免费使用 Azure Defender 30 天。 有关详细信息，请参阅[安全中心定价](https://azure.microsoft.com/pricing/details/security-center)。
- 如果已准备好启用 Azure Defender，请参阅 [快速入门：设置 Azure 安全中心](/azure/security-center/security-center-get-started) 来完成这些步骤。

你还可以使用 Windows 管理中心来设置其他 Azure 混合服务，例如备份、文件同步、Site Recovery、点到站点 VPN、更新管理和 Azure Monitor。

## <a name="next-steps"></a>后续步骤
有关与可信企业虚拟化相关的详细信息，请参阅：
- [Windows Server 上的 Hyper-V](/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)