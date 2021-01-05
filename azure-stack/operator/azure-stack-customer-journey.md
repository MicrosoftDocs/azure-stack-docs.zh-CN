---
title: Azure Stack Hub 数据中心集成演练
description: 了解在数据中心现场成功部署 Azure Stack Hub 后预期产生的效果（从规划到部署后）。
author: PatAltimore
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 307a24fdf42ce5e007f74a9cf0072b5124d247b2
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871423"
---
# <a name="azure-stack-hub-datacenter-integration-walkthrough"></a>Azure Stack Hub 数据中心集成演练

本文介绍 Azure Stack Hub 数据中心集成的端到端过程，涉及从购买到部署后支持的整个过程。 集成是客户、解决方案提供商和 Microsoft 之间的协作项目。 单击以下选项卡可以查看项目中每个成员的特定步骤，并查看后续部分，大致了解项目时间线的不同阶段。 

# <a name="customer"></a>[客户](#tab/customer)

1. 描述用例和要求
1. 确定计费模型
1. 查看和批准合同
1. 完成 [部署工作表](azure-stack-deployment-worksheet.md)
1. 确保满足部署先决条件
1. 准备数据中心 
1. 在部署过程中提供订阅信息
1. 解决有关提供的数据的任何问题

# <a name="partner"></a>[合作伙伴](#tab/partner)

1. 根据客户要求建议解决方案选项
1. 提议概念证明 (POC) 
1. 确定支持级别
1. 准备好与客户签署的合同
1. 创建客户采购订单
1. 指定传递计划
1. 让客户与 Microsoft 联系 
1. 在部署时培训客户 
1. 帮助客户验证收集的数据
1. 安装和验证基线版本和 Microsoft 部署工具包
1. 将硬件寄送到客户所在地
1. 提供现场工程师
1. 机架和堆栈
1. 部署硬件生命周期主机 (HLH) 
1. 部署 Azure Stack Hub
1. 移交给客户

# <a name="microsoft"></a>[Microsoft](#tab/micro)

1. 与合作伙伴合作以获取预售支持
2. 准备软件许可与合同
3. 提供工具以收集数据中心集成要求
4. 提供每月基线版本和工具链更新
5. Microsoft 支持工程师会针对任何部署问题提供协助

---

## <a name="planning"></a>规划
Microsoft 或 Azure Stack 中心解决方案合作伙伴将有助于评估你的目标。 他们会帮助你确定如下问题：

-   Azure Stack Hub 解决方案是否适合你的组织？
-   组织适合使用哪种计费模型和许可模型？
-   需要哪种规模的解决方案？
-   电源和散热要求是什么？

使用 [Azure Stack Hub Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) 来调查和分析最佳硬件容量和配置以满足你的需求。 

## <a name="ordering"></a>排序
你的组织承诺购买 Azure Stack Hub、签署合同和采购订单，并向解决方案提供商提供集成要求数据。

## <a name="pre-deployment"></a>部署前
决定如何将 Azure Stack Hub 集成到数据中心。 Microsoft 与解决方案提供商合作发布[部署工作表](azure-stack-deployment-worksheet.md)，帮助你收集必要的信息。
[一般数据中心集成注意事项](azure-stack-datacenter-integration.md)一文提供的信息可帮助你完成该模板（名为“部署工作表”）。

> [!IMPORTANT]
> 在对解决方案进行排序之前，将调查所有先决条件，这样有助于防止部署延迟。 验证先决条件可能需要一些时间，并且需要组织中的不同部门进行协调和数据收集。 

需选择以下项：

- **Azure Stack Hub 连接模型和标识提供者**。 可以选择[在已连接到 Internet（和 Azure）时或断开连接时](azure-stack-connection-models.md)部署 Azure Stack Hub。 若要从 Azure Stack Hub（包括混合方案）获得最大效益，建议在连接到 Azure 时进行部署。 选择 Active Directory 联合身份验证服务 (AD FS) 还是 Azure Active Directory (Azure AD) 是在部署时必须进行的一次性决策。 **以后，除非重新部署整个系统，否则将无法更改标识提供者**。

- **许可模型**。 可供选择的许可模型选项取决于现有的部署类型。 标识提供者选项与租户虚拟机或其使用的标识系统和帐户无关。
    - 采用[离线部署](azure-stack-disconnected-deployment.md)的客户只有一个选项：基于容量的计费。

    - 采用[联网部署](azure-stack-connected-deployment.md)的客户可以选择基于容量的计费或即用即付。 基于容量的计费需要企业协议 (EA) Azure 订阅才能注册。 注册时需要该订阅，这样才能通过 Azure 订阅为 Azure 市场中的项提供可用性。

- **网络集成**。 [网络集成](azure-stack-network.md)对于部署、操作和管理 Azure Stack Hub 系统至关重要。 需要考虑到多种因素才能确保 Azure Stack Hub 解决方案具有复原能力，并提供一个高可用性物理基础设施来支持其操作。

- **防火墙集成**。 建议[使用防火墙](azure-stack-firewall.md)来帮助保护 Azure Stack Hub。 防火墙有助于防止 DDOS 攻击，以及执行入侵检测和内容检查。 但应注意，它可能成为 Azure 存储服务的吞吐量瓶颈。

- **证书要求**。 必须在现场工程师抵达数据中心进行部署之前准备好全部[所需的证书](azure-stack-pki-certs.md)。

通过部署工作表收集所有必要信息后，解决方案提供商将会根据收集的数据开始工厂处理，确保将 Azure Stack Hub 成功集成到数据中心。

## <a name="hardware-delivery"></a>硬件交付 
当解决方案抵达你的设施时，解决方案提供商将与你一起安排日程。 收到并安置好解决方案后，需要与解决方案提供商一起安排好时间，让工程师到现场执行 Azure Stack Hub 部署。

在现场工程师抵达开始部署解决方案之前，将所有必要数据锁住并准备好，这一点 **至关重要**。

-   必须购买并准备好所有证书。

-   必须确定区域名。

-   所有网络集成参数都已确认，且符合你向解决方案提供商提出的要求。

> [!TIP]
> 如果有任何一项信息发生更改，请务必在计划实际部署之前，将此更改告知解决方案提供商。

## <a name="onsite-deployment"></a>现场部署 
硬件解决方案提供商的现场工程师必须在场才能开始部署 Azure Stack Hub。 为确保部署成功，请确保所有通过部署工作表提供的信息未曾更改。

以下检查是部署体验期间现场工程师应执行的操作：

- 检查所有布线和边界连接，确保解决方案正确定位在一起，且符合要求。
- 配置解决方案的 HLH（硬件生命周期主机，如果存在的话）。
- 检查并确保所有 BMC、BIOS 和网络设置正确。
- 确保所有组件的固件使用解决方案批准的最新版本。
- 开始部署。

> [!NOTE]
> 现场工程师可能需要一个工作周的时间来完成部署过程。

## <a name="post-deployment"></a>后期部署 
在集成后的阶段，合作伙伴必须先执行几个步骤，然后才能将解决方案移交给客户。 在此阶段，验证非常重要，它可以确保系统部署正确且运行正常。 

OEM 合作伙伴应执行的操作包括：

- [运行 test-azurestack](azure-stack-diagnostic-test.md)。

- [注册到 Azure](azure-stack-registration.md)。

- [市场联合](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items)。

- 备份交换机配置文件和 HLH 配置文件。

- 去除 DVM。

- 为客户准备部署摘要。

- [检查更新以确保解决方案软件已更新到最新版本](./azure-stack-updates.md)。

有几个步骤是必需或可选的，具体取决于安装类型。

- 如果部署是使用 [AD FS](azure-stack-integrate-identity.md) 完成的，则 Azure Stack Hub 阵列需要与客户自己的 AD FS 相集成。

  > [!NOTE]
  > 此步骤由客户负责，不过，合作伙伴可以选择性地提供相关服务。

- 与相关合作伙伴的现有监视系统相集成。

  -   [System Center Operations Manager 集成](azure-stack-integrate-monitor.md)也支持机群管理功能。

  -   [Nagios 集成](azure-stack-integrate-monitor.md#integrate-with-nagios)。

## <a name="schedule"></a>计划

![Azure Stack Hub 现场部署的大致时间线](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>支持
Azure Stack Hub 可以在整个系统生命周期内实现 Azure 一致的集成支持体验。 若要完全支持 Azure Stack 集线器集成系统，客户需要两个支持合同：一个适用于 Microsoft (或其云解决方案提供商) 提供 Azure 服务支持，另一个用于系统支持的硬件提供程序。 集成支持体验提供协调的升级和解决方法，因此，不管客户首先联系哪一家提供商，都能获得一致的支持体验。 对于已拥有 Premier、Azure 标准/ProDirect 或与 Microsoft 的合作伙伴支持的客户，包括 Azure Stack 集线器软件支持。

集成支持体验利用了案例交换机制来双向传输支持案例和 Microsoft 与硬件合作伙伴之间的案例更新。 Microsoft Azure Stack 中心将遵循 [新式生命周期策略](https://support.microsoft.com/help/30881)。

## <a name="next-steps"></a>后续步骤
详细了解[一般数据中心集成注意事项](azure-stack-datacenter-integration.md)。
