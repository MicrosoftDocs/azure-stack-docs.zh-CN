---
title: 为 Azure Stack 中心提供基于 Red Hat 的虚拟机
titleSuffix: Azure Stack Hub
description: 了解如何创建和上传包含 Red Hat Linux 操作系统的 Azure 虚拟硬盘 (VHD)。
author: sethmanheim
ms.topic: article
ms.date: 3/3/2021
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 3/3/2021
ms.openlocfilehash: 92623d76de9f3358edc1d1ffb1cace199f2f6148
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187276"
---
# <a name="offer-a-red-hat-based-virtual-machine-for-azure-stack-hub"></a>为 Azure Stack 中心提供基于 Red Hat 的虚拟机

本文介绍如何准备 Red Hat Enterprise Linux 虚拟机 (VM) 以便在 Azure Stack 中心使用。 

## <a name="offer-a-red-hat-based-vm"></a>提供基于 Red Hat 的虚拟机

可以通过两种方式在 Azure Stack 中心提供基于 Red Hat 的 VM：

- 可以通过 Azure Stack 中心市场添加提供计算机。
    - 熟悉 [Red Hat 云访问计划](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) 条款。 为 red [Hat 订阅管理器](https://access.redhat.com/management/cloud)的云访问启用 red hat 订阅。 需要手动注册 Azure Stack 中心注册到的 Azure 订阅，才能注册云访问。
    - Red Hat Enterprise Linux 映像是 Azure Stack 中心的专用产品/服务。 要使此产品可用于 " **Marketplace 管理** " 选项卡，你将需要 [完成调查](https://forms.office.com/pages/responsepage.aspx?id=v4j5cvGGr0GRqy180BHbR_e32WQju3tMrgXNcUR94AVUNkJTWjdQRjc3TzFLREdGU0dIVFRUQ1JCSi4u)。 发布调查后，需要7个工作日内才能在 Marketplace 管理内的 " **从 Azure 添加** " 选项卡中查看。
    - 有关详细信息，请参阅 [Azure Stack 中心市场概述](azure-stack-marketplace.md)。
- 你可以将自己的自定义添加到 Azure Stack 中心，然后在 Marketplace 中提供映像。 
    1. 你将需要具有 Red Hat 云访问权限。
    2. 有关为 Azure 和 Azure Stack 中心准备映像的说明，请参阅 [为 Azure 准备基于 Red Hat 的虚拟机](/azure/virtual-machines/linux/redhat-create-upload-vhd)。
    3. 有关在 Azure Stack Hub Marketplace 中提供自定义映像的说明，请参阅 [创建和发布 Marketplace 项](azure-stack-create-and-publish-marketplace-item.md)。

## <a name="next-steps"></a>后续步骤

有关经认证可运行 Red Hat Enterprise Linux 的虚拟机监控程序的详细信息，请参阅 [Red Hat 网站](https://access.redhat.com/certified-hypervisors)。