---
title: Azure Stack 数据中心集成演练 | Microsoft Docs
description: 了解在数据中心现场成功部署 Azure Stack 后预期产生的效果。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: asganesh
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: b68618bf1755310471901e89b8b154e34e3c0794
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172718"
---
# <a name="azure-stack-datacenter-integration"></a>Azure Stack 数据中心集成

本文介绍从采购集成的系统到解决方案提供商成功完成现场部署在内的端到端 Azure Stack 客户体验。 使用此信息可以简化旅程，并帮助 Azure Stack 客户设置预期。

Azure Stack 客户应该为以下阶段做好准备：

|     |规划阶段|订单处理|部署前|工厂处理|硬件交付|现场部署|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|与合作伙伴洽谈以提供售前支持。|准备所需的软件许可与合同。|为客户提供所需的工具用于收集数据中心集成要求和文档。|每月提供最新的基线版本和工具链更新。|不适用|Microsoft 支持工程师帮助进行任何部署问题。|
|**合作伙伴**|根据客户要求建议解决方案选项。<br><br>根据需要提议概念证明 (POC)。<br><br>建立业务关系。<br><br>确定支持级别。|准备好与客户签署的必要合同。<br><br>创建客户采购订单。<br><br>确定交付时间表。<br><br>如有必要，请与 Microsoft 联系客户。|为客户提供必要的培训，确保客户了解所有部署先决条件和数据中心集成选项。<br><br>帮助客户验证所收集的数据，以确保完整性和准确性。|应用最后一个已验证的基线版本。<br><br>应用所需的 Microsoft 部署工具包。|将硬件寄送到客户所在地。|由现场工程师处理部署。<br><br>机架和堆栈。<br><br>硬件生命周期主机 (HLH) 部署。<br><br>Azure Stack 部署。<br><br>移交给客户。|
|**客户**|描述预期用例，并指定要求。|确定要使用的计费模型，并审查和审批合同。|完成[部署工作表](azure-stack-deployment-worksheet.md)，并确保所有部署先决条件都都满足并准备好进行部署。|不适用|准备数据中心：确保已准备好所有必要的电源和散热设备、边界连接，并满足其他必要的数据中心集成要求。|可在部署期间提供订阅凭据，并在所提供的数据有疑问时提供支持。|
| | | | | | | |


## <a name="planning-phase"></a>规划阶段
Microsoft 或 Azure Stack 解决方案合作伙伴，将工作与你一同评估并了解你的需求来确定 Azure Stack 是否适合你的解决方案时，规划阶段：

他们将帮助你确定以下事宜：

-   Azure Stack 解决方案是否适合你的组织？

-   需要哪种规模的解决方案？

-   组织适合使用哪种计费模型和许可模型？

-   电源和散热要求是什么？

若要确保硬件解决方案完美符合需求，可在采购前的规划中使用 [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) 来帮助确定 Azure Stack 硬件解决方案适用的容量和配置。

该电子表格并非旨在替代你自己对硬件解决方案适用度的调查与分析。 规划 Azure Stack 部署时，还应该查看适用于 Azure Stack 集成系统的[一般数据中心集成注意事项](azure-stack-datacenter-integration.md)。

## <a name="order-process-phase"></a>订单处理阶段
在此阶段，很多关于可行性方面的问题已有解答。 现已准备好要完成 Azure Stack 的采购，在签署所有必要的合同与采购订单后，需要向解决方案提供商提供集成要求数据。

## <a name="pre-deployment-phase"></a>部署前的阶段
在此阶段，需要确定如何将 Azure Stack 集成到数据中心。 为了简化此过程，Microsoft，与解决方案提供商，协作将汇总到要求模板可帮助您收集所需的信息来规划集成的系统部署在环境中。

[一般数据中心集成注意事项](azure-stack-datacenter-integration.md)一文提供的信息可帮助你完成该模板（名为“部署工作表”）。 

> [!IMPORTANT]
> 在此阶段，必须先调查并确定所有必要信息，然后再订购解决方案。 请注意，此步骤相当耗时，需要与组织中的多个专业单位协调，并从中收集数据。 信息不正确或不完整可能导致部署时间延长。 

在部署前的阶段，需要确定以下事宜：

- **Azure Stack 连接模型和标识提供者**。 可以选择在[连接到 Internet（和 Azure）时或者离线时](azure-stack-connection-models.md)部署 Azure Stack。 若要从 Azure Stack（包括混合方案）获得最大效益，建议在连接到 Azure 时进行部署。 选择 Active Directory 联合身份验证服务 (AD FS) 还是 Azure Active Directory (Azure AD) 是在部署时必须进行的一次性决策。 **无需重新部署整个系统，不能以后更改此**。

- **许可模型**。 可供选择的许可模型选项取决于现有的部署类型。 标识提供者选项与租户虚拟机或其使用的标识系统和帐户无关。
    - 采用[离线部署](azure-stack-disconnected-deployment.md)的客户只有一个选项：基于容量的计费。

    - 采用[联网部署](azure-stack-connected-deployment.md)的客户可以选择基于容量的计费或即用即付。 基于容量的计费需要企业协议 (EA) Azure 订阅才能注册。 注册时需要该订阅，这样才能通过 Azure 订阅为市场中的项提供可用性。

- **网络集成**。 [网络集成](azure-stack-network.md)对于部署、操作和管理 Azure Stack 系统至关重要。 需要考虑到多种因素才能确保 Azure Stack 解决方案具有复原能力，并提供一个高可用性物理基础设施来支持其操作。

- **防火墙集成**。 建议[使用防火墙](azure-stack-firewall.md)来帮助保护 Azure Stack。 防火墙有助于防止 DDOS 攻击，以及执行入侵检测和内容检查。 但应注意，它可能成为 Azure 存储服务的吞吐量瓶颈。


- **证书要求**。 必须在现场工程师抵达数据中心进行部署之前准备好全部[所需的证书](azure-stack-pki-certs.md)。

通过部署工作表收集所有必要信息后，解决方案提供商将会根据收集的数据开始工厂处理，确保将 Azure Stack 成功集成到数据中心。

## <a name="hardware-delivery-phase"></a>硬件交付阶段
当解决方案抵达你的设施时，解决方案提供商将与你一起安排日程。 收到并安置好解决方案后，需要与解决方案提供商一起安排好时间，让工程师到现场执行 Azure Stack 部署。

在现场工程师抵达开始部署解决方案之前，将所有必要数据锁住并准备好，这一点**至关重要**。

-   必须购买并准备好所有证书。

-   必须确定区域名。

-   所有网络集成参数都已确认，且符合你向解决方案提供商提出的要求。

> [!TIP]
> 如果有任何一项信息发生更改，请务必在计划实际部署之前，将此更改告知解决方案提供商。

## <a name="onsite-deployment-phase"></a>现场部署阶段
硬件解决方案提供商的现场工程师必须在场才能开始部署 Azure Stack。 为确保部署成功，请确保所有通过部署工作表提供的信息未曾更改。 

下面是在部署体验期间，现场工程师应执行的操作：

- 检查所有布线和边界连接，确保解决方案正确定位在一起，且符合要求。
- 配置解决方案的 HLH（硬件生命周期主机，如果存在的话）。
- 检查并确保所有 BMC、BIOS 和网络设置正确。
- 确保所有组件的固件使用解决方案批准的最新版本。
- 开始部署。

> [!NOTE]
> 现场工程师可能需要一个工作周的时间来完成部署过程。

## <a name="post-deployment-phase"></a>部署后阶段
在集成后的阶段，合作伙伴必须先执行几个步骤，然后才能将解决方案移交给客户。 在此阶段，验证非常重要，它可以确保系统部署正确且运行正常。 

OEM 合作伙伴应执行的操作包括：

- [运行 test-azurestack](azure-stack-diagnostic-test.md)

- [注册到 Azure](azure-stack-registration.md)

- [市场联合](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)

- 备份交换机配置文件和 HLH 配置文件

- 去除 DVM

- 为客户准备部署摘要

- [检查更新以确保解决方案软件已更新到最新版本](./azure-stack-updates.md)

有几个步骤是必需或可选的，具体取决于安装类型。

- 如果部署是使用 [AD FS](azure-stack-integrate-identity.md) 完成的，则 Azure Stack 阵列需要与客户自己的 AD FS 相集成。

  > [!NOTE]
  > 此步骤由客户负责，不过，合作伙伴可以选择性地提供相关服务。

- 与相关合作伙伴的现有监视系统相集成。

  -   [System Center Operations Manager 集成](azure-stack-integrate-monitor.md)也支持机群管理功能。

  -   [Nagios 集成](azure-stack-integrate-monitor.md#integrate-with-nagios)

## <a name="overall-timeline"></a>整体时间表

![](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>支持
Azure Stack 可以在整个系统生命周期内实现 Azure 一致的集成支持体验。 若要完全支持 Azure Stack 集成系统，客户需要签署两份支持合同：与 Microsoft（或其云解决方案提供商）签署有关 Azure 服务支持的合同，与硬件提供商签署有关系统支持的合同。 集成支持体验提供协调的事务升级和解决方法，不管客户首先联系哪一家提供商，都能获得一致的支持体验。 对于已安装了高级客户，客户 Azure 的标准 / 包含了与 Microsoft Azure Stack 软件支持的 ProDirect 或合作伙伴支持。

集成的支持体验使利用用例 Exchange 支持案例和事例更新的 Microsoft 硬件合作伙伴之间的双向传输机制。 Microsoft Azure Stack 将遵循[Modern Lifecycle 策略](https://support.microsoft.com/help/30881)。

## <a name="next-steps"></a>后续步骤
详细了解[一般数据中心集成注意事项](azure-stack-datacenter-integration.md)。
