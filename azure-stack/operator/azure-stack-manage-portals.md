---
title: 在 Azure Stack 集线器中使用管理员门户
description: 了解如何在 Azure Stack 集线器中使用管理员门户。
author: justinha
ms.topic: quickstart
ms.date: 06/07/2019
ms.author: justinha
ms.reviewer: efemmano
ms.lastreviewed: 06/07/2019
ms.openlocfilehash: def9dd3fca3c7e3a2076ffdf79b96fa00e72fac5
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882015"
---
# <a name="use-the-administrator-portal-in-azure-stack-hub"></a>在 Azure Stack 集线器中使用管理员门户

Azure Stack 中心有两个门户：管理员门户和用户门户。 作为 Azure Stack 中心操作员，你可以使用管理员门户来实现 Azure Stack 中心的日常管理和操作。

## <a name="access-the-administrator-portal"></a>访问管理员门户

若要访问管理员门户，请浏览到门户 URL 并使用 Azure Stack 中心操作员凭据进行登录。 对于集成系统，门户 URL 根据 Azure Stack 中心部署的区域名称和外部完全限定的域名（FQDN）而有所不同。 Azure Stack 开发工具包（ASDK）部署的管理员门户 URL 始终相同。

| 环境 | 管理员门户 URL |   
| -- | -- | 
| ASDK| https://adminportal.local.azurestack.external  |
| 集成系统 | https://adminportal.&lt ;*区域*&gt; 。&lt;*FQDN*&gt; | 
| | |

> [!TIP]
> 对于 ASDK 环境，首先需要确保可以通过远程桌面连接或虚拟专用网络（VPN）[连接到开发工具包主机](../asdk/asdk-connect.md)。

 ![Azure Stack 中心管理员门户](media/azure-stack-manage-portals/admin-portal.png)

所有 Azure Stack 中心部署的默认时区均设置为协调世界时（UTC）。

在管理员门户中，你可以执行如下操作：

* [向 Azure 注册 Azure Stack 集线器](azure-stack-registration.md)
* [填充 marketplace](azure-stack-download-azure-marketplace-item.md)
* [为用户创建计划、产品/服务和订阅](service-plan-offer-subscription-overview.md)
* [监视运行状况和警报](azure-stack-monitor-health.md)
* [管理 Azure Stack 中心更新](azure-stack-updates.md)

"**快速入门教程**" 磁贴提供指向最常见任务的联机文档的链接。

尽管操作员可以在管理员门户中创建虚拟机（Vm）、虚拟网络和存储帐户等资源，但应[登录到用户门户](../user/azure-stack-use-portal.md)来创建和测试资源。

>[!NOTE]
>"快速入门教程" 磁贴中的 "**创建虚拟机**" 链接在管理员门户中创建了一个 VM，但这仅用于验证 Azure Stack 中心是否已成功部署。

## <a name="understand-subscription-behavior"></a>了解订阅行为

默认情况下，在管理员门户中创建三个订阅：消耗、默认提供程序和计量。 作为操作员，你将主要使用*默认提供程序订阅*。 你不能添加任何其他订阅，并在管理员门户中使用它们。

其他订阅会根据你为其创建的计划和产品/服务在用户门户中创建。 但是，用户门户不提供对管理员门户的任何管理或操作功能的访问权限。

管理员和用户门户由 Azure 资源管理器的单独实例支持。 由于此 Azure 资源管理器分离，订阅不交叉门户。 例如，如果你作为 Azure Stack 中心操作员登录到用户门户，则无法访问*默认提供商订阅*。 尽管你不能访问任何管理功能，但你可以为自己的可用公共产品/服务创建订阅。 只要你登录到用户门户，你就会被视为租户用户。

  >[!NOTE]
  >在 ASDK 环境中，如果用户与 Azure Stack 中心操作员属于同一租户目录，则不会阻止用户登录到管理员门户。 但是，他们不能访问任何管理功能，也不能添加订阅以访问用户门户中可用的产品/服务。

## <a name="administrator-portal-tips"></a>管理员门户提示

### <a name="customize-the-dashboard"></a>自定义仪表板

该仪表板包含一组默认磁贴。 你可以选择 "**编辑仪表板**" 来修改默认仪表板，或选择 "**新建仪表板**" 以添加自定义仪表板。 你还可以将磁贴添加到仪表板。 例如，选择 " **+ 创建资源**"，右键单击 "**产品/服务 + 计划**"，然后选择 "**固定到仪表板**"。

有时，你可能会在门户中看到一个空白仪表板。 若要恢复仪表板，请单击 "**编辑仪表板**"，然后右键单击并选择 "**重置为默认状态**"。

### <a name="quick-access-to-online-documentation"></a>快速访问联机文档

若要访问 Azure Stack 中心操作员文档，请使用管理员门户右上角的 "帮助和支持" 图标（问号）。 将光标移到图标上，然后选择 "**帮助 + 支持**"。

### <a name="quick-access-to-help-and-support"></a>快速访问帮助和支持

如果单击管理员门户右上角的 "帮助" 图标（问号），请单击 "**帮助 + 支持**"，然后单击 "**支持**" 下的 "**新建支持请求**"，会出现以下结果之一：

- 如果你使用的是集成系统，此操作将打开一个网站，你可以在其中直接与 Microsoft 客户支持服务（CSS）一起打开支持票证。 请参阅[在何处获取支持](azure-stack-manage-basics.md#where-to-get-support)，了解何时应通过 Microsoft 支持或原始设备制造商（OEM）硬件供应商支持。
- 如果使用的是 ASDK，则此操作会直接打开[Azure Stack 中心论坛站点](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack)。 这些论坛会定期受到监视。 由于 ASDK 是一个评估环境，因此没有通过 Microsoft CSS 提供的官方支持。

### <a name="quick-access-to-the-azure-roadmap"></a>快速访问 Azure 路线图

如果选择管理员门户右上角的 "**帮助和支持**" （问号），然后选择 " **Azure 路线图**"，则会打开一个新的浏览器选项卡，并转到 Azure 路线图。 通过在 "**产品**" 搜索框中键入**Azure Stack Hub** ，你可以查看所有 Azure Stack 中心路线图更新。

## <a name="next-steps"></a>后续步骤

向[Azure 注册 Azure Stack 集线器](azure-stack-registration.md)，并向[Azure Stack 中心市场](azure-stack-marketplace.md)填充项以提供用户。
