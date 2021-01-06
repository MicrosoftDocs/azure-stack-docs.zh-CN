---
title: 保护在 Azure Stack 上部署的 VM | Microsoft Docs
description: 了解如何生成恢复计划来保护 Azure Stack 上部署的 VM，以防出现数据丢失和计划外停机。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: c57e421140dfea9eac949b5dcf97cc90745ab861
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910953"
---
# <a name="protect-vms-deployed-on-azure-stack-hub---ruggedized"></a>保护 Azure Stack 集线器上部署的 Vm-耐用

使用本文作为指南来制定一套保护 (Vm) 用户在 Azure Stack Hub 上部署的虚拟机的计划。

若要防止数据丢失和计划外停机，请在 Azure Stack 中心为基于 VM 的应用程序实施数据保护和灾难恢复计划。 实现的保护计划将取决于应用程序的业务要求和设计。 此计划应遵循组织通过 \' 全面的业务连续性和灾难恢复 (BC/DR) 策略建立的框架。 有关 Azure Stack 中心的 BC/DR 注意事项的详细概述，请参阅 Azure Stack：业务连续性和灾难恢复的注意事项。

## <a name="application-recovery-objectives"></a>应用程序恢复目标

确定你的组织可容忍每个应用程序的停机时间和数据丢失量。 通过对故障时间和数据丢失进行量化，你可以创建恢复计划来将灾难对你的组织的影响降到最低。 对于每个应用程序，请考虑：

- **恢复时间目标 (RTO)**\
    RTO 是指发生某个事件后，可接受应用不可用的最长时间。 例如，如果 RTO 是 90 分钟，则意味着从发生灾难开始，必须能够在 90 分钟内将应用还原到正常运行状态。 如果 RTO 低，可以持续运转一个后备部署，以防范区域性服务中断。

- **恢复点目标 (RPO)**\
    RPO 是指发生灾难期间，可接受数据丢失的最大持续时间。 例如，如果在单个数据库中存储数据并且未将数据复制到其他数据库，而是执行每小时备份，则最长可能会丢失一小时的数据。

执行评估以定义每个应用程序的 RTO 和 RPO。

要考虑的另一个重要指标是恢复 (MTTR) 的 **平均时间** ，这是在发生故障后还原应用程序所需的平均时间。 MTTR 反映的是系统的经验值。 如果 MTTR 超过 RTO，则系统中的故障将导致无法接受的业务中断，因为在 \' 定义的 RTO 内无法还原系统。

## <a name="protection-options-for-iaas-vms"></a>IaaS Vm 的保护选项

### <a name="backup-restore"></a>备份-还原

对于基于 VM 的应用，最常见的保护方案是使用备份软件。 备份 VM 通常包含 VM 内包含的操作系统、操作系统配置、应用程序二进制文件和永久应用程序数据。 使用来宾操作系统中的代理创建备份，以捕获应用程序、OS 或文件系统/卷。 另一种方法是通过依赖于与 Azure Stack 中心 Api 的集成来读取有关 VM 配置的信息并为附加到 VM 的磁盘拍摄快照，这种方法更少。 请注意，Azure Stack 集线器不支持直接从虚拟机监控程序进行备份。

### <a name="planning-your-backup-strategy"></a>规划备份策略

在计划备份策略和定义缩放要求时，首先应确定需要保护的 VM 实例的数目。 备份系统中的所有 Vm 可能并不是保护应用程序的最有效方法。 使用 Azure Stack 集线器，不应在 VM 级别备份规模集或可用性集中的 Vm。 这些 Vm 被视为暂时的，因为 Vm 集可以进行放大或缩小。理想情况下，任何需要保存的数据都在单独的存储库（如数据库或对象存储）中。 如果扩展体系结构中部署的应用程序包含必须保持和保护的数据，则这将需要使用应用程序提供的本机功能或依赖于代理的应用程序级备份。

在 Azure Stack 上备份 VM 的重要注意事项：

- **分类**
  - 考虑用户选择加入 VM 备份的模型。
  - 根据应用程序优先级或对业务的影响来定义恢复服务级别协议 (SLA)。
- **可伸缩**
  - 在载入大量新的 VM 时考虑进行交错式备份（如果必须备份）。
  - 评估可以有效地捕获和传输备份数据的备份产品，尽量减少解决方案上的资源内容。
  - 评估可以通过增量备份或差异备份有效存储备份数据的备份产品，尽量减少为环境中的所有 VM 创建完整备份的需求。
- **还原**
  - 备份产品可以还原虚拟磁盘、现有 VM 中的应用数据，或者整个 VM 资源和关联的虚拟磁盘。 所需的还原方案取决于计划还原应用的方式。 例如，从模板重新部署 SQL Server 并还原数据库而不是还原整个 VM 或 VM 集可能会更容易。

### <a name="replicationmanual-failover"></a>复制/手动故障转移

支持恢复的另一种方法是将数据复制到其他环境。 可以使用代理将数据的作用域限定为应用程序，例如数据库复制或来宾 OS 中的操作系统，也可以通过与 Azure Stack 中心 Api 集成来确定数据的作用域。 发生灾难时，需要故障转移到辅助位置。 使用 SQL 可用性组或在来宾 OS 级别（使用代理或群集技术）或使用保护产品在 VM 级别，可以使用应用程序以本机方式处理故障转移。

### <a name="high-availabilityautomatic-failover"></a>高可用性/自动故障转移

以本机方式支持高可用性或依赖群集软件实现跨节点的高可用性的应用程序，可以部署在一组 Azure Stack 集线器中的一组 Vm 上，也可以部署在多个 Azure Stack 中心实例上。 在所有情况下，需要进行一定程度的负载平衡，以确保正确路由应用程序流量。 在此配置中，应用程序可以从故障中自动恢复。 对于本地硬件故障，Azure Stack 集线器基础结构实现物理基础结构中的高可用性和容错能力。 对于计算级别错误，Azure Stack 集线器在 N-1 配置中使用缩放单位中的多个节点。 在 VM 级别，可用性和规模集将缩放单位中的每个节点建模为容错域，以保证节点级的反相似性，使节点故障不会关闭分布式应用程序。

### <a name="no-protection"></a>无保护

某些应用程序可能没有需要保存的数据。 例如，用于开发和测试的 Vm 通常 \' 不需要恢复。 另一个示例是无状态应用程序，可以在发生故障时从 CI/CD 管道重新部署。 务必要确定不需要保护的应用程序，以免不必要地保护 Vm。

<!-- ## Recommended topologies

Important considerations for your Azure Stack deployment: -->

## <a name="next-steps"></a>后续步骤

本文提供了用于保护 Azure Stack 上部署的用户 VM 的一般准则。 有关使用 Azure 服务保护用户 VM 的信息，请参阅：

- [业务连续性和灾难恢复的注意事项](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="partner-products"></a>合作伙伴产品

- [Azure Stack Datacenter 集成合作伙伴生态系统数据表](https://aka.ms/azurestackbcdrpartners)
- 若要详细了解在 Azure Stack 上提供 VM 保护的合作伙伴产品，请参阅 [在 Azure Stack 上保护应用和数据](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/)。
