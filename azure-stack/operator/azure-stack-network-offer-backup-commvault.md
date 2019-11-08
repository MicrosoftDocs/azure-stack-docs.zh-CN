---
title: 将 Commvault 添加到 Azure Stack Marketplace |Microsoft Docs
description: 了解如何将 Commvault 添加到 Azure Stack Marketplace。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 787453550e9a8ed69aa9dfda0a03ea5e57c49d7a
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802324"
---
# <a name="add-commvault-to-the-azure-stack-marketplace"></a>将 Commvault 添加到 Azure Stack Marketplace

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*

本文逐步讲解如何提供 Commvault 实时同步，以更新位于单独 Azure Stack 缩放单位上的恢复 VM。 你可以下载 Commvault，并为用户提供备份和复制解决方案。 

## <a name="notes-for-commvault"></a>Commvault 说明

- 用户需要在其源 Azure Stack 订阅中的 VM 上安装备份和复制软件。 Azure Site Recovery 和 Azure 备份可以提供一个脱离堆栈位置，用于存储备份和恢复映像。 它们都需要在 Azure 中创建恢复服务保管库，然后再从以下位置下载要安装在 Azure Stack 上的软件映像： [Azure 备份服务器](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409)和[Azure Site Recovery](https://aka.ms/unifiedinstaller_eus)。  
    
- 你可能需要第三方软件的许可证（如果选择）。
- 用户可能需要在备份和复制主机上通过虚拟网络网关（VPN）或公共 IP 连接其源和目标。
- 针对恢复目标 Azure Stack 的 Azure 云订阅或订阅。
- 恢复目标 Azure Stack 上的目标资源组和 Blob 存储帐户。
- 某些解决方案要求您在目标订阅中创建需要24x7x365 运行的虚拟机，以便从源服务器接收更改。 在 "[使用 Commvault 在 Azure Stack 上备份 VM](../user/azure-stack-network-howto-backup-commvault.md)" 中，Commvault 实时同步会在初始配置期间创建目标恢复 vm，并使其保持空闲（不运行，不收费），直到需要在复制周期内应用更改。


## <a name="get-commvault-for-your-marketplace"></a>为 Marketplace 获取 Commvault

1. 打开 Azure Stack 管理门户。
2. 选择 " **Marketplace 管理**" > **从 Azure 添加**"。

    ![用于 Azure Stack 的 Commvault](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. 输入 `commvault` 。
4. 选择**Commvault 试用版**。 然后选择 "**下载**"。


## <a name="next-steps"></a>后续步骤

[通过 Commvault 在 Azure Stack 上备份 VM](../user/azure-stack-network-howto-backup-commvault.md)

[在 Azure Stack 中提供服务概述](service-plan-offer-subscription-overview.md)
