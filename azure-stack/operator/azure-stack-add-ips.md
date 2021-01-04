---
title: 在 Azure Stack Hub 中添加公共 IP 地址
description: 了解如何向 Azure Stack Hub 添加公共 IP 地址。
author: justinha
ms.topic: article
ms.date: 05/28/2020
ms.author: justinha
ms.reviewer: scottnap
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 245817c62d2c5ddbd0856bdf4af869b7a66dbe18
ms.sourcegitcommit: 8790b8a4ecf4421409534df5ff510d537cc000da
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/29/2020
ms.locfileid: "97801991"
---
# <a name="add-public-ip-addresses"></a>添加公共 IP 地址

本文将外部地址称作公共 IP 地址。 在 Azure Stack Hub 的上下文中，公共 IP 地址是可从 Azure Stack Hub 外部访问的 IP 地址。 就本文来说，不管外部网络是可以通过公共 Internet 路由的网络，还是位于 Intranet 中并使用专用地址空间的网络，都没有关系，步骤是相同的。 

虽然可以设置多个 IP 池，但是你将无法选择要使用的 IP 池。 Azure Stack Hub 将所有 IP 池视为一个线程。 创建资源时，无法选择要分配的 IP。

> [!IMPORTANT]
> 本文中的步骤仅适用于使用合作伙伴工具包 1809 或更高版本部署的系统。 在版本 1809 之前部署的系统需要更新机架安装式 (TOR) 交换机访问控制列表 (ACL)，才能允许新的公共 VIP 池范围。 如果运行早期的交换机配置，请在 OEM 的配合下为新的公共 IP 池添加相应的“允许”ACL，或使用最新的合作伙伴工具包重新配置交换机，以防新的公共 IP 地址遭到阻止。

## <a name="add-a-public-ip-address-pool"></a>添加公共 IP 地址池
在完成 Azure Stack Hub 系统的初始部署以后，可以随时向 Azure Stack Hub 系统添加公共 IP 地址。 了解如何[查看公共 IP 地址使用情况](azure-stack-viewing-public-ip-address-consumption.md)，确定 Azure Stack Hub 上的当前使用情况和公共 IP 地址可用性。

概括说来，向 Azure Stack Hub 添加新公共 IP 地址块的过程如下所示：

 ![添加 IP 流](media/azure-stack-add-ips/flow.svg)

## <a name="obtain-the-address-block-from-your-provider"></a>从提供程序处获取地址块
首先需要做的事是获取需要添加到 Azure Stack Hub 的地址块。 根据你从何处获取地址块，考虑提前期是多少，并在对其进行管理时考虑到在 Azure Stack Hub 中使用公共 IP 地址的速率。

> [!IMPORTANT]
> Azure Stack Hub 会接受你提供的任何地址块，只要它是有效的地址块并且不与 Azure Stack Hub 中现有的地址范围重叠即可。 请确保获取可路由的有效地址块，并且该地址块不与 Azure Stack Hub 连接到的外部网络重叠。 向 Azure Stack Hub 添加该范围后，就不能删除它。

## <a name="add-the-ip-address-range-to-azure-stack-hub"></a>向 Azure Stack Hub 添加 IP 地址范围

1. 在浏览器中，转到管理员门户仪表板。 对于本示例，我们将使用 `https://adminportal.local.azurestack.external`。
2. 以云操作员的身份登录到 Azure Stack Hub 管理门户。
3. 在默认仪表板上找到“区域管理”列表，并选择要管理的区域。 本示例使用“本地”。
4. 找到“资源提供程序”磁贴，单击网络资源提供程序。
5. 单击“公共 IP 池用量”磁贴。
6. 单击“添加 IP 池”按钮。
7. 提供 IP 池的名称。 所选的名称有助于轻松识别 IP 池。 不能在此字段中使用特殊字符，如 "/"。 最好是使名称与地址范围相同，但这不是必需的。
8. 以 CIDR 表示法输入要添加的地址块。 例如：192.168.203.0/24
9. 在“地址范围(CIDR 块)”字段中提供有效的 CIDR 范围以后，“开始 IP 地址”、“结束 IP 地址”和“可用 IP 地址”字段会自动填充。 这些字段为只读字段，并且是自动生成的，因此在不修改“地址范围”字段中的值的情况下，不能更改这些字段。
10. 查看边栏选项卡上的信息并确认一切正确以后，请选择“确定”，以提交所做的更改并将地址范围添加到 Azure Stack Hub。


## <a name="next-steps"></a>后续步骤 
[查看缩放单元节点操作](azure-stack-node-actions.md)。
