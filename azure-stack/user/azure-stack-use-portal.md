---
title: 使用 Azure Stack 集线器用户门户 |Microsoft Docs
description: 了解如何在 Azure Stack 中心访问和使用用户门户。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: efemmano
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 4dec7bfe4ee19f1762311fb40eee9a43626d6cfd
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75815708"
---
# <a name="use-the-azure-stack-hub-user-portal"></a>使用 Azure Stack 集线器用户门户

*适用于： Azure Stack 集线器集成系统和 Azure Stack 开发工具包*

使用 Azure Stack 中心门户订阅公共产品/服务，并使用这些产品提供的服务。 如果已使用 global Azure 门户，则已熟悉网站的工作原理。

## <a name="access-the-portal"></a>访问门户

你的 Azure Stack 中心操作员（你的组织中的服务提供商或管理员）将让你知道正确的 URL 来访问门户。

- 对于集成系统，URL 根据操作员的区域和外部域名而变化，格式为 https://portal.&lt ;*区域*&gt; 。&lt;*FQDN*&gt; 。
- 如果使用的是 Azure Stack 开发工具包（ASDK），则 https://portal.local.azurestack.external 门户地址。
- 所有 Azure Stack 中心部署的默认时区均设置为协调世界时（UTC）。 您可以在安装 Azure Stack 集线器时选择时区，但在安装过程中，它将自动恢复为默认值。

## <a name="customize-the-dashboard"></a>自定义仪表板

该仪表板包含一组默认磁贴。 选择 "**编辑仪表板**" 以修改默认仪表板，或选择 "**新建仪表板**" 创建自定义仪表板。 可以通过添加或删除磁贴来轻松地自定义仪表板。 例如，若要添加计算磁贴，请选择 " **+ 创建资源**"。 右键单击 "**计算**"，然后选择 "**固定到仪表板**"。

![Azure Stack 集线器用户门户的屏幕截图](media/azure-stack-use-portal/userportal.png)

将仪表板还原为原始设置：
1.  选择 "**编辑仪表板**"。 
2.  右键单击并选择 "**重置为默认状态**"。

## <a name="create-subscription-and-browse-available-resources"></a>创建订阅并浏览可用资源

如果还没有订阅，则需要订阅产品/服务。 之后，你可以浏览可用的资源。 若要浏览和创建资源，请使用以下方法之一：

- 在仪表板上选择 " **Marketplace** " 磁贴。
- 在 "**所有资源**" 磁贴上，选择 "**创建资源**"。
- 在左侧导航窗格中，选择 " **+ 创建资源**"。

## <a name="learn-how-to-use-available-services"></a>了解如何使用可用服务

如果需要有关如何使用可用服务的指南，可能有不同的选项可供您使用。

- 你的组织或服务提供商可能会提供自己的文档，如果他们提供自定义的服务或应用程序，则通常就是这种情况。
- 第三方应用有其自己的文档。
- 对于 Azure 一致性服务，我们强烈建议你先查看 Azure Stack 中心文档。 若要访问 Azure Stack 集线器用户文档，请选择 "帮助" 图标（ **？** ），然后选择 "**帮助 + 支持**"。

    ![UI 中的 "帮助和支持" 选项](media/azure-stack-use-portal/HelpAndSupport.png)

    具体而言，我们建议您查看以下文章以开始操作：

    - [关键注意事项：使用服务或为 Azure Stack 中心构建应用](azure-stack-considerations.md)。
    - 文档的 "**使用服务**" 部分提供了有关每个服务的注意事项。 "注意事项" 页面描述了在 Azure 中提供的服务与 Azure Stack 中心提供的相同服务之间的差异。 有关示例，请参阅[VM 注意事项](azure-stack-vm-considerations.md)。 "**使用服务**" 部分中可能有 Azure Stack 中心独有的其他信息。

      你可以使用 Azure 文档作为服务的一般参考，但你必须了解这些差异。 了解 "**快速入门教程**" 磁贴上的文档链接指向 Azure 文档。

## <a name="get-support"></a>获取支持

如果需要支持，请联系你的组织或服务提供商获取帮助。

如果你使用的是 Azure Stack 开发工具包（ASDK），则[Azure Stack 中心论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)是唯一支持的来源。

## <a name="next-steps"></a>后续步骤

[关键注意事项：使用服务或为 Azure Stack 中心构建应用](azure-stack-considerations.md)
