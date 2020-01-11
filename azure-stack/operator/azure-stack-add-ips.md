---
title: 在 Azure Stack Hub 中添加公共 IP 地址 |Microsoft Docs
description: 了解如何将公共 IP 地址添加到 Azure Stack 中心。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: scottnap
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 214fed4c87c6c36b5e290c084efe43032b1407e0
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75881138"
---
# <a name="add-public-ip-addresses"></a>添加公共 IP 地址

本文以公共 IP 地址的形式引用外部地址。 在 Azure Stack 集线器的上下文中，公共 IP 地址是一种可从 Azure Stack 中心外部访问的 IP 地址。 无论此外部网络是公共 internet 可路由还是位于 intranet 上并使用专用地址空间，本文的目的都是相同的，这些步骤是相同的。

> [!IMPORTANT]
> 本文中的步骤仅适用于使用合作伙伴工具包版本1809或更高版本部署的系统。 在版本1809之前部署的系统需要更新机箱顶部（TOR）交换机访问控制列表（Acl），以允许新的公共 VIP 池范围。 如果运行的是较旧的交换机配置，请与 OEM 合作，为新的公共 IP 池添加适当的允许 Acl，或使用最新的合作伙伴工具包重新配置交换机，以防止新的公共 IP 地址被阻止。

## <a name="add-a-public-ip-address-pool"></a>添加公共 IP 地址池
初始部署 Azure Stack 中心系统后，可以随时将公共 IP 地址添加到 Azure Stack 中心系统。 查看如何[查看公共 ip 地址消耗](azure-stack-viewing-public-ip-address-consumption.md)，查看 Azure Stack 中心上当前的使用情况和公共 ip 地址的可用性。

在高级别中，将新的公共 IP 地址块添加到 Azure Stack 集线器的过程如下所示：

 ![添加 IP 流](media/azure-stack-add-ips/flow.PNG)

## <a name="obtain-the-address-block-from-your-provider"></a>从提供程序获取地址块
您需要做的第一件事就是获取要添加到 Azure Stack 集线器的地址块。 根据你从何处获取你的地址块，考虑在 Azure Stack 集线器中使用公共 IP 地址的速度，并对此进行管理。

> [!IMPORTANT]
> 如果 Azure Stack 集线器是有效的地址块，并且不与 Azure Stack 中心内现有的地址范围重叠，则它将接受提供的任何地址块。 请确保获取一个有效的地址块，该地址块可路由，并且与 Azure Stack 中心连接到的外部网络不重叠。 将范围添加到 Azure Stack 中心后，无法将其删除。

## <a name="add-the-ip-address-range-to-azure-stack-hub"></a>将 IP 地址范围添加到 Azure Stack 中心

1. 在浏览器中，请切换到管理员门户仪表板。 在此示例中，我们将使用 https://adminportal.local.azurestack.external 。
2. 以云操作员身份登录到 Azure Stack 中心管理员门户。
3. 在默认仪表板上，找到 "区域管理" 列表，并选择要管理的区域。 在此示例中，我们使用 local。
4. 找到 "资源提供程序" 磁贴，然后单击网络资源提供程序。
5. 单击 "公共 IP 池用量" 磁贴。
6. 单击 "添加 IP 池" 按钮。
7. 提供 IP 池的名称。 您选择的名称可帮助您轻松地识别 IP 池。 最好使名称与地址范围相同，但这并不是必需的。
8. 输入要添加为 CIDR 表示法的地址块。 例如： 192.168.203.0/24
9. 当你在 "地址范围（CIDR 块）" 字段中提供有效的 CIDR 范围时，"起始 IP 地址"、"结束 IP 地址" 和 "可用 IP 地址" 字段将自动填充。 它们是只读的，自动生成的，因此，如果不修改 "地址范围" 字段中的值，则无法更改这些字段。
10. 查看边栏选项卡上的信息并确认所有内容正确后，请选择 **"确定"** 提交更改并将地址范围添加到 Azure Stack 中心。


## <a name="next-steps"></a>后续步骤 
[查看缩放单位节点操作](azure-stack-node-actions.md)。
