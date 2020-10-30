---
title: 如何在 Azure Stack Hub 资源提供程序上为 IoT 中心旋转机密
description: 了解如何在 Azure Stack Hub 资源提供程序上为 IoT 中心旋转机密
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/20/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: f648aaf0b8a8d90ca8f4789cb6dd58bf5c9fb8c9
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93049666"
---
# <a name="how-to-rotate-secrets-for-iot-hub-on-azure-stack-hub"></a>如何在 Azure Stack 中心为 IoT 中心轮换机密

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

本文将演示如何轮换 IoT 中心资源提供程序使用的机密。

## <a name="overview-and-prerequisites"></a>概述与先决条件

[!INCLUDE [prereqs](../includes/resource-provider-va-rotate-secrets-prereqs.md)]

## <a name="prepare-a-new-tls-certificate"></a>准备新的 TLS 证书

[!INCLUDE [prepare](../includes/resource-provider-va-rotate-secrets-prepare.md)]

## <a name="rotate-secrets"></a>轮换机密

[!INCLUDE [rotate](../includes/resource-provider-va-rotate-secrets-rotate.md)]

## <a name="troubleshooting"></a>疑难解答

机密旋转应成功完成，且不发生错误。 如果你在管理员门户中遇到以下任何情况，请 [打开支持请求](azure-stack-manage-basics.md#where-to-get-support) 以获取帮助：

   - 身份验证问题，包括连接到 IoT 中心资源提供程序时出现的问题。
   - 无法升级资源提供程序或编辑配置参数。
   - 未显示使用情况指标。
   - 未生成帐单。
   - 备份未发生。

## <a name="next-steps"></a>后续步骤

若要详细了解如何旋转 Azure Stack 中心基础结构机密，请访问 [Azure Stack 中心的轮换机密](azure-stack-rotate-secrets.md)。