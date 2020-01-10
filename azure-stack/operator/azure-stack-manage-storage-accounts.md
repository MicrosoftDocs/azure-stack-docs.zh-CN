---
title: 管理 Azure Stack 中心存储帐户 |Microsoft Docs
description: 了解如何查找、管理、恢复和回收 Azure Stack 中心存储帐户。
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
ms.openlocfilehash: 888d34441af672dfcfa27e8f48237c41105ce629
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817119"
---
# <a name="manage-azure-stack-hub-storage-accounts"></a>管理 Azure Stack 中心存储帐户

了解如何管理 Azure Stack 中心存储帐户。 根据业务需求查找、恢复和回收存储容量。

## <a name="find-a-storage-account"></a>查找存储帐户

可以通过以下步骤在 Azure Stack Hub 中查看区域中的存储帐户列表：

1. 登录到[管理员门户](https://adminportal.local.azurestack.external)。

2. 选择“所有服务” > “存储” > “存储帐户”。

   ![Azure Stack 中心存储帐户](media/azure-stack-manage-storage-accounts/image4.png)

默认情况下，将显示前10个帐户。 可以通过单击列表底部的 "**加载更多**" 链接来选择获取详细信息。

或者

如果你对特定存储帐户感兴趣，则只能**筛选和提取相关帐户**。

**筛选帐户：**

1. 选择窗格顶部的 "**筛选器**"。
2. 在 "筛选器" 窗格中，可以指定 "**帐户名**"、"**订阅 ID**" 或 "**状态**" 来微调要显示的存储帐户列表。 根据需要使用这些文件。
3. 键入时，列表将自动应用筛选器。

    ![筛选 Azure Stack 中心存储帐户](media/azure-stack-manage-storage-accounts/image5.png)

4. 若要重置筛选器：选择 "**筛选器**"，清除选择和更新。

在 "存储帐户" 列表窗格顶部的 "搜索" 文本框中，可以突出显示帐户列表中的选定文本。 当全名或 ID 不可用时，可以使用此名称。

你可以在此处使用 "免费文本" 来帮助查找你感兴趣的帐户。

![查找 Azure Stack 中心存储帐户](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>查看帐户详细信息
找到想要查看的帐户后，可以选择特定帐户查看特定的详细信息。 此时将打开一个新窗格，其中包含帐户详细信息。 这些详细信息包括帐户类型、创建时间、位置等。

![存储帐户详细信息](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>恢复已删除的帐户
你可能会遇到需要恢复已删除帐户的情况。

在 Azure Stack Hub 中，有一种简单的方法可实现此目的：

1. 浏览到 "存储帐户" 列表。 有关详细信息，请参阅本文顶部的[查找存储帐户](azure-stack-manage-storage-accounts.md)。
2. 在列表中找到该特定帐户。 可能需要筛选。
3. 检查帐户的*状态*。 它应显示为 "**已删除**"。
4. 选择帐户，该帐户将打开 "帐户详细信息" 窗格。
5. 在此窗格的顶部找到 "**恢复**" 按钮，并选择它。
6. 请选择“是”以确认。

   ![恢复存储帐户确认](media/azure-stack-manage-storage-accounts/image8.png)

7. 恢复现在正在进行。 等待指示该操作成功。 你还可以在门户顶部选择 "电铃" 图标以查看进度指示。

   ![存储帐户恢复成功](media/azure-stack-manage-storage-accounts/image9.png)

   成功同步已恢复的帐户后，可以再次使用它。

### <a name="some-gotchas"></a>一些问题
* 已删除的帐户将状态显示为 "不**保留**"。
  
  不保留，表示已删除帐户已超过保持期，可能无法恢复。

* 帐户列表中不显示已删除的帐户。
  
  如果已删除已删除的帐户，则帐户列表中可能不会显示帐户列表。 在这种情况下，不能恢复它。 有关详细信息，请参阅本文中的[回收容量](#reclaim)。

## <a name="set-the-retention-period"></a>设置保留期
云操作员可以使用“保留期”设置来指定一个时间段（以天为单位，值为 0 到 9999 天），在此时间段内删除的帐户有可能能够恢复。 默认保留期设置为 0 天。 将值设置为“0”表示任何已删除的帐户会立即失去保留期，并标记为定期接受垃圾回收。

**更改保持期：**

1. 登录到[管理员门户](https://adminportal.local.azurestack.external)。
2. 选择 "**管理**" 下的 "**所有服务**" > **区域管理**"。
3. 选择 "**资源提供程序**" > **存储** > **设置**。 路径为 Home >*区域*资源提供程序 > 存储。
4. 选择 "**配置**"，然后编辑 "保持期" 值。

   设置天数，然后将其保存。

   此值立即生效，并设置为整个区域。

   ![在管理员门户中编辑保留期](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>回收容量
保持期的一个副作用是，已删除的帐户将继续消耗容量，直到其保留期结束。 作为云操作员，你可能需要一种方法来回收已删除的帐户空间，即使保留期尚未过期。

可以使用门户或 PowerShell 回收容量。

**使用门户回收容量：**
1. 导航到 "存储帐户" 窗格。 请参阅查找存储帐户。
2. 选择窗格顶部的 "**回收空间**"。
3. 阅读消息，然后选择 **"确定"** 。

    ![回收存储帐户中的空间](media/azure-stack-manage-storage-accounts/image11.png)

4. 等待成功通知。 请参阅门户上的钟形图标。

    ![已成功回收空间](media/azure-stack-manage-storage-accounts/image12.png)

5. 刷新 "存储帐户" 页。 删除的帐户不再显示在列表中，因为它们已被清除。

你还可以使用 PowerShell 显式覆盖保持期并立即回收容量。

**使用 PowerShell 回收容量：**

1. 确认已安装并配置 Azure PowerShell。 否则，请使用以下说明： 
   * 若要安装最新 Azure PowerShell 版本并将其与 Azure 订阅相关联，请参阅[如何安装和配置 Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/)。
   有关 Azure 资源管理器 cmdlet 的详细信息，请参阅将[Azure PowerShell 与 azure 资源管理器配合使用](https://go.microsoft.com/fwlink/?LinkId=394767)。
2. 运行以下 cmdlet：

> [!NOTE]  
> 如果你运行这些 cmdlet，则会永久删除帐户及其内容。 它不可恢复。 请谨慎使用。

```powershell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

有关详细信息，请参阅[Azure Stack 中心 PowerShell 文档](https://docs.microsoft.com/powershell/azure/azure-stack/overview)。
 

## <a name="next-steps"></a>后续步骤

 - 有关管理权限的信息，请参阅[使用基于角色的访问控制设置访问权限](azure-stack-manage-permissions.md)。
 - 有关管理 Azure Stack 中心存储容量的信息，请参阅[管理 Azure Stack 中心的存储容量](azure-stack-manage-storage-shares.md)。
