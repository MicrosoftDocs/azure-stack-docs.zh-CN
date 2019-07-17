---
title: Azure Stack 的混合云设计模式构建解决方案 |Microsoft Docs
description: 了解如何使用 Azure 和 Azure Stack 创建混合云应用
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 538a626b2e89d15aa4b816674dbb8c374ec4a262
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286809"
---
#  <a name="build-solutions-hybrid-cloud-design-patterns-for-azure-stack"></a>Azure Stack 的混合云设计模式构建解决方案

Microsoft Azure Stack 是 Azure 的扩展。 Azure Stack 通过启用混合云为本地环境和 Edge 带来云计算的灵活性。 你可以在 Azure 中构建混合云应用，并将其部署到位于任何位置的已连接或已断开连接的数据中心。

Microsoft Azure 是一致的混合云。 Azure 允许你重用在 Azure 中开发的代码，并将你的应用部署到 Azure 主权云和 Azure Stack。 跨云的应用程序也称为*混合应用程序*。

混合方案因可用于开发的资源而有很大的不同，并且涉及地理、安全性、Internet 访问等考虑因素。 尽管这些方案不能满足你的特定需求，但它们可以为你提供一些重要的准则和示例，以帮助你探索实现混合解决方案。

## <a name="hybrid-cloud-patterns"></a>混合云模式

- [跨云缩放模式](azure-stack-edge-pattern-cross-cloud-scaling.md)
- [异地分布式模式](azure-stack-edge-pattern-geo-distribution.md)
- [DevOps 模式](azure-stack-edge-pattern-hybrid-ci-cd.md)

## <a name="step-by-step-tutorials"></a>分步教程

- [将应用部署在 Azure 和 Azure Stack](azure-stack-solution-pipeline.md)
- [将应用部署到 Azure Stack 和 Azure](azure-stack-solution-hybrid-identity.md)
- [使用 Azure 和 Azure Stack 应用配置混合云标识](azure-stack-solution-hybrid-connectivity.md)
- [使用 Azure 和 Azure Stack 配置混合云连接](azure-stack-solution-staged-data-analytics.md)
- [使用 Azure 和 Azure Stack 创建临时数据分析解决方案](azure-stack-solution-staged-data.md)
- [使用 Azure 创建跨云缩放解决方案](azure-stack-solution-cloud-burst.md)
- [使用 Azure 和 Azure Stack 创建异地分布式应用解决方案](azure-stack-solution-geo-distributed.md)
- [使用 Azure 和 Azure Stack 部署混合云解决方案](azure-stack-solution-hybrid-cloud.md)
- [部署 MongoDB 在 Azure 和 Azure Stack](azure-stack-solution-mongodb-ha.md)
- [部署 SQL Server 2016 中 Azure 和 Azure Stack](azure-stack-solution-sql-ha.md)


## <a name="next-steps"></a>后续步骤

- 阅读有关[混合应用程序的设计注意事项](azure-stack-edge-pattern-overview.md)若要查看用于设计、 部署和操作混合应用程序的软件质量的构成要素。
- [在 Azure Stack 上设置开发环境](azure-stack-dev-start.md)和在 Azure Stack 上[部署第一个应用](azure-stack-dev-start-deploy-app.md)。
