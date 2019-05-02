---
title: 在 Azure Stack 中添加公共 IP 地址 | Microsoft Docs
description: 了解如何向 Azure Stack 添加更多的公共 IP 地址。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2019
ms.author: jeffgilb
ms.reviewer: scottnap
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 3697dd623f7531491cac7e8f7d8a327de0c02af7
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986092"
---
# <a name="add-public-ip-addresses"></a>添加公共 IP 地址
*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*  

了解如何向 Azure Stack 添加更多的公共 IP 地址。  在本文中，我们将外部地址称为公共 IP 地址，但在 Azure Stack 中，这是指将 IP 地址块添加到外部网络。  该外部网络是否是公共 Internet 可路由或位于 Intranet 上并使用专用地址空间实际上并不重要的这篇文章的目的。  步骤是一样的。 

## <a name="add-a-public-ip-address-pool"></a>添加公共 IP 地址池
在完成 Azure Stack 系统的初始部署以后，可以随时向 Azure Stack 系统添加公共 IP 地址。 了解如何[查看公共 IP 地址使用情况](azure-stack-viewing-public-ip-address-consumption.md)，确定 Azure Stack 上的当前使用情况和公共 IP 地址可用性。

概括说来，向 Azure Stack 添加新公共 IP 地址块的过程如下所示：

 ![添加 IP 流](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>从提供程序处获取地址块
将需要执行的第一个操作是获取你想要添加到 Azure Stack 的地址块。  具体取决于从何处获取你地址块，将需要考虑的提前期是什么，并对在其中使用 Azure Stack 中的公共 IP 地址的速率对此进行管理。  

> [!IMPORTANT]
> Azure Stack 会接受你提供的任何地址块，只要它是有效的地址块并且不与 Azure Stack 中现有的地址范围重叠即可。  请确保获取可路由的有效地址块，并且该地址块不与 Azure Stack 连接到的外部网络重叠。  向 Azure Stack 添加该范围以后，就不能删除它。

## <a name="add-the-ip-address-range-to-azure-stack"></a>向 Azure Stack 添加 IP 地址范围

1. 在 Internet 浏览器中，导航到管理门户仪表板。  对于此示例中，我们将使用 https://adminportal.local.azurestack.external。  
2.  以云操作员的身份登录到 Azure Stack 管理门户。
3.  默认仪表板上找到区域管理列表并选择你想要管理，对于此示例中，本地的区域。
4.  找到“资源提供程序”磁贴，单击“网络资源提供程序”。
5.  单击“公共 IP 池用量”磁贴。
6.  单击“添加 IP 池”按钮。
7.  提供 IP 池的名称。  选择一个名称只是为了让你可以轻松地标识 IP 池，因此可以随意命名。  很好的做法，以使名称相同的地址范围，但不是必需。
8.   以 CIDR 表示法输入要添加的地址块。  例如：192.168.203.0/24
9.  在“地址范围(CIDR 块)”字段中提供有效的 CIDR 范围以后，“开始 IP 地址”、“结束 IP 地址”和“可用 IP 地址”字段会自动进行填充。  它们是只读的因此您无法更改它们而无需修改地址范围字段中的值自动生成。
10. 查看边栏选项卡上的信息并确认一切正确以后，请单击“确定”，以便提交所做的更改并将地址范围添加到 Azure Stack。


## <a name="next-steps"></a>后续步骤 
[查看缩放单元节点操作](azure-stack-node-actions.md) 
