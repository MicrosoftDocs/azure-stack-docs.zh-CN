---
title: 将虚拟机提供给 Azure Stack 用户使用 | Microsoft Docs
description: 了解如何使虚拟机可在 Azure Stack 上使用
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
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: unknown
ms.lastreviewed: 09/11/2018
ms.custom: mvc
ms.openlocfilehash: b39eaf4365f74e59c6dd60db67fdadf1162cb91d
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268134"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>教程：将虚拟机提供给 Azure Stack 用户使用

Azure Stack 云管理员可以创建套餐供用户（有时称为租户）订阅。 通过订阅套餐，用户可以使用套餐提供的 Azure Stack 服务。

本教程介绍如何为虚拟机创建套餐，然后以用户身份登录，以测试该套餐。

要学习的知识：

> [!div class="checklist"]
> * 创建产品
> * 添加映像
> * 测试产品/服务

在 Azure Stack 中，可通过订阅、套餐和计划将服务交付给用户。 用户可以订阅多个产品/服务。 套餐可以包含一个或多个计划，计划可以包含一个或多个服务。

![订阅、套餐和计划](media/azure-stack-key-features/image4.png)

有关详细信息，请参阅 [Azure Stack 中的重要功能和概念](azure-stack-overview.md)。

## <a name="create-an-offer"></a>创建产品

套餐是提供者提供给用户购买或订阅的一个或多个计划的组合。 套餐的创建过程包括多个步骤。 首先，系统会依次提示创建套餐、计划和配额。

1. 以云管理员身份[登录](../asdk/asdk-connect.md)到门户，选择“+ 创建资源” > “套餐 + 计划” > “套餐”。   

   ![新产品/服务](media/azure-stack-tutorial-tenant-vm/image01.png)

1. 在“新建套餐”中，输入“显示名称”和“资源名称”，然后选择新的或现有的**资源组**。    “显示名称”是套餐的友好名称。 只有云操作员可以看到资源名称，管理员可以使用该名称将套餐作为 Azure 资源管理器资源处理。

   ![显示名称](media/azure-stack-tutorial-tenant-vm/image02.png)

1. 选择“基本计划”，在“计划”部分选择“添加”，将新计划添加到套餐。   

   ![添加计划](media/azure-stack-tutorial-tenant-vm/image03.png)

1. 在“新建计划”部分中，填写“显示名称”和“资源名称”。    显示名称是用户可以看到的计划友好名称。 只有云操作员可以看到资源名称，云操作员可以使用该名称将计划作为 Azure 资源管理器资源处理。

   ![计划显示名称](media/azure-stack-tutorial-tenant-vm/image04.png)

1. 选择“服务”。  在“服务”列表中，选择“Microsoft.Compute”、“Microsoft.Network”和“Microsoft.Storage”。    选择“选择”，将这些服务添加到计划。 

   ![计划服务](media/azure-stack-tutorial-tenant-vm/image05.png)

1. 选择“配额”，然后选择要为其创建配额的第一个服务。  对于 IaaS 配额，请使用以下示例作为指导，配置“计算”、“网络”和“存储服务”的配额。

   - 首先为“计算”服务创建配额。 在命名空间列表中，选择“Microsoft.Compute”，然后选择“创建新配额”。  

     ![创建新配额](media/azure-stack-tutorial-tenant-vm/image06.png)

   - 在“创建配额”中，输入配额的名称。  对于所要创建的配额，可以更改或接受显示的配额值。 在此示例中，我们接受默认设置，并选择“确定”。 

     ![配额名称](media/azure-stack-tutorial-tenant-vm/image07.png)

   - 在命名空间列表中选择“Microsoft.Compute”，然后选择创建的配额。  这会将该配额链接到“计算”服务。

     ![选择配额](media/azure-stack-tutorial-tenant-vm/image08.png)

      针对“网络”和“存储”服务重复上述步骤。 完成后，在“配额”中选择“确定”以保存所有配额。  

1. 在“新建计划”中，选择“确定”。  

1. 在“计划”下面选择新计划，然后选择“选择”。  

1. 在“新建套餐”中，选择“创建”。   创建套餐后，会看到通知。

1. 在仪表板菜单中选择“套餐”，然后选择创建的套餐。 

1. 依次选择“更改状态”、“公共”。  

    ![公共状态](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>添加映像

部署虚拟机之前，必须先将一个映像添加到 Azure Stack 市场。 可以从 Azure 市场添加所选的映像，包括 Linux 映像。

如果在联网场景中操作，且已向 Azure 注册 Azure Stack 实例，则可以使用[将市场项从 Azure 下载到 Azure Stack](azure-stack-download-azure-marketplace-item.md) 主题中所述的步骤，从 Azure 市场下载 Windows Server 2016 VM 映像。

有关将不同的项添加到市场 的信息，请参阅 [Azure Stack 市场](azure-stack-marketplace.md)。

## <a name="test-the-offer"></a>测试产品/服务

现在，已创建产品/服务，可以测试它。 以用户身份登录并订阅该套餐，然后添加虚拟机。

1. **订阅套餐**

   a. 使用用户帐户登录到用户门户，然后选择“获取订阅”磁贴。 
   - 对于集成系统，URL 根据操作员所在的区域和外部域名的不同而异，格式为 https://portal.&lt;*region*&gt;.&lt;*FQDN*&gt;。
   - 如果使用的是 Azure Stack 开发工具包，则门户地址为 https://portal.local.azurestack.external。

   ![获取订阅](media/azure-stack-tutorial-tenant-vm/image10.png)

   b. 在“获取订阅”中的“显示名称”字段内输入订阅的名称。   选择“套餐”，然后在“选择套餐”列表中选择一个套餐。   选择“创建”  。

   ![创建产品](media/azure-stack-tutorial-tenant-vm/image11.png)

   c. 若要查看订阅，请选择“所有服务”  ，然后在“常规”  类别下选择“订阅”  。 选择新订阅，以查看其中包含的服务。

   >[!NOTE]
   >订阅套餐之后，可能需要刷新门户才能看到哪些服务包含在新订阅中。

1. **预配虚拟机**

   可在用户门户中使用新订阅预配虚拟机。

   a. 使用用户帐户登录到用户门户。
   - 对于集成系统，URL 根据操作员所在的区域和外部域名的不同而异，格式为 https://portal.&lt;*region*&gt;.&lt;*FQDN*&gt;。
     - 如果使用的是 Azure Stack 开发工具包，则门户地址为 https://portal.local.azurestack.external。

   b.  在仪表板上，选择“+创建资源” > “计算” > “Windows Server 2016 Datacenter Eval”，然后选择“创建”。    

   c. 在“基本信息”中提供以下信息： 
      - 输入**名称**
      - 输入**用户名**
      - 输入**密码**
      - 选择**订阅**
      - 创建**资源组**（或选择现有的资源组）。 
      - 选择“确定”保存此信息。 

   d. 在“选择大小”中，选择“A1 标准”，然后选择“选择”    。  

   e. 在“设置”中，选择“虚拟网络”。  

   f. 在“选择虚拟网络”中，选择“新建”。  

   g. 在“创建虚拟网络”中接受所有默认值，然后选择“确定”。  

   h. 在“设置”中选择“确定”，以保存网络配置。  

      i. 在“摘要”中，选择“确定”创建虚拟机。    

   j. 若要查看新虚拟机，请选择“所有资源”。  搜索该虚拟机，然后从搜索结果中选择其名称。

   
## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 创建产品
> * 添加映像
> * 测试产品/服务

转到下一教程，了解如何执行以下操作：
> [!div class="nextstepaction"]
> [将 SQL 数据库提供给 Azure Stack 用户使用](azure-stack-tutorial-sql-server.md)