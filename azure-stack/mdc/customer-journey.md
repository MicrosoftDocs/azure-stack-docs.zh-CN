---
title: 从购买到 Azure Stack 中心的客户旅程后期部署 |Microsoft Docs
description: 从规划到后期部署，了解如何成功地现场部署模块化数据中心 (MDC) 。
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
ms.openlocfilehash: 12631a118600e67611294c87636cd2fa62bfaaea
ms.sourcegitcommit: ecd98662194d2cdb15c22f8b1f99812fc5f4c15a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2020
ms.locfileid: "93344773"
---
# <a name="modular-data-center-integration-overview"></a>模块化数据中心集成概述

本文介绍了从购买到后期部署的 MDC 集成的端到端过程。 集成是客户与 Microsoft 之间的协作项目。 以下各节介绍了项目时间线的不同阶段以及项目成员的特定步骤。

## <a name="introduction"></a>简介

下表列出了部署的各个阶段所需的内容。

|   |订单流程  |预先部署 |集成，验证，传输 |现场部署  |部署之后 |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
|Microsoft  |-发送到美国位置的信号    |提供所需的工具和文档来收集数据中心需求  |-验证配置项目并检查验证结果<br>-确保提供硬件  |-机架和堆栈<br>-网络集成<br>-Azure Stack 中心部署<br>-移交给客户    |注册和 Marketplace 联合|
|客户   |通知购买   |-在部署工作表中填写网络详细信息<br>-收集证书<br>-获取 Azure AD 帐户<br>-运行提供的任何验证工具    |确保站点已准备好网络、电源和冷却系统必备组件    |-准备好部署配置项目<br>-客户的网络工程师可用   |     |


## <a name="order-process"></a>订购流程

你的组织将与 Microsoft 合作，以便为分配的系统数设置订单。 下订单后，Microsoft 会将 MDC 交付到美国位置。 Microsoft 将确保满足所有安全供应链要求。 

## <a name="hardware-delivery"></a>硬件交付

Microsoft 将与你合作，以确保所有必需的硬件在给定的分配时间内到达美国位置。  

**重要** 的是，所有先决条件数据都已锁定，并 *在现场 Microsoft 部署工程师到达部署解决方案之前可用。*

- 部署工作表填写了所有数据。 
- 所有证书都必须经过验证并准备就绪。
- 必须确定区域名。
- 所有网络集成参数均已完成。

>[!Tip]
>如果此信息中有任何信息发生了变化，请确保与您的内部组织合作，确保在现场部署工程师到达之前更新信息。 这会阻止部署过程中出现任何延迟。

## <a name="pre-deployment"></a>部署前

决定如何将 Azure Stack Hub 集成到数据中心。 Microsoft 发布了一个 [部署工作表](../operator/azure-stack-deployment-worksheet.md) ，指导您收集成功集成到数据中心所需的所有必要信息。 此外，在部署时需要一组特定的证书。 为了帮助你获取这些证书，Microsoft 为你提供了一个称为 [Azure Stack 中心就绪检查](../operator/azure-stack-validation-report.md)程序的工具。 此工具将帮助你创建 (Csr) 提供给内部 CA 的证书签名请求。 

>[!Important]
>验证所有先决条件，以帮助防止部署延迟。 验证先决条件可能需要一些时间，并且需要组织中的不同部门进行协调和数据收集。

需选择以下项：

- **Azure Stack 中心连接模型和标识提供者。** 你可以选择将 [连接到 internet (Azure Stack 集线器部署到 Azure) ](../operator/azure-stack-connected-deployment.md) 或 [断开](../operator/azure-stack-disconnected-deployment.md)连接。 若要从 Azure Stack Hub（包括混合方案）获得最大效益，建议在连接到 Azure 时进行部署。 选择 Active Directory 联合身份验证服务 (AD FS) 还是 Azure Active Directory (Azure AD) 是在部署时必须进行的一次性决策。 **你无法在以后更改标识提供程序，而无需重新部署整个系统。**
- **网络集成。** [网络集成](../operator/azure-stack-network.md)对于部署、操作和管理 Azure Stack Hub 系统至关重要。 需要考虑到多种因素才能确保 Azure Stack Hub 解决方案具有复原能力，并提供一个高可用性物理基础设施来支持其操作。
- **防火墙集成。** 建议[使用防火墙](../operator/azure-stack-firewall.md)来帮助保护 Azure Stack Hub。 防火墙有助于防止 DDOS 攻击，以及执行入侵检测和内容检查。 但应注意，它可能成为 Azure 存储服务的吞吐量瓶颈。
- **证书要求。** 必须在现场工程师抵达数据中心进行部署之前准备好全部[所需的证书](../operator/azure-stack-pki-certs.md)。

通过 "部署" 工作表收集所有先决条件信息后，Microsoft 将确保验证所有验证工具是否已运行，并协助您可能遇到的任何问题。 

## <a name="onsite-deployment"></a>现场部署

若要部署 Azure Stack 中心，可以使用 Microsoft 部署工程师来启动部署。 我们要求你的组织中的网络工程师也可以在现场部署期间使用。

以下检查是部署体验期间现场工程师应执行的操作：

- 硬件的取消装箱和清点
- 连接解决方案的电源和电源
- 验证物理硬件运行状况
- 检查所有布线和边界连接，确保解决方案正确定位在一起，且符合要求
- 配置解决方案的 HLH（硬件生命周期主机）
- 数据中心网络集成
- 检查以确保所有物理硬件设置正确
- 请确保解决方案中的所有组件都处于最新批准版本
- 开始部署

## <a name="post-deployment"></a>后期部署

在将解决方案移交给客户之前，必须先执行几个步骤。 在此阶段，验证非常重要，它可以确保系统部署正确且运行正常。

Microsoft 部署工程师应执行的操作：

- 启用增值资源提供程序 (RPs) 
- 运行 [test-azurestack](../operator/azure-stack-diagnostic-test.md)
- [注册](../operator/azure-stack-registration-role.md) Azure
- [市场联合](../operator/azure-stack-marketplace.md)
- 备份交换机配置和 HLH 配置文件
- 为客户准备部署摘要
- [检查更新](../operator/azure-stack-updates.md) 以确保解决方案软件已更新到最新版本

## <a name="next-steps"></a>后续步骤

[模块化数据中心部署概述](deployment-overview.md)。

