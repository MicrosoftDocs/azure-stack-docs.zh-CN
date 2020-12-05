---
title: 从购买到 Azure Stack 集线器的客户旅程后期部署 |Microsoft Docs
description: 了解从规划部署到后期部署，成功部署 Azure 模块化数据中心所需的内容， (MDC) 。
services: azure-stack
documentationcenter: ''
author: ashika789
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 11/04/2020
ms.openlocfilehash: 2d65f96cb7e64b22949150a246326d620ea66f56
ms.sourcegitcommit: 0efffe1d04a54062a26d5c6ce31a417f511b9dbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2020
ms.locfileid: "96615479"
---
# <a name="modular-datacenter-integration-overview"></a>模块化数据中心集成概述

本文介绍 Azure 模块式 Datacenter (MDC) 集成的端到端过程，从购买到后期部署。 集成是客户与 Microsoft 之间的协作项目。 以下部分介绍项目时间线的不同阶段以及项目成员的特定步骤。

## <a name="introduction"></a>简介

下表列出了部署的各个阶段所需的内容。

| 参与者 |订购流程 |部署前 |集成，验证，传输 |现场部署 |后期部署 |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
|Microsoft  | 传递给 US 位置的信号。    |提供所需的工具和文档来收集数据中心需求。 |-验证配置项目并检查验证结果。<br>-确保已提供硬件。    |-机架和堆栈。<br>-网络集成。<br>-Azure Stack 中心部署。<br>-交付给客户。    |注册和 Azure Stack 集线器 Marketplace 联合。|
|客户   |采购信号。   |-在部署工作表中填写网络详细信息。<br>-收集证书。<br>-获取 Azure Active Directory (Azure AD) 帐户。<br>-运行提供的任何验证工具。   |确保站点已准备好网络、电源和冷却系统必备组件。   |-准备好部署配置项目。<br>-确保客户的网络工程师可用。   |     |


## <a name="order-process"></a>订购流程

你的组织将与 Microsoft 合作，以便为分配的系统数设置订单。 订购订单后，Microsoft 会将 MDC 交付到美国位置。 Microsoft 将确保满足所有安全供应链要求。

## <a name="hardware-delivery"></a>硬件交付

Microsoft 将与你合作，以确保所有必需的硬件在给定的分配时间内到达美国位置。

*重要* 的是，所有先决条件数据都已锁定，并 *在现场 Microsoft 部署工程师到达部署解决方案之前可用。*

- 部署工作表填写了所有数据。
- 必须验证并准备好所有证书。
- 必须确定区域名。
- 已确定和完成所有网络集成参数。

>[!Tip]
>如果上述任何信息发生了更改，请务必与内部组织合作，确保在现场部署工程师到达之前更新信息。 更新信息将会阻止部署过程中出现任何延迟。

## <a name="predeployment"></a>部署前

决定如何将 Azure Stack Hub 集成到数据中心。 Microsoft 发布了[部署工作表](../operator/azure-stack-deployment-worksheet.md)，可指导你收集成功集成到数据中心所需的所有必要信息。 此外，在部署时需要一组特定的证书。 为了帮助你获取这些证书，Microsoft 提供了 [Azure Stack 中心就绪检查](../operator/azure-stack-validation-report.md)程序。 此工具将帮助你创建 (Csr) 提供给内部证书颁发机构的证书签名请求。

>[!Important]
>验证所有先决条件，以帮助防止部署延迟。 验证先决条件可能需要一些时间，并且需要组织中的不同部门进行协调和数据收集。

需选择以下项：

- Azure Stack Hub 连接模型和标识提供者。 可以选择在[已连接到 Internet（和 Azure）](../operator/azure-stack-connected-deployment.md)时或[断开连接](../operator/azure-stack-disconnected-deployment.md)时部署 Azure Stack Hub。 若要从 Azure Stack 中心获得最大优势，包括混合方案，需要将连接到 Azure。 选择 Active Directory 联合身份验证服务 (AD FS) 或 Azure AD 是在部署时必须进行的一次性决定。 以后，除非重新部署整个系统，否则将无法更改标识提供者。
- 网络集成。 [网络集成](../operator/azure-stack-network.md)对于部署、操作和管理 Azure Stack Hub 系统至关重要。 需要考虑到多种因素才能确保 Azure Stack Hub 解决方案具有复原能力，并提供一个高可用性物理基础设施来支持其操作。
- 防火墙集成。 建议 [使用防火墙](../operator/azure-stack-firewall.md) 来帮助保护 Azure Stack 中心的安全。 防火墙可帮助防止 DDoS 攻击、入侵检测和内容检查。 请注意，防火墙可能会成为 Azure 存储服务的吞吐量瓶颈。
- 证书要求。 必须在现场工程师抵达数据中心进行部署之前准备好全部[所需的证书](../operator/azure-stack-pki-certs.md)。

在通过 "部署" 工作表收集所有先决条件信息后，Microsoft 将确保验证所有验证工具是否已运行，并协助你可能遇到的任何问题。

## <a name="onsite-deployment"></a>现场部署

若要部署 Azure Stack 中心，可以使用 Microsoft 部署工程师来启动部署。 我们还需要在现场部署过程中提供组织的网络工程师。

以下检查是部署体验期间现场工程师应执行的操作：

- 取消装箱和清点硬件。
- 接通解决方案的电源和电源。
- 验证物理硬件运行状况。
- 检查所有布线和边界连接，确保解决方案正确定位在一起，且符合要求。
- 配置解决方案硬件生命周期主机 (HLH) 。
- 集成数据中心网络。
- 检查以确保所有物理硬件设置都正确。
- 确保所有组件的固件使用解决方案批准的最新版本。
- 开始部署。

## <a name="post-deployment"></a>后期部署

在将解决方案移交给客户之前，必须先执行几个步骤。 在此阶段，验证非常重要，它可以确保系统部署正确且运行正常。

Microsoft 部署工程师应执行的操作：

- 启用增值资源提供程序。
- 运行 [test-azurestack](../operator/azure-stack-diagnostic-test.md)。
- [注册](../operator/azure-stack-registration-role.md) 到 Azure。
- 确保 [Azure Stack 集线器 Marketplace 联合](../operator/azure-stack-marketplace.md)。
- 备份交换机配置和 HLH 配置文件。
- 为客户准备部署摘要。
- [检查更新](../operator/azure-stack-updates.md) ，确保解决方案软件已更新到最新版本。

## <a name="next-steps"></a>后续步骤

[模块化数据中心部署概述](deployment-overview.md)

