---
title: Azure Stack datacenter integration 演练 |Microsoft Docs
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
ms.openlocfilehash: 6ea5248881e10ca9e96ba423ecb4ea5569f00211
ms.sourcegitcommit: 20d1c0ab3892e9c4c71d5b039457f1e15b1c84c7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73618257"
---
# <a name="azure-stack-datacenter-integration-walkthrough"></a>Azure Stack datacenter integration 演练

本文介绍了从购买集成系统到通过解决方案提供商成功实现现场部署的端到端 Azure Stack 客户体验。 使用此信息来简化您的旅程，并帮助您为 Azure Stack 客户提供预期。

作为 Azure Stack 客户，你应预测以下阶段：

# <a name="customertabcustomer"></a>[客户](#tab/customer)

1. 介绍目标用例并指定需求。
2. 确定要使用、查看和批准合同的计费模型。
3. 完成[部署工作表](azure-stack-deployment-worksheet.md)，并确保满足所有部署先决条件并准备好进行部署。
4. 通过确保所有必需的电源和冷却、边界连接和其他所需的数据中心集成要求准备就绪，从而准备数据中心。
5. 在部署过程中提供订阅凭据，并在提供的数据上出现问题时提供支持。

# <a name="partnertabpartner"></a>[合作伙伴](#tab/partner)

1. 规划阶段：
   - 根据客户要求推荐解决方案选项。
   - 如有必要，建议概念证明（POC）。
   - 建立业务关系。
   - 确定支持级别。
2. 订单流程：
   - 为客户准备必要的协定。
   - 创建客户采购订单。
   - 决定交付时间线。
   - 必要时将客户连接到 Microsoft。
3. 预先部署
   - 为客户提供必要的培训，确保了解所有部署先决条件和数据中心集成选项。
   - 帮助客户验证所收集的数据，以确保完整性和准确性。
4. 出厂过程
   - 应用上次验证的基线生成。
   - 应用所需的 Microsoft 部署工具包。
5. 硬件交付：
   - 将硬件发运到客户站点。
6. 现场部署
   - 部署由现场工程师处理。
   - 机架和堆栈。
   - 硬件生命周期主机（HLH）部署。
   - Azure Stack 部署。
   - 交付给客户。

# <a name="microsofttabmicro"></a>[Microsoft](#tab/micro)

1. 与合作伙伴合作提供售前支持。
2. 根据需要准备软件许可和约定。
3. 提供必要的工具来向客户收集数据中心集成要求和文档。
4. 提供每月节奏的最新基准生成和工具链更新。
5. Microsoft 支持工程师提供任何部署问题的帮助。



## <a name="planning-phase"></a>规划阶段
在规划阶段，Microsoft 或 Azure Stack 解决方案合作伙伴将与你一起评估并了解你的需求，以便确定 Azure Stack 是否适合你的解决方案：

他们将帮助你确定以下问题：

-   Azure Stack 适用于你的组织的正确解决方案吗？

-   需要哪种大小的解决方案？

-   你的组织将使用哪种类型的计费和许可模式？

-   电源和冷却的要求是什么？

为了确保硬件解决方案能够满足您的需求， [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)用于帮助进行预购买计划，以确定 Azure Stack 硬件解决方案的适当容量和配置。

该电子表格*并未*用于替代最适合你的需求的硬件解决方案，而是用作你自己的调查和分析。 规划 Azure Stack 部署时，还应查看 Azure Stack 集成系统的[常规数据中心集成注意事项](azure-stack-datacenter-integration.md)。

## <a name="order-process-phase"></a>订单处理阶段
在此阶段，许多与可行性相关的问题都已得到回答。 现在，你已准备好提交 Azure Stack，并在对所有必需的合同和采购订单进行签名后，将要求你向解决方案提供商提供集成要求数据。

## <a name="pre-deployment-phase"></a>预先部署阶段
在此阶段，需要确定要如何将 Azure Stack 集成到数据中心。 为了简化此过程，Microsoft 将要求模板结合在一起，以帮助您收集规划环境中的集成系统所需的信息。 此要求模板与解决方案提供商协作。

"[常规数据中心集成注意事项](azure-stack-datacenter-integration.md)" 一文提供的信息可帮助你完成模板（称为 "部署工作表"）。

> [!IMPORTANT]
> 在此阶段，重要的是，在对解决方案进行排序之前，调查并决定所有先决条件信息。 请注意，此步骤非常耗时，需要从组织中的多个学科收集协调和数据。 不正确或不完整的信息可能会导致部署时间较长。 

在部署前阶段，需要确定以下各项：

- **Azure Stack 连接模型和标识提供程序**。 你可以选择部署 Azure Stack[连接到 internet （和 Azure）或断开](azure-stack-connection-models.md)连接。 若要充分利用 Azure Stack 的好处，包括混合方案，你需要部署连接到 Azure。 选择 Active Directory 联合身份验证服务（AD FS）或 Azure Active Directory （Azure AD）是在部署时必须进行的一次决定。 **你无法在以后更改标识提供程序，而无需重新部署整个系统**。

- **授权模型**。 要从中进行选择的许可模式选项取决于你所拥有的部署类型。 你的标识提供者选择与租户虚拟机或其使用的标识系统和帐户无关。
    - 处于[断开连接的部署](azure-stack-disconnected-deployment.md)中的客户只有一个选项：基于容量的计费。

    - 处于[连接状态的部署](azure-stack-connected-deployment.md)中的客户可以选择基于容量的计费和即用即付。 基于容量的计费需要用于注册的企业协议（EA） Azure 订阅。 这对于注册是必需的，它通过 Azure 订阅提供 Azure Marketplace 中项目的可用性。

- **网络集成**。 [网络集成](azure-stack-network.md)对于部署、操作和管理 Azure Stack 系统至关重要。 请注意以下几个事项，以确保 Azure Stack 解决方案具有复原能力，并提供高度可用的物理基础结构来支持其操作。

- **防火墙集成**。 建议[使用防火墙](azure-stack-firewall.md)来帮助保护 Azure Stack。 防火墙可帮助防止 DDOS 攻击、入侵检测和内容检查。 但请注意，它可能成为 Azure 存储服务的吞吐量瓶颈。


- **证书要求**。 在现场工程师到达数据中心进行部署*之前*，必须提供所有[必需的证书](azure-stack-pki-certs.md)。

通过 "部署" 工作表收集所有先决条件信息后，解决方案提供商将基于收集的数据启动工厂流程，以确保成功地将 Azure Stack 集成到你的数据中心。

## <a name="hardware-delivery-phase"></a>硬件交付阶段
解决方案提供商将与你合作，以便在解决方案到达你的设施时进行计划。 一旦接收并投入使用后，就需要使用解决方案提供商计划时间，让工程师在现场执行 Azure Stack 部署。

在*现场工程师到达部署解决方案之前*，所有先决条件数据都已锁定并可用，这一点非常**重要**。

-   所有证书都必须购买并准备就绪。

-   必须决定区域名称。

-   所有网络集成参数均已完成，并与你与解决方案提供商共享的内容相匹配。

> [!TIP]
> 如果此信息中的任何一个已更改，请确保在计划实际部署之前与解决方案提供商传达更改。

## <a name="onsite-deployment-phase"></a>现场部署阶段
若要部署 Azure Stack，需要提供硬件解决方案提供商提供的现场工程师来启动部署。 若要确保部署成功，请确保通过部署工作表提供的所有信息都未更改。

在部署过程中，现场工程师应进行以下检查：

- 检查所有布线和边界连接性，以确保解决方案正确放置在一起并满足您的要求。
- 配置解决方案 HLH （硬件生命周期主机）（如果存在）。
- 检查以确保所有 BMC、BIOS 和网络设置均正确。
- 请确保所有组件的固件都是解决方案的最新批准版本。
- 启动部署。

> [!NOTE]
> 现场工程师的部署过程可能需要一周来完成工作。

## <a name="post-deployment-phase"></a>部署后阶段
在将解决方案移交给客户后，必须由合作伙伴执行多个步骤。 在此阶段，验证很重要，因为确保系统部署正确并正确执行。 

应由 OEM 合作伙伴执行的操作包括：

- [运行 test-azurestack](azure-stack-diagnostic-test.md)。

- [注册到 Azure](azure-stack-registration.md)。

- [Marketplace 联合](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)。

- 备份交换机配置和 HLH 配置文件。

- 删除 DVM。

- 准备客户摘要以进行部署。

- [检查更新，确保解决方案软件已更新到最新版本](./azure-stack-updates.md)。

根据安装类型，有几个步骤是必需的或可选的。

- 如果使用[AD FS](azure-stack-integrate-identity.md)完成部署，则 Azure Stack stamp 将需要与客户自己的 AD FS 集成。

  > [!NOTE]
  > 此步骤是客户的责任，但合作伙伴可以选择提供服务来实现此目的。

- 与相应合作伙伴的现有监视系统集成。

  -   [System Center Operations Manager 集成](azure-stack-integrate-monitor.md)还支持汽油管理功能。

  -   [Nagios 集成](azure-stack-integrate-monitor.md#integrate-with-nagios)。

## <a name="overall-timeline"></a>整体时间线

![Azure Stack 现场部署的总体时间线](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>支持
Azure Stack 启用了 Azure 一致的集成支持体验，涵盖完整的系统生命周期。 若要完全支持 Azure Stack 集成系统，客户需要两个支持合同：一个适用于 Azure 服务支持的 Microsoft （或其云解决方案提供商），另一个用于系统支持的硬件提供程序。 集成的支持体验提供协调的升级和解决方法，使客户能够获得一致的支持体验，而不管他们首先拜访的是谁。 对于已拥有 Premier、Azure 标准/ProDirect 或与 Microsoft 的合作伙伴支持的客户，包括 Azure Stack 软件支持。

集成支持体验利用了案例交换机制来双向传输支持案例和 Microsoft 与硬件合作伙伴之间的案例更新。 Microsoft Azure Stack 将遵循[新式生命周期策略](https://support.microsoft.com/help/30881)。

## <a name="next-steps"></a>后续步骤
了解有关[一般数据中心集成注意事项](azure-stack-datacenter-integration.md)的详细信息。
