---
title: Azure Stack 操作清单 |Microsoft Docs
description: 了解 Azure Stack 运算符需要执行的常见任务以及执行这些任务的频率。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 10/07/2019
ms.openlocfilehash: 246fdc867080da1816f00c9fb6a68179b6944275
ms.sourcegitcommit: 12034a1190d52ca2c7d3f05c8c096416120d8392
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038192"
---
# <a name="azure-stack-operators-checklist"></a>Azure Stack 操作员的清单

我们将一些操作清单组合在一起，以便 Azure Stack 的操作员取得成功。 你可以添加需要为你的环境完成的特定操作。 它们旨在帮助作为操作员加入的人员了解每日、每周和每月要执行的操作。 

## <a name="routine-daily-weekly-and-monthly-operations"></a>日常日常操作、每周和每月操作

Azure Stack 运算符需要定期执行以下操作： 

> [!div class="checklist"]
> * 监视和修正[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)和[Systen Center](https://docs.microsoft.com/system-center/scom/welcome)中的错误每日 Operations Manager 两次
> * 每日运行[test-azurestack](azure-stack-diagnostic-test.md)一次
> * 报表[Azure Stack 每周使用情况](azure-stack-usage-reporting.md) 
> * 每两个月更新一次[oem 固件](azure-stack-update-oem.md)或由 oem 通知
> * 每月准备[Azure Stack 更新](release-notes-checklist.md)

## <a name="day-to-day-operations-as-needed"></a>根据需要进行日常操作

在日常工作中，Azure Stack 运算符还需要完成以下操作： 

> [!div class="checklist"]
> * 创建[计划](azure-stack-create-plan.md)
> * 创建[产品](azure-stack-create-offer.md)
> * 创建[订阅](azure-stack-subscribe-plan-provision-vm.md)
> * 更新[配额](azure-stack-quota-types.md)
> * 发布[marketplace 示例](azure-stack-create-and-publish-marketplace-item.md)
> * 添加[自定义虚拟机映像](azure-stack-add-vm-image.md)
> * 提供[虚拟机规模集](azure-stack-compute-add-scalesets.md) 
> * 应用[marketplace 更新](azure-stack-marketplace-changes.md)
> * 配置[遥测](azure-stack-telemetry.md)
> * 查看[区域管理](azure-stack-region-management.md)
> * 关闭或[重新启动 Azure Stack 服务](azure-stack-start-and-stop.md) 
> * 替换[缩放单位节点](azure-stack-replace-node.md)
> * 替换[物理磁盘](azure-stack-replace-disk.md)
> * 替换[硬件组件](azure-stack-replace-component.md)
> * 更新加载项资源提供程序（例如[SQL](azure-stack-sql-resource-provider-update.md)、 [MySQL](azure-stack-mysql-resource-provider-update.md)和[应用服务](azure-stack-app-service-update.md)）
> * 运行[诊断日志收集](azure-stack-diagnostic-log-collection-overview.md)  



<!---Ask Jeff, Brian, is this everything you do, how can we make it more useful? Theebs has another user.

To be successful, do these things

ask Brian what are all the the things they need to write down for quick access, like passswords, IP addresses, and so on, to make them more efficient

have a password manager for azure-stack, pep, inventory

A checklist so operators can be successful, so someone onboarding is operator know what to do weekly, daily, monthly. --->


