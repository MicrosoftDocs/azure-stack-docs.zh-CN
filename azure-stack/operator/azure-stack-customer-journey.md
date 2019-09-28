---
title: Azure Stack 数据中心集成演练 | Microsoft Docs
description: 从规划到后期部署，了解如何成功地从数据中心部署 Azure Stack 的现场。
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
ms.reviewer: asganesh
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 57e92f877ab9516b7b4978b5a919b18dad9b60ea
ms.sourcegitcommit: c2ea4ffb42563c26faaf2993ba7b484bcb6d5cb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71342847"
---
# <a name="azure-stack-datacenter-integration-walkthrough"></a>Azure Stack datacenter integration 演练

本文介绍从采购集成的系统到解决方案提供商成功完成现场部署在内的端到端 Azure Stack 客户体验。 使用此信息来简化您的旅程，并帮助您为 Azure Stack 客户提供预期。

Azure Stack 客户应该为以下阶段做好准备：

|     |规划阶段|订单处理|部署前|工厂处理|硬件交付|现场部署|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|与合作伙伴洽谈以提供售前支持。|准备所需的软件许可与合同。|为客户提供所需的工具用于收集数据中心集成要求和文档。|每月提供最新的基线版本和工具链更新。|不可用|Microsoft 支持工程师提供任何部署问题的帮助。|
|**合作伙伴**|根据客户要求建议解决方案选项。<br><br>根据需要提议概念证明 (POC)。<br><br>建立业务关系。<br><br>确定支持级别。|准备好与客户签署的必要合同。<br><br>创建客户采购订单。<br><br>确定交付时间表。<br><br>必要时将客户连接到 Microsoft。|为客户提供必要的培训，确保客户了解所有部署先决条件和数据中心集成选项。<br><br>帮助客户验证所收集的数据，以确保完整性和准确性。|应用最后一个已验证的基线版本。<br><br>应用所需的 Microsoft 部署工具包。|将硬件寄送到客户所在地。|由现场工程师处理部署。<br><br>机架和堆栈。<br><br>硬件生命周期主机 (HLH) 部署。<br><br>Azure Stack 部署。<br><br>移交给客户。|
|**客户**|描述预期用例，并指定要求。|确定要使用、查看和批准合同的计费模型。|完成[部署工作表](azure-stack-deployment-worksheet.md)，确保满足所有部署先决条件，并且已准备好进行部署。|不可用|通过确保所有必需的电源和冷却、边界连接和其他所需的数据中心集成要求准备就绪，来准备数据中心。|可在部署期间提供订阅凭据，并在所提供的数据有疑问时提供支持。|
| | | | | | | |


## <a name="planning-phase"></a>规划阶段
在规划阶段，Microsoft 或 Azure Stack 解决方案合作伙伴将与你一起评估并了解你的需求，以便确定 Azure Stack 是否适合你的解决方案：

他们将帮助你确定以下问题：

-   Azure Stack 解决方案是否适合你的组织？

-   需要哪种规模的解决方案？

-   组织适合使用哪种计费模型和许可模型？

-   电源和散热要求是什么？

为了确保硬件解决方案能够满足您的需求， [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)用于帮助进行预购买计划，以确定 Azure Stack 硬件解决方案的适当容量和配置。

该电子表格并非旨在替代你自己对硬件解决方案适用度的调查与分析。 规划 Azure Stack 部署时，还应该查看适用于 Azure Stack 集成系统的[一般数据中心集成注意事项](azure-stack-datacenter-integration.md)。

## <a name="order-process-phase"></a>订单处理阶段
在此阶段，很多关于可行性方面的问题已有解答。 现在，你已准备好提交 Azure Stack，并在对所有必需的合同和采购订单进行签名后，将要求你向解决方案提供商提供集成要求数据。

## <a name="pre-deployment-phase"></a>部署前的阶段
在此阶段，需要确定要如何将 Azure Stack 集成到数据中心。 为了简化此过程，Microsoft 将要求模板结合在一起，以帮助您收集规划环境中的集成系统所需的信息。 此要求模板与解决方案提供商协作。

[一般数据中心集成注意事项](azure-stack-datacenter-integration.md)一文提供的信息可帮助你完成该模板（名为“部署工作表”）。

> [!IMPORTANT]
> 在此阶段，重要的是，在对解决方案进行排序之前，调查并决定所有先决条件信息。 请注意，此步骤相当耗时，需要与组织中的多个专业单位协调，并从中收集数据。 信息不正确或不完整可能导致部署时间延长。 

在部署前阶段，需要确定以下各项：

- **Azure Stack 连接模型和标识提供者**。 可以选择在[连接到 Internet（和 Azure）时或者离线时](azure-stack-connection-models.md)部署 Azure Stack。 若要从 Azure Stack（包括混合方案）获得最大效益，建议在连接到 Azure 时进行部署。 选择 Active Directory 联合身份验证服务 (AD FS) 还是 Azure Active Directory (Azure AD) 是在部署时必须进行的一次性决策。 **你无法在以后更改标识提供程序，而无需重新部署整个系统**。

- **许可模型**。 要从中进行选择的许可模式选项取决于你所拥有的部署类型。 标识提供者选项与租户虚拟机或其使用的标识系统和帐户无关。
    - 采用[离线部署](azure-stack-disconnected-deployment.md)的客户只有一个选项：基于容量的计费。

    - 采用[联网部署](azure-stack-connected-deployment.md)的客户可以选择基于容量的计费或即用即付。 基于容量的计费需要用于注册的企业协议（EA） Azure 订阅。 这对于注册是必需的，它通过 Azure 订阅提供 Azure Marketplace 中项目的可用性。

- **网络集成**。 [网络集成](azure-stack-network.md)对于部署、操作和管理 Azure Stack 系统至关重要。 需要考虑到多种因素才能确保 Azure Stack 解决方案具有复原能力，并提供一个高可用性物理基础设施来支持其操作。

- **防火墙集成**。 建议[使用防火墙](azure-stack-firewall.md)来帮助保护 Azure Stack。 防火墙有助于防止 DDOS 攻击，以及执行入侵检测和内容检查。 但应注意，它可能成为 Azure 存储服务的吞吐量瓶颈。


- **证书要求**。 在现场工程师到达数据中心进行部署*之前*，必须提供所有[必需的证书](azure-stack-pki-certs.md)。

通过部署工作表收集所有必要信息后，解决方案提供商将会根据收集的数据开始工厂处理，确保将 Azure Stack 成功集成到数据中心。

## <a name="hardware-delivery-phase"></a>硬件交付阶段
当解决方案抵达你的设施时，解决方案提供商将与你一起安排日程。 一旦接收并投入使用后，就需要使用解决方案提供商计划时间，让工程师在现场执行 Azure Stack 部署。

在*现场工程师到达部署解决方案之前*，所有先决条件数据都已锁定并可用，这一点非常**重要**。

-   必须购买并准备好所有证书。

-   必须确定区域名。

-   所有网络集成参数都已确认，且符合你向解决方案提供商提出的要求。

> [!TIP]
> 如果有任何一项信息发生更改，请务必在计划实际部署之前，将此更改告知解决方案提供商。

## <a name="onsite-deployment-phase"></a>现场部署阶段
硬件解决方案提供商的现场工程师必须在场才能开始部署 Azure Stack。 若要确保部署成功，请确保通过部署工作表提供的所有信息都未更改。

在部署过程中，现场工程师应进行以下检查：

- 检查所有布线和边界连接，确保解决方案正确定位在一起，且符合要求。
- 配置解决方案的 HLH（硬件生命周期主机，如果存在的话）。
- 检查以确保所有 BMC、BIOS 和网络设置均正确。
- 请确保所有组件的固件都是解决方案的最新批准版本。
- 开始部署。

> [!NOTE]
> 现场工程师可能需要一个工作周的时间来完成部署过程。

## <a name="post-deployment-phase"></a>部署后阶段
在集成后的阶段，合作伙伴必须先执行几个步骤，然后才能将解决方案移交给客户。 在此阶段，验证非常重要，它可以确保系统部署正确且运行正常。 

OEM 合作伙伴应执行的操作包括：

- [运行 test-azurestack](azure-stack-diagnostic-test.md)。

- [注册到 Azure](azure-stack-registration.md)。

- [Marketplace 联合](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)。

- 备份交换机配置和 HLH 配置文件。

- 删除 DVM。

- 准备客户摘要以进行部署。

- [检查更新，确保解决方案软件已更新到最新版本](./azure-stack-updates.md)。

有几个步骤是必需或可选的，具体取决于安装类型。

- 如果部署是使用 [AD FS](azure-stack-integrate-identity.md) 完成的，则 Azure Stack 阵列需要与客户自己的 AD FS 相集成。

  > [!NOTE]
  > 此步骤由客户负责，不过，合作伙伴可以选择性地提供相关服务。

- 与相关合作伙伴的现有监视系统相集成。

  -   [System Center Operations Manager 集成](azure-stack-integrate-monitor.md)也支持机群管理功能。

  -   [Nagios 集成](azure-stack-integrate-monitor.md#integrate-with-nagios)。

## <a name="overall-timeline"></a>整体时间表

![Azure Stack 现场部署的总体时间线](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>支持
Azure Stack 可以在整个系统生命周期内实现 Azure 一致的集成支持体验。 若要完全支持 Azure Stack 集成系统，客户需要两个支持合同：一个适用于 Azure 服务支持的 Microsoft （或其云解决方案提供商），另一个用于系统支持的硬件提供程序。 集成的支持体验提供协调的升级和解决方法，使客户能够获得一致的支持体验，而不管他们首先拜访的是谁。 对于已拥有 Premier、Azure 标准/ProDirect 或与 Microsoft 的合作伙伴支持的客户，包括 Azure Stack 软件支持。

集成支持体验利用了案例交换机制来双向传输支持案例和 Microsoft 与硬件合作伙伴之间的案例更新。 Microsoft Azure Stack 将遵循[新式生命周期策略](https://support.microsoft.com/help/30881)。

## <a name="next-steps"></a>后续步骤
详细了解[一般数据中心集成注意事项](azure-stack-datacenter-integration.md)。
