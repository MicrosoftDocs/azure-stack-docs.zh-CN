---
title: Azure Stack 集线器集成系统连接模型
description: 确定 Azure Stack 集线器集成系统的连接模型和其他部署规划决策。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 620e88bcf20f9744ab91d2450da2a8570369cc14
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77695183"
---
# <a name="azure-stack-hub-integrated-systems-connection-models"></a>Azure Stack 集线器集成系统连接模型
如果你有兴趣购买 Azure Stack 集线器集成系统，则需要了解 Azure Stack 中心部署的[几个数据中心集成注意事项](azure-stack-datacenter-integration.md)，以确定系统如何适应你的数据中心。 此外，还需要决定将 Azure Stack 集线器集成到混合云环境中的方式。 本文概述了这些重要决策，其中包括 Azure 连接模型、标识存储选项和计费模型选项。

如果决定购买集成系统，原始设备制造商（OEM）硬件供应商将帮助你更详细地指导你完成规划过程。 OEM 硬件供应商还会执行实际部署。

## <a name="choose-an-azure-stack-hub-deployment-connection-model"></a>选择 Azure Stack 中心部署连接模型
你可以选择部署连接到 internet （和到 Azure）或断开连接 Azure Stack 集线器。 部署已连接到 Azure，以便从 Azure Stack 中心获得最大优势，包括 Azure Stack 中心与 Azure 之间的混合方案。 此选项定义了可用于标识存储（Azure Active Directory 或 Active Directory 联合身份验证服务）和计费模型的选项（根据使用计费或基于容量的计费付费），如以下关系图和表中所概括：

![Azure Stack 中心部署和计费方案](media/azure-stack-connection-models/azure-stack-scenarios.png)
  
> [!IMPORTANT]
> 这是关键决策点！ 选择 Active Directory 联合身份验证服务（AD FS）或 Azure Active Directory （Azure AD）是在部署时必须进行的一次决定。 稍后不能更改此项，而无需重新部署整个系统。  


|选项|已连接到 Azure|已与 Azure 断开连接|
|-----|:-----:|:-----:|
|Azure AD|![支持](media/azure-stack-connection-models/check.png)| |
|AD FS|![支持](media/azure-stack-connection-models/check.png)|![支持](media/azure-stack-connection-models/check.png)|
|基于消耗的计费|![支持](media/azure-stack-connection-models/check.png)| |
|基于容量的计费|![支持](media/azure-stack-connection-models/check.png)|![支持](media/azure-stack-connection-models/check.png)|
|授权| 企业协议或云解决方案提供商 | 企业协议 |
|修补和更新|更新包可以直接从 Internet 下载到 Azure Stack 中心 |  必选<br><br>还需要可移动媒体<br> 和单独连接的设备 |
| 注册 | 自动 | 必选<br><br>还需要可移动媒体<br> 和单独连接的设备 |

确定要用于 Azure Stack 中心部署的 Azure 连接模型后，必须为标识存储和计费方法做出其他依赖于连接的决策。

## <a name="next-steps"></a>后续步骤

[Azure 连接 Azure Stack 中心部署决策](azure-stack-connected-deployment.md)

[Azure 断开连接 Azure Stack 中心部署决策](azure-stack-disconnected-deployment.md)
