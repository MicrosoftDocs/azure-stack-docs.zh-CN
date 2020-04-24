---
title: Azure Stack Hub 市场概述
description: Azure Stack Hub 市场和市场项概述。
author: sethmanheim
ms.topic: article
ms.date: 04/20/2020
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 638ffe45cef9301059fd9e9e39b74b793e2ba86a
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661352"
---
# <a name="azure-stack-hub-marketplace-overview"></a>Azure Stack Hub 市场概述

Azure Stack Hub 市场是针对 Azure Stack Hub 自定义的服务、应用和资源的集合。 资源包括网络、虚拟机 (VM)、存储，等等。 可以使用 Azure Stack Hub 市场创建新资源和部署新应用，或者浏览并选择想要使用的项。 若要使用某个市场项，用户必须订阅一个可让他们访问该项的产品/服务。

作为 Azure Stack Hub 操作员，你可以确定要添加（发布）到 Azure Stack Hub 市场的项。 可以发布数据库、应用程序服务等项。 发布使项对所有用户可见。 可以发布创建的自定义项，或者发布不断扩充的 [Azure 市场项列表](azure-stack-marketplace-azure-items.md)中所列的项。 将某个项发布到 Azure Stack Hub 市场后，用户在五分钟内就能看到它。

> [!CAUTION]  
> 将所有库项项目（包括映像和 JSON 文件）发布到 Azure Stack Hub 市场后，无需身份验证即可访问这些项目。 有关发布自定义市场项时的其他注意事项，请参阅[创建和发布市场项](azure-stack-create-and-publish-marketplace-item.md)。

若要打开市场，请在管理员门户中选择“+ 创建资源”  。

![在 Azure Stack Hub 管理员门户中创建资源](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>市场项

Azure Stack Hub 市场项是可供用户下载并使用的服务、应用或资源。 所有用户都可以看到所有 Azure Stack Hub 市场项，包括计划和套餐等管理项。 这些管理项不需要订阅即可查看，但对用户不起作用。

每个市场项具有：

* 用于资源预配的 Azure 资源管理器模板。
* 元数据，例如字符串、图标和其他营销材料。
* 有关在门户中显示该项的格式信息。

发布到 Azure Stack Hub 市场的每个项都使用 Azure 库包 (.azpkg) 格式。 请单独地将部署或运行时资源（代码、包含软件的 .zip 文件或 VM 映像）添加到 Azure Stack Hub，而不要将其作为市场项的一部分添加。

在 1803 及更高版本中，从 Azure 下载映像或上传自定义映像时，Azure Stack Hub 会将映像转换为稀疏文件。 此过程会在添加映像时延长时间，但可节省空间并加速这些映像的部署。 转换仅适用于新映像。 现有映像不会更改。

## <a name="next-steps"></a>后续步骤

* [从 Azure 下载现有市场项并将其发布到 Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)  
* [创建并发布自定义 Azure Stack Hub 市场项](azure-stack-create-and-publish-marketplace-item.md)
