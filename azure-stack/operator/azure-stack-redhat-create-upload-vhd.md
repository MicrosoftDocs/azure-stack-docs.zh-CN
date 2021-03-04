---
title: 为 Azure Stack 中心提供基于 Red Hat 的虚拟机
titleSuffix: Azure Stack Hub
description: 了解如何创建和上传包含 Red Hat Linux 操作系统的 Azure 虚拟硬盘 (VHD)。
author: sethmanheim
ms.topic: article
ms.date: 2/18/2021
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 2/18/2021
ms.openlocfilehash: 4dc1280b0120c5c64dff6a273ce13ae97dbe980c
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840297"
---
# <a name="offer-a-red-hat-based-virtual-machine-for-azure-stack-hub"></a>为 Azure Stack 中心提供基于 Red Hat 的虚拟机

本文介绍如何准备 Red Hat Enterprise Linux 虚拟机 (VM) 以便在 Azure Stack 中心使用。 

## <a name="offer-a-red-hat-based-vm"></a>提供基于 Red Hat 的虚拟机

可以通过两种方式在 Azure Stack 中心提供基于 Red Hat 的 VM：

- 可以通过 Azure Stack 中心市场添加提供计算机。
    - Red Hat Enterprise Linux 映像是 Azure Stack 中心的专用产品/服务。 要使此产品可用于 " **Marketplace 管理** " 选项卡，你将需要 [完成调查](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbR_e32WQju3tMrgXNcUR94AVUNkJTWjdQRjc3TzFLREdGU0dIVFRUQ1JCSi4u)。
    - 有关详细信息，请参阅 [Azure Stack 中心市场概述](azure-stack-marketplace.md)。
- 你可以将自己的自定义添加到 Azure Stack 中心，然后在 Marketplace 中提供映像。 
    1. 你将需要具有 Red Hat 云访问权限。
    2. 有关为 Azure 和 Azure Stack 中心准备映像的说明，请参阅 [为 Azure 准备基于 Red Hat 的虚拟机](/azure/virtual-machines/linux/redhat-create-upload-vhd)。
    3. 有关在 Azure Stack Hub Marketplace 中提供自定义映像的说明，请参阅 [创建和发布 Marketplace 项](azure-stack-create-and-publish-marketplace-item.md)。

## <a name="next-steps"></a>后续步骤

有关经认证可运行 Red Hat Enterprise Linux 的虚拟机监控程序的详细信息，请参阅 [Red Hat 网站](https://access.redhat.com/certified-hypervisors)。