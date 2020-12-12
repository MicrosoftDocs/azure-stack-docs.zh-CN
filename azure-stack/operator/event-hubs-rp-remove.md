---
title: 如何删除 Azure Stack Hub 上的事件中心
description: 了解如何删除 Azure Stack Hub 上的事件中心资源提供程序。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: b69d5b7a7b621d22745c89c18a3e7f000e54bcbd
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2020
ms.locfileid: "97343739"
---
# <a name="how-to-remove-event-hubs-on-azure-stack-hub"></a>如何删除 Azure Stack Hub 上的事件中心

> [!WARNING]
> 卸载事件中心会删除（擦除）资源提供程序以及所有用户创建的事件中心群集、命名空间和事件中心资源。 这还会删除其关联的事件数据。  
> 在决定卸载事件中心之前，请先格外小心地进行相关处理。 卸载事件中心不会删除用于安装事件中心的包。 若要实现该操作，请参阅[删除事件中心包](#delete-event-hubs-packages)。

## <a name="uninstall-event-hubs"></a>卸载事件中心

这一系列的步骤会删除所有事件中心资源（包括群集、命名空间、事件中心和资源提供程序）。

若要删除用户创建的事件中心和所有相关资源，请完成以下步骤：

1. 登录到 Azure Stack Hub 管理员门户。
2. 选择左侧的“市场管理”。
3. 选择“资源提供程序”。
4. 从资源提供程序列表中选择“事件中心”。 建议你通过在提供的“搜索”文本框中输入“事件中心”来筛选列表。

   [![删除事件中心 1](media/event-hubs-rp-remove/1-uninstall.png)](media/event-hubs-rp-remove/1-uninstall.png#lightbox)

5. 从页面顶部提供的选项中选择“卸载”。

   [![删除事件中心 2](media/event-hubs-rp-remove/2-uninstall.png)](media/event-hubs-rp-remove/2-uninstall.png#lightbox)

6. 输入资源提供程序的名称，然后选择“卸载”。 此操作确认你要卸载以下内容：
   - 事件中心资源提供程序
   - 所有用户创建的群集、命名空间、事件中心和事件数据。

   [![删除事件中心 3](media/event-hubs-rp-remove/3-uninstall.png)](media/event-hubs-rp-remove/3-uninstall.png#lightbox)

   [![删除事件中心 4](media/event-hubs-rp-remove/4-uninstall.png)](media/event-hubs-rp-remove/4-uninstall.png#lightbox)

   > [!IMPORTANT]
   > 在成功删除事件中心之后，必须至少等待 10 分钟，然后才能再次安装事件中心。 这是因为清理活动可能仍在运行，而这可能与任何新安装冲突。

## <a name="delete-event-hubs-packages"></a>删除事件中心包

如果卸载事件中心后还希望删除用于安装事件中心的任何包，请使用此选项。 

## <a name="next-steps"></a>后续步骤

若要重新进行安装，请返回到[安装事件中心资源提供程序](event-hubs-rp-install.md)一文。