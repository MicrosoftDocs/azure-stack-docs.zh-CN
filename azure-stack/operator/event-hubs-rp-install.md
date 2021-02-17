---
title: 如何在 Azure Stack Hub 上安装事件中心
description: 了解如何在 Azure Stack Hub 上安装事件中心资源提供程序。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/02/2020
ms.reviewer: jfggdl
ms.lastreviewed: 09/02/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 7f20866236099dadc7e6ca3e3c201572f51069ca
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562488"
---
# <a name="how-to-install-event-hubs-on-azure-stack-hub"></a>如何在 Azure Stack Hub 上安装事件中心

本文介绍如何下载和安装事件中心资源提供程序，使其可供客户订阅。 你还必须先完成 [事件中心安装先决条件](event-hubs-rp-prerequisites.md) ，然后再继续。

## <a name="download-packages"></a>下载包

必须先使用市场管理功能下载资源提供程序及其相关的包，然后才能在 Azure Stack Hub 上安装事件中心。 如果不熟悉市场管理，请花费一些时间阅读[从 Azure 下载市场项并将其发布到 Azure Stack Hub](azure-stack-download-azure-marketplace-item.md)。 本部分逐步讲解如何从 Azure 市场下载项目。 

> [!NOTE]
> 下载过程可能需要 30 分钟到 2 小时的时间，具体取决于网络延迟和 Azure Stack Hub 实例上的现有包。 

::: zone pivot="state-connected"
对于联网场景，可以将项目从 Azure 市场直接下载到 Azure Stack Hub 市场：

1. 登录到 Azure Stack Hub 管理员门户。
2. 选择左侧的“市场管理”。
3. 选择“资源提供程序”。
4. 选择“+ 从 Azure 添加”。
5. 使用搜索栏搜索“事件中心”。
6. 在搜索结果中选择“事件中心”行。 
7. 在“事件中心”下载页上，选择要安装的事件中心版本，然后选择页面底部的“下载”。 
   [![市场管理 - 包](media/event-hubs-rp-install/1-marketplace-management-download.png)](media/event-hubs-rp-install/1-marketplace-management-download.png#lightbox)

请注意，其他软件包会随事件中心一起下载，其中包括：

- Microsoft Azure Stack 中心 Add-On RP Windows Server 仅限内部
- PowerShell 所需状态配置
::: zone-end

::: zone pivot="state-disconnected"
对于离线场景或部分联网场景，请将包下载到本地计算机，然后将其导入到 Azure Stack Hub 市场：

1. 如果尚未这样做，请按照[下载市场项 - 离线或部分联网的场景](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected)中的说明进行操作。 其中，你将下载并运行“市场联合”工具，该工具用于下载“事件中心”包。
2. 在该联合工具的“Azure 市场项”窗口打开后，查找并选择“事件中心”，将所需的包下载到本地计算机。
3. 下载完成后，将包导入到 Azure Stack Hub 实例，然后发布到市场。 
::: zone-end

## <a name="installation"></a>安装 

1. 如果尚未登录，请登录到 Azure Stack Hub 管理员门户，选择左侧的“市场管理”，然后选择“资源提供程序” 。
2. 下载了“事件中心”和所需的其他软件后，“市场管理”会显示状态为“未安装”的“事件中心”包。 可能还有状态显示为“已下载”的其他包。 选择要安装的“事件中心”行。
   [![市场管理 - 下载的包](media/event-hubs-rp-install/2-marketplace-management-downloaded.png)](media/event-hubs-rp-install/2-marketplace-management-downloaded.png#lightbox)
 
3. “事件中心”安装包页面会在顶部显示一条蓝色横幅。 选择该横幅，开始安装事件中心。
   [![“市场管理”中的“事件中心”- 开始安装](media/event-hubs-rp-install/3-marketplace-management-install-ready.png)](media/event-hubs-rp-install/3-marketplace-management-install-ready.png#lightbox)

### <a name="install-prerequisites"></a>安装必备组件

1. 接下来，你将转到“安装”页。 选择“安装必备组件”开始安装过程。
   ![“市场管理”中的“事件中心”- 安装必备组件](media/event-hubs-rp-install/4-marketplace-management-install-prereqs-start.png)
 
2. 等到必备组件安装成功。 在继续下一步之前，你应该会在“安装必备组件”旁边看到一个绿色的复选标记。

   ![“市场管理”中的“事件中心”- 安装必备组件成功](media/event-hubs-rp-install/5-marketplace-management-install-prereqs-succeeded.png)

### <a name="prepare-secrets"></a>准备机密 

1. 在“2. 准备机密”步骤下，选择“添加证书”，此时会显示“添加证书”面板。
   ![“市场管理”中的“事件中心”- 准备机密](media/event-hubs-rp-install/6-marketplace-management-install-prepare-secrets.png)

2. 选择“添加证书”上的“浏览”按钮（紧邻证书文件名字段的右侧）。
3. 选择在完成先决条件时获取的 .pfx 证书文件。 有关详细信息，请参阅[安装先决条件](event-hubs-rp-prerequisites.md)。 

4. 输入你在为事件中心 SSL 证书创建安全字符串时提供的密码。 然后选择“添加”。
   ![“市场管理”中的“事件中心”- 添加证书](media/event-hubs-rp-install/7-marketplace-management-install-prepare-secrets-add-cert.png)

### <a name="install-resource-provider"></a>安装资源提供程序

1. 证书安装成功后，在继续下一步之前，你应该会在“准备密钥”旁边看到一个绿色的复选标记。 现在，选择“3 安装资源提供程序”下面的“安装”按钮。
   ![“市场管理”中的“事件中心”- 开始 RP 安装](media/event-hubs-rp-install/8-marketplace-management-install-start.png)
 
2. 接下来，你会看到以下页面，其中指示正在安装事件中心资源提供程序。
   [![“市场管理”中的“事件中心”- RP 安装](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png)](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png#lightbox)
 
3. 等待安装完成通知。 此过程通常要花费一小时或更长时间，具体取决于你的 Azure Stack Hub 类型。 
   [![“市场管理”中的“事件中心”- RP 安装完成](media/event-hubs-rp-install/10-marketplace-management-install-complete.png)](media/event-hubs-rp-install/10-marketplace-management-install-complete.png#lightbox)

4. 返回到“市场管理”的“资源提供程序”页，验证是否已成功安装事件中心。 事件中心的状态应显示为“已安装”。
   ![“市场管理”中的“事件中心”- 可用](media/event-hubs-rp-install/11-marketplace-management-rps-installed.png)

## <a name="next-steps"></a>后续步骤

你必须先创建一个或多个计划、套餐和订阅，然后用户才能部署事件中心资源。 

- 如果这是你第一次提供服务，请从[向用户提供服务](tutorial-offer-services.md)教程开始。 然后继续学习下一教程[测试服务产品](tutorial-test-offer.md)。
- 熟悉提供服务的概念后，请创建包含事件中心资源提供程序的套餐和计划。 然后为你的用户创建订阅，或为其提供套餐信息，以便他们可以创建自己的订阅。 如需参考信息，还可以阅读[服务、计划、套餐、订阅概述](service-plan-offer-subscription-overview.md)下的一系列文章。

若要查找更新，请阅读[如何更新 Azure Stack Hub上的事件中心](resource-provider-apply-updates.md)。

如果需要删除资源提供程序，请参阅[删除事件中心资源提供程序](event-hubs-rp-remove.md)

若要了解有关用户体验的详细信息，请访问用户文档中的 [Azure Stack Hub 上的事件中心概述](../user/event-hubs-overview.md)。