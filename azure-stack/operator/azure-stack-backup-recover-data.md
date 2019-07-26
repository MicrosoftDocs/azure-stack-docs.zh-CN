---
title: 使用基础结构备份服务在 Azure Stack 中发生灾难性数据丢失后进行恢复 | Microsoft Docs
description: 当灾难性故障导致 Azure Stack 失败后，在重新建立 Azure Stack 部署时可以还原基础结构数据。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: b6b6796f5d47189499e01c94b9c988dbf03091bb
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494003"
---
# <a name="recover-from-catastrophic-data-loss"></a>在发生灾难性数据丢失后进行恢复

*适用于：Azure Stack 集成系统。*

Azure Stack 在数据中心运行 Azure 服务，并且可以在安装在单个机架中的四个节点小的环境中运行。 同时，Azure 也可以在 40 多个地区中的多个数据中心内运行，并且每个地区中可以有多个区域。 用户资源可以跨多个服务器、机架、数据中心和地区。 使用 Azure Stack，当前只能将整个云部署到单个机架。 这会导致你的云有在数据中心内发生灾难性事件的风险，以及由于重大产品 bug 而发生故障的风险。 当灾难发生时，Azure Stack 实例会进入脱机状态。 可能无法恢复所有数据。

根据数据丢失的根本原因，你可能需要修复单个基础结构服务或还原整个 Azure Stack 实例。 你甚至可能需要还原到同一位置或其他位置中的其他硬件。

当私有云的设备和重新部署出现故障时，此方案恢复整个安装。

| 应用场景                                                           | 数据丢失                            | 注意事项                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| 在由于灾难或产品 bug 而发生灾难性数据丢失后进行恢复 | 所有基础结构及用户和应用数据 | 用户应用程序和数据是独立于基础结构数据进行保护的 |

## <a name="workflows"></a>Workflows

保护 Azure Stack 的过程从分别备份基础结构和应用/租户数据开始。 本文档介绍了如何保护基础结构。 

![Azure Stack 的初始部署](media/azure-stack-backup/azure-stack-backup-workflow1.png)

在所有数据均丢失的最差情形方案中，恢复 Azure Stack 是还原与 Azure Stack 部署相关的基础结构数据和所有用户数据的过程。 

![重新部署 Azure Stack](media/azure-stack-backup/azure-stack-backup-workflow2.png)

## <a name="restore"></a>还原

如果发生灾难性数据丢失，但硬件仍然可以使用，则需要重新部署 Azure Stack。 在重新部署期间，可以指定存储位置和访问备份所需的凭据。 在此模式下，不需要指定需要还原的服务。 基础结构备份控制器将控制层状态插入为部署工作流的一部分。

如果发生导致硬件不可用的灾难，则只能在新硬件上重新部署。 因为要订购更换硬件并等待硬件到达数据中心，所以重新部署可能会花费数周时间。 可以在任何时间还原控制层数据。 但是，如果重新部署的实例的版本比上次备份中使用的版本高一个版本，则不支持还原。 

| 部署模式 | 起点 | 终点                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 全新安装   | 基线版本 | OEM 部署 Azure Stack，并更新到最新的受支持版本。                                                                                                                                          |
| 恢复模式   | 基线版本 | OEM 在恢复模式下部署 Azure Stack 并根据可用的最新备份来处理版本匹配要求。 OEM 通过更新到最新的受支持版本来完成部署。 |

## <a name="data-in-backups"></a>备份中的数据

Azure Stack 支持称为云恢复模式的部署类型。 只有当灾难或产品 bug 导致解决方案不可恢复后，你选择恢复 Azure Stack 时才使用此模式。 此部署模式不会恢复解决方案中存储的任何用户数据。 此部署模式的作用域仅限于还原以下数据：

 - 部署输入
 - 内部标识服务数据 (ADFS 部署)
 - 联合识别配置 (ADFS 部署)
 - 内部证书颁发机构使用的根证书
 - Azure 资源管理器配置用户数据，例如订阅、计划、套餐，以及存储、网络和计算资源的配额
 - KeyVault 机密和保管库
 - RBAC 策略分配和角色分配 

在部署期间不会恢复任何用户基础结构即服务 (IaaS) 或平台即服务 (PaaS) 资源。 也就是说，IaaS VM、存储帐户、blob、表、网络配置等等都会丢失。 云恢复的目的是为了确保操作员和用户在部署完成后可以重新登录回门户。 重新登录回来的用户不会看到其任何资源。 用户将还原其订阅以及由管理员定义的原始计划和套餐策略。 重新登录回系统的用户在操作时将受原始解决方案在发生灾难前施加的相同约束制约。 在云恢复完成后，操作员可以手动还原增值 RP 和第三方 RP 以及关联的数据。

## <a name="next-steps"></a>后续步骤

了解[使用基础结构备份服务](azure-stack-backup-best-practices.md)的最佳做法。
