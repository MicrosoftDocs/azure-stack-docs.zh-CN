---
title: 在 Azure Stack 中创建存储帐户 |Microsoft Docs
titleSuffix: Azure Stack
description: 了解如何在 Azure Stack 中创建存储帐户。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/2/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 449d9e39b650e6f7ccd91f4703709ea033e7a5dc
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802337"
---
# <a name="create-storage-accounts-in-azure-stack"></a>在 Azure Stack 中创建存储帐户

Azure Stack 中的存储帐户包括 Blob 和表服务，以及你的存储数据对象的唯一命名空间。 默认情况下，你的帐户中的数据仅适用于你的存储帐户所有者。

1. 在 Azure Stack POC 计算机上，以[管理员身份](../asdk/asdk-connect.md)登录到 `https://adminportal.local.azurestack.external`，然后单击 " **+ 创建资源**" > **数据 + 存储** > **存储帐户**。

   ![在 Azure Stack 管理员门户中创建存储帐户](media/azure-stack-provision-storage-account/image01.png)

2. 在 "**创建存储帐户**" 边栏选项卡中，键入存储帐户的名称。 创建新的**资源组**，或选择现有的资源组，然后单击 "**创建**" 以创建存储帐户。

   ![在 Azure Stack 管理员门户中查看存储帐户](media/azure-stack-provision-storage-account/image02.png)

3. 若要查看新的存储帐户，请单击 "**所有资源**"，然后搜索存储帐户并单击其名称。

    ![Azure Stack 管理员门户中的存储帐户名称](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>后续步骤

- [使用 Azure 资源管理器模板](../user/azure-stack-arm-templates.md)
- [了解 Azure 存储帐户](/azure/storage/common/storage-create-storage-account)
- [下载 Azure Stack Azure 一致的存储验证指南](https://aka.ms/azurestacktp1doc)
