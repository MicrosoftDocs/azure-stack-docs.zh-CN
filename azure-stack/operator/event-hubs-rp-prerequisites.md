---
title: 在 Azure Stack 集线器上安装事件中心的先决条件
description: 在 Azure Stack 集线器上安装事件中心资源提供程序之前，请了解所需的必备组件。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 7fd04e0952810bd4080d9c4431589cd53ccd0536
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80423989"
---
# <a name="prerequisites-for-installing-event-hubs-on-azure-stack-hub"></a>在 Azure Stack 集线器上安装事件中心的先决条件

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

必须先完成以下先决条件，然后才能在 Azure Stack 集线器上安装事件中心。 完成所有步骤**可能需要几天或几周的提前期**。

> [!IMPORTANT]
> 这些先决条件假设已至少部署了4节点 Azure Stack 集线器集成系统。 Azure Stack 开发工具包（ASDK）不支持事件中心资源提供程序。

> [!IMPORTANT]
> 事件中心需要 Azure Stack 集线器2002内部版本或更高版本。 请注意，Azure Stack 集线器生成是递增的。 例如，如果安装了[版本 1908](/azure-stack/operator/release-notes?view=azs-1908#1908-build-reference) ，则必须先升级到[1910](/azure-stack/operator/release-notes?view=azs-1910#1910-build-reference)，然后再升级到2002。 也就是说，不能在内部跳过生成。

## <a name="common-prerequisites"></a>常见的先决条件

[!INCLUDE [Common RP prerequisites](../includes/marketplace-resource-provider-prerequisites.md)]

## <a name="event-hubs-prerequisites"></a>事件中心先决条件

1. 为事件中心购买公钥基础结构（PKI） SSL 证书。 使用者备用名称（SAN）必须遵循以下命名模式： `CN=*.eventhub.<region>.<fqdn>`。 可以指定使用者名称，但在处理证书时事件中心不会使用此名称。 仅使用使用者备用名称。 有关详细要求的完整列表，请参阅[PKI 证书要求](azure-stack-pki-certs.md)。  

   ![示例证书](media/event-hubs-rp-prerequisites/certificate-example.png)

   > [!NOTE]
   > **PFX 文件必须受密码保护**。 稍后将在安装过程中请求密码。

2. 务必查看[验证证书](azure-stack-validate-pki-certs.md)。 本文介绍如何准备和验证用于事件中心资源提供程序的证书。 

## <a name="next-steps"></a>后续步骤

接下来，[安装事件中心资源提供程序](event-hubs-rp-install.md)。
