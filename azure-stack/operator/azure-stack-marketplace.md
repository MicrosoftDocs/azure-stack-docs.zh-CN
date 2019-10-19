---
title: Azure Stack Marketplace 概述 |Microsoft Docs
description: Azure Stack Marketplace 和 marketplace 项的概述。
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: ihcherie
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: da8fdf20446e5ed60271e236f44939d439118b4b
ms.sourcegitcommit: b5eb024d170f12e51cc852aa2c72eabf26792d8d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72534092"
---
# <a name="azure-stack-marketplace-overview"></a>Azure Stack Marketplace 概述

*适用于： Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack Marketplace 是为 Azure Stack 自定义的服务、应用和资源的集合。 资源包括网络、虚拟机（Vm）、存储等。 使用 Azure Stack Marketplace 创建新资源并部署新应用，或浏览并选择要使用的项目。 若要使用 marketplace 项，用户必须订阅向其授予对项目的访问权限的产品/服务。

作为 Azure Stack 运算符，你可以决定要将哪些项添加（发布）到 Azure Stack Marketplace。 你可以发布项目，如数据库、应用服务等。 发布使项对所有用户可见。 可以发布创建的自定义项，也可以从不断增长[的 Azure Marketplace 项列表](azure-stack-marketplace-azure-items.md)中发布项。 将项发布到 Azure Stack Marketplace 后，用户可以在5分钟内看到它。

> [!CAUTION]  
> 在 Azure Stack Marketplace 中提供所有库项项目（包括图像和 JSON 文件）后，无需进行身份验证即可访问这些项目。 有关发布自定义 marketplace 项时的更多注意事项，请参阅[创建和发布 marketplace 项](azure-stack-create-and-publish-marketplace-item.md)。

若要打开 Marketplace，请在管理员门户中选择 " **+ 创建资源**"。

![在 Azure Stack 管理员门户中创建资源](media/azure-stack-marketplace/marketplace1.png)

## <a name="marketplace-items"></a>Marketplace 项

Azure Stack Marketplace 项是用户可以下载和使用的服务、应用或资源。 所有用户都可以看到所有 Azure Stack Marketplace 项，包括诸如计划和产品/服务等管理项。 这些管理项不需要订阅即可查看，但对用户不起作用。

每个 marketplace 项都有：

* 用于资源预配的 Azure 资源管理器模板。
* 元数据，如字符串、图标和其他市场宣传材料。
* 用于在门户中显示项的格式设置信息。

发布到 Azure Stack Marketplace 的每个项目都使用 Azure 库包（.azpkg）格式。 将部署或运行时资源（带有软件或 VM 映像的代码、.zip 文件）添加到单独 Azure Stack，而不是作为 marketplace 项的一部分。

在版本1803及更高版本中，在从 Azure 下载或上传自定义映像时，Azure Stack 将图像转换为稀疏文件。 此过程会在添加图像时添加时间，但节省空间并加速这些映像的部署。 转换仅适用于新映像。 不会更改现有映像。

## <a name="next-steps"></a>后续步骤

* [从 Azure 下载现有 marketplace 项并发布到 Azure Stack](azure-stack-download-azure-marketplace-item.md)  
* [创建和发布自定义 Azure Stack Marketplace 项](azure-stack-create-and-publish-marketplace-item.md)
