---
title: 如何在 Azure Stack Hub 资源提供程序上轮换事件中心的机密
description: 了解如何在 Azure Stack Hub 资源提供程序上轮换事件中心的机密
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/15/2020
ms.reviewer: jfggdl
ms.lastreviewed: 10/15/2020
ms.openlocfilehash: fe238576814d1aae5d7469e9438f8921f912f0ea
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97349427"
---
# <a name="how-to-rotate-secrets-for-event-hubs-on-azure-stack-hub"></a>如何在 Azure Stack 中心旋转事件中心的机密

本文将演示如何轮换事件中心资源提供程序使用的机密。

## <a name="overview-and-prerequisites"></a>概述与先决条件

[!INCLUDE [prereqs](../includes/resource-provider-va-rotate-secrets-prereqs.md)]

## <a name="prepare-a-new-tls-certificate"></a>准备新的 TLS 证书

[!INCLUDE [prepare](../includes/resource-provider-va-rotate-secrets-prepare.md)]

## <a name="rotate-secrets"></a>轮换机密

[!INCLUDE [rotate](../includes/resource-provider-va-rotate-secrets-rotate.md)]

## <a name="troubleshooting"></a>疑难解答

机密轮换应成功完成，且不发生错误。 如果在管理员门户中遇到以下任何情况，可以[提交支持请求](azure-stack-manage-basics.md#where-to-get-support)获取帮助：

   - 身份验证问题，包括连接到事件中心资源提供程序时出现的问题。
   - 无法升级资源提供程序或编辑配置参数。
   - 未显示使用情况指标。
   - 未生成帐单。
   - 没有发生备份。

## <a name="next-steps"></a>后续步骤

若要详细了解如何轮换 Azure Stack Hub 基础结构机密，请访问[在 Azure Stack Hub 中轮换机密](azure-stack-rotate-secrets.md)。