---
title: 管理 Azure Stack 存储帐户 | Microsoft Docs
description: 了解如何查找、管理、恢复和回收 Azure Stack 存储帐户
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: a7bd45b8eefe201a8c3767f31f7de138e37bb01d
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909272"
---
# <a name="manage-azure-stack-storage-accounts"></a>管理 Azure Stack 存储帐户

了解如何管理 Azure Stack 存储帐户。 根据业务需求查找、恢复和回收存储容量。

## <a name="find-a-storage-account"></a>查找存储帐户

可在 Azure Stack 中通过以下方式查看区域中的存储帐户列表：

1. 登录到[管理员门户](https://adminportal.local.azurestack.external)。

2. 选择“所有服务” > “存储” > “存储帐户”。

   ![Azure Stack 存储帐户](media/azure-stack-manage-storage-accounts/image4.png)

默认显示前 10 个帐户。 可以单击列表底部的“加载更多”链接来提取更多的帐户。

或

如果只想查看特定的存储帐户，可以只**筛选并提取相关的帐户**。


**筛选帐户：**

1. 选择窗格顶部的“筛选”。
2. 在“筛选”窗格中，可以指定“帐户名”、“订阅 ID”或“状态”，以调整要显示的存储帐户列表。 适当地使用筛选器。
3. 键入时，列表会自动应用筛选器。  .
   
    ![筛选 Azure Stack 存储帐户](media/azure-stack-manage-storage-accounts/image5.png)

4. 若要重置筛选器，请选择“筛选”、清除选择内容，然后更新。

使用搜索文本框（位于存储帐户列表窗格的顶部）可以突出显示帐户列表中选择的文本。 无法轻松获取完整名称或 ID 时，可以使用此功能。

可在此处使用任意文本，帮助找到想要查看的帐户。

![查找 Azure Stack 存储帐户](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>查看帐户详细信息
找到想要查看的帐户后，可以选择特定帐户查看特定详细信息。 此时将打开新的窗格，其中包含帐户详细信息，例如帐户类型、创建时间、位置等等。

![](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>恢复已删除的帐户
有时，我们需要恢复已删除的帐户。

在 Azure Stack 中，可通过一种简单的方法实现此目的：

1. 浏览到存储帐户列表。 有关详细信息，请参阅本文中的“查找存储帐户”。
2. 在列表中找到该特定帐户。 可能需要执行筛选。
3. 检查帐户的状态。 状态应显示为“已删除”。
4. 选择该帐户，这会打开帐户详细信息窗格。
5. 在此窗格的顶部找到“恢复”按钮，并选择它。
6. 请选择“是”以确认。
   
   ![](media/azure-stack-manage-storage-accounts/image8.png)
7. 恢复现在正在进行中 *。等待*指示该操作成功。
   也可以选择门户顶部的“铃铛”图标查看进度指示。
   
   ![](media/azure-stack-manage-storage-accounts/image9.png)
   
   成功同步已恢复的帐户之后，可以再次使用该帐户。

### <a name="some-gotchas"></a>注意事项
* 已删除的帐户显示“超出保留期”状态。
  
  超出保留期意味着已删除的帐户超出了保留期，可能无法恢复。
* 已删除的帐户未显示在帐户列表中。
  
  删除的帐户已作为垃圾回收后，该帐户可能不会显示在帐户列表中。 在此情况下，无法恢复该帐户。 请参阅本文中的[回收容量](#reclaim)。

## <a name="set-the-retention-period"></a>设置保留期
云操作员可以使用“保留期”设置来指定一个时间段（以天为单位，值为 0 到 9999 天），在此时间段内删除的帐户有可能能够恢复。 默认保留期设置为 0 天。 将值设置为“0”表示任何已删除的帐户会立即超出保留期，并标记为定期进行垃圾回收。

**更改保留期：**

1. 登录到[管理员门户](https://adminportal.local.azurestack.external)。
2. 在“管理”下选择“所有服务” > “区域管理”。
3. 选择“资源提供程序” > “存储” > “设置”。 路径为主目录 > *区域* - 资源提供程序 > 存储。
4. 选择“配置”，然后编辑保留期值。

   设置天数并保存。
   
   此值立即生效，并且是针对整个区域设置的。

   ![](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>回收容量
使用保留期的负面影响之一是，已删除的帐户在超出保留期之前会一直消耗容量。 云操作员可能需要通过某种方式，在保留期尚未到来之前回收已删除帐户占用的空间。

可以使用门户或 PowerShell 来回收容量。

**使用门户回收容量：**
1. 导航到存储帐户窗格。 请参阅“查找存储帐户”。
2. 选择窗格顶部的“回收空间”。
3. 阅读消息，并选择“确定”。

    ![](media/azure-stack-manage-storage-accounts/image11.png)
4. 等待显示成功通知（查看门户上的铃铛图标）。

    ![](media/azure-stack-manage-storage-accounts/image12.png)
5. 刷新“存储帐户”页。 已删除的帐户已被清除，因此不再显示在列表中。

也可以使用 PowerShell 显式替代保留期，并立即回收容量。

**使用 PowerShell 回收容量：**   

1. 确认已安装并配置 Azure PowerShell。 如果未安装，请遵照以下说明： 
   * 若要安装最新版本的 Azure PowerShell 并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)。
   有关 Azure 资源管理器 cmdlet 的详细信息，请参阅[将 Azure PowerShell 与 Azure 资源管理器配合使用](https://go.microsoft.com/fwlink/?LinkId=394767)
2. 运行以下 cmdlet：

> [!NOTE]  
> 如果运行这些 cmdlet，将会永久删除帐户及其内容。 不可恢复。 请慎用此 cmdlet。

```powershell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

有关详细信息，请参阅 [Azure Stack PowerShell 文档](https://docs.microsoft.com/powershell/azure/azure-stack/overview)。
 

## <a name="next-steps"></a>后续步骤

 - 有关管理权限的信息，请参阅[管理基于角色的访问控制](azure-stack-manage-permissions.md)。
 - 有关管理 Azure Stack 的存储容量的信息，请参阅[管理 Azure Stack 的存储容量](azure-stack-manage-storage-shares.md)。
