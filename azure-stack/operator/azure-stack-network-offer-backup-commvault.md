---
title: 将 Commvault 添加到 Azure Stack 中心市场
description: 了解如何将 Commvault 添加到 Azure Stack 中心市场。
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: d2d12e032e639cb9b272affb4beed3a3a439b2cb
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881702"
---
# <a name="add-commvault-to-the-azure-stack-hub-marketplace"></a>将 Commvault 添加到 Azure Stack 中心市场

本文逐步讲解如何提供 Commvault 实时同步，以更新位于单独 Azure Stack 集线器缩放单位上的恢复 VM。 你可以下载 Commvault，并为用户提供备份和复制解决方案。 

## <a name="notes-for-commvault"></a>Commvault 说明

- 用户需要在其源 Azure Stack 中心订阅中的 VM 上安装备份和复制软件。 Azure Site Recovery 和 Azure 备份可以提供一个脱离堆栈位置，用于存储备份和恢复映像。 它们都需要在 Azure 中创建恢复服务保管库，然后再从以下位置下载要安装在 Azure Stack 集线器上的软件映像： [Azure 备份服务器](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409)和[Azure Site Recovery](https://aka.ms/unifiedinstaller_eus)。  
    
- 你可能需要第三方软件的许可证（如果选择）。
- 用户可能需要在备份和复制主机上通过虚拟网络网关（VPN）或公共 IP 连接其源和目标。
- 针对恢复目标 Azure Stack 集线器上的 Azure 云订阅或订阅。
- 恢复目标上的目标资源组和 Blob 存储帐户 Azure Stack 中心。
- 某些解决方案要求您在目标订阅中创建需要24x7x365 运行的虚拟机，以便从源服务器接收更改。 在 "[使用 Commvault 在 Azure Stack 集线器上备份 VM](../user/azure-stack-network-howto-backup-commvault.md)" 中，Commvault 实时同步会在初始配置期间创建目标恢复 vm，并使其保持空闲（不运行，不收费），直到需要在复制周期内应用更改。


## <a name="get-commvault-for-your-marketplace"></a>为 Marketplace 获取 Commvault

1. 打开 Azure Stack 集线器管理门户。
2. 选择 " **Marketplace 管理**" > **从 Azure 添加**"。

    ![Azure Stack 中心的 Commvault](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. 输入 `commvault`。
4. 选择**Commvault 试用版**。 然后选择 "**下载**"。


## <a name="next-steps"></a>后续步骤

[通过 Commvault 在 Azure Stack 集线器上备份 VM](../user/azure-stack-network-howto-backup-commvault.md)

[Azure Stack 中心提供服务的概述](service-plan-offer-subscription-overview.md)
