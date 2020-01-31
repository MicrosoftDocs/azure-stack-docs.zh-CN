---
title: Azure Stack 中心数据中心集成演练
description: 从规划到后期部署开始，了解你的数据中心内成功的现场部署 Azure Stack。
author: ihenkel
ms.topic: article
ms.date: 11/07/2019
ms.author: inhenkel
ms.reviewer: asganesh
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 6711483384ba9985ced7396d19e025e0ed3240c6
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882504"
---
# <a name="azure-stack-hub-datacenter-integration-walkthrough"></a>Azure Stack 中心数据中心集成演练

本文介绍了从购买到后期部署支持 Azure Stack 中心数据中心集成的端到端过程。 集成是客户、解决方案提供商和 Microsoft 之间的协作项目。 单击以下选项卡可以查看项目中每个成员的特定步骤，并查看后续部分，了解项目时间线的不同阶段。 

# <a name="customertabcustomer"></a>[客户](#tab/customer)

1. 描述用例和要求
1. 确定计费模型
1. 查看和批准合同
1. 完成[部署工作表](azure-stack-deployment-worksheet.md)
1. 确保满足部署先决条件
1. 准备数据中心 
1. 在部署过程中提供订阅信息
1. 解决有关提供的数据的任何问题

# <a name="partnertabpartner"></a>[合作伙伴](#tab/partner)

1. 根据客户要求推荐解决方案选项
1. 建议概念证明（POC） 
1. 确定支持级别
1. 为客户准备合同
1. 创建客户采购订单
1. 决定交付计划
1. 将客户与 Microsoft 连接 
1. 在部署时培训客户 
1. 帮助客户验证收集的数据
1. 安装和验证基准生成和 Microsoft 部署工具包
1. 将硬件发运到客户站点
1. 提供现场工程师
1. 机架和堆栈
1. 部署硬件生命周期主机（HLH） 
1. 部署 Azure Stack 中心
1. 向客户交付

# <a name="microsofttabmicro"></a>[Microsoft](#tab/micro)

1. 与合作伙伴合作以获取售销售支持
2. 准备软件许可和协定
3. 提供工具以收集数据中心集成要求
4. 提供每月基线生成和工具链更新
5. Microsoft 支持工程师提供任何部署问题的帮助

---

## <a name="planning"></a>规划
Microsoft 或 Azure Stack 中心解决方案合作伙伴将有助于评估你的目标。 他们将帮助你确定类似于以下内容的问题：

-   Azure Stack 集线器适用于你的组织的解决方案吗？
-   你的组织将使用哪种类型的计费和许可模式？
-   需要哪种大小的解决方案？
-   电源和冷却的要求是什么？

使用[Azure Stack 中心 Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822)调查和分析最佳硬件容量和配置，满足你的需求。 

## <a name="ordering"></a>订购
你的组织承诺购买 Azure Stack 中心、签署合同和采购订单，并向解决方案提供商提供集成要求数据。

## <a name="pre-deployment"></a>预先部署
你决定如何将 Azure Stack 集线器集成到你的数据中心。 Microsoft 与解决方案提供商合作发布[部署工作表](azure-stack-deployment-worksheet.md)，以帮助您收集必要的信息。
"[常规数据中心集成注意事项](azure-stack-datacenter-integration.md)" 一文提供的信息可帮助你完成模板（称为 "部署工作表"）。

> [!IMPORTANT]
> 在对解决方案进行排序之前，将调查所有先决条件，以帮助防止部署延迟。 验证先决条件可能需要一些时间，并且需要从组织中的不同部门收集协调和数据。 

选择以下项：

- **Azure Stack 中心连接模型和标识提供者**。 你可以选择部署[连接到 internet （和到 Azure）或断开](azure-stack-connection-models.md)连接 Azure Stack 集线器。 若要从 Azure Stack 中心获得最大优势，包括混合方案，你需要将连接到 Azure。 选择 Active Directory 联合身份验证服务（AD FS）或 Azure Active Directory （Azure AD）是在部署时必须进行的一次决定。 **你无法在以后更改标识提供程序，而无需重新部署整个系统**。

- **授权模型**。 要从中进行选择的许可模式选项取决于你所拥有的部署类型。 你的标识提供者选择与租户虚拟机或其使用的标识系统和帐户无关。
    - 处于[断开连接的部署](azure-stack-disconnected-deployment.md)中的客户只有一个选项：基于容量的计费。

    - 处于[连接状态的部署](azure-stack-connected-deployment.md)中的客户可以选择基于容量的计费和即用即付。 基于容量的计费需要用于注册的企业协议（EA） Azure 订阅。 这对于注册是必需的，它通过 Azure 订阅提供 Azure Marketplace 中项目的可用性。

- **网络集成**。 [网络集成](azure-stack-network.md)对于部署、操作和管理 Azure Stack 中心系统至关重要。 考虑到确保 Azure Stack 集线器解决方案具有复原能力，并提供高度可用的物理基础结构来支持其操作，有几个注意事项。

- **防火墙集成**。 建议[使用防火墙](azure-stack-firewall.md)来帮助保护 Azure Stack 中心。 防火墙可帮助防止 DDOS 攻击、入侵检测和内容检查。 但请注意，它可能成为 Azure 存储服务的吞吐量瓶颈。

- **证书要求**。 在现场工程师到达数据中心进行部署*之前*，必须提供所有[必需的证书](azure-stack-pki-certs.md)。

通过 "部署" 工作表收集所有先决条件信息后，解决方案提供商将基于收集的数据启动工厂流程，以确保成功地将 Azure Stack 集线器集成到你的数据中心。

## <a name="hardware-delivery"></a>硬件交付 
解决方案提供商将与你合作，以便在解决方案到达你的设施时进行计划。 一旦接收并投入使用后，就需要使用解决方案提供商计划时间，让工程师在现场执行 Azure Stack 中心部署。

在*现场工程师到达部署解决方案之前*，所有先决条件数据都已锁定并可用，这一点非常**重要**。

-   所有证书都必须购买并准备就绪。

-   必须决定区域名称。

-   所有网络集成参数均已完成，并与你与解决方案提供商共享的内容相匹配。

> [!TIP]
> 如果此信息中的任何一个已更改，请确保在计划实际部署之前与解决方案提供商传达更改。

## <a name="onsite-deployment"></a>现场部署 
若要部署 Azure Stack 中心，需要提供硬件解决方案提供商提供的现场工程师来启动部署。 若要确保部署成功，请确保通过部署工作表提供的所有信息都未更改。

在部署过程中，现场工程师应进行以下检查：

- 检查所有布线和边界连接性，以确保解决方案正确放置在一起并满足您的要求。
- 配置解决方案 HLH （硬件生命周期主机）（如果存在）。
- 检查以确保所有 BMC、BIOS 和网络设置均正确。
- 请确保所有组件的固件都是解决方案的最新批准版本。
- 启动部署。

> [!NOTE]
> 现场工程师的部署过程可能需要一周来完成工作。

## <a name="post-deployment"></a>后期部署 
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

- 如果使用[AD FS](azure-stack-integrate-identity.md)完成部署，则 Azure Stack 中心标记将需要与客户自己的 AD FS 集成。

  > [!NOTE]
  > 此步骤是客户的责任，但合作伙伴可以选择提供服务来实现此目的。

- 与相应合作伙伴的现有监视系统集成。

  -   [System Center Operations Manager 集成](azure-stack-integrate-monitor.md)还支持汽油管理功能。

  -   [Nagios 集成](azure-stack-integrate-monitor.md#integrate-with-nagios)。

## <a name="schedule"></a>计划

![Azure Stack 中心现场部署的总体时间线](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>支持
Azure Stack 中心实现了 Azure 一致的集成支持体验，涵盖完整的系统生命周期。 若要完全支持 Azure Stack 集线器集成系统，客户需要两个支持合同：一个适用于 Azure 服务支持的 Microsoft （或其云解决方案提供商），另一个用于系统支持的硬件提供程序。 集成的支持体验提供协调的升级和解决方法，使客户能够获得一致的支持体验，而不管他们首先拜访的是谁。 对于已拥有 Premier、Azure 标准/ProDirect 或与 Microsoft 的合作伙伴支持的客户，包括 Azure Stack 集线器软件支持。

集成支持体验利用了案例交换机制来双向传输支持案例和 Microsoft 与硬件合作伙伴之间的案例更新。 Microsoft Azure Stack 中心将遵循[新式生命周期策略](https://support.microsoft.com/help/30881)。

## <a name="next-steps"></a>后续步骤
了解有关[一般数据中心集成注意事项](azure-stack-datacenter-integration.md)的详细信息。
