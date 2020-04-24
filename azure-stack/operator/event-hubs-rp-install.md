---
title: 如何在 Azure Stack 集线器上安装事件中心
description: 了解如何在 Azure Stack 集线器上安装事件中心资源提供程序。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: e07d311c8edbe140834a020af489ae49d8380d86
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "80423967"
---
# <a name="how-to-install-event-hubs-on-azure-stack-hub"></a>如何在 Azure Stack 集线器上安装事件中心

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

本文介绍如何下载和安装事件中心资源提供程序，使其可用于订阅的客户。

## <a name="download-packages"></a>下载包

必须先下载资源提供程序及其相关的包，然后才能在 Azure Stack 集线器上安装事件中心。 你有两个选项，具体取决于你的具体情况还是要求：

- 在连接的情况下下载事件中心。
- 在断开连接或部分连接的情况下下载事件中心。

如果你不熟悉 Azure Stack 中心管理员门户的**Marketplace 管理**功能，请花时间查看[从 Azure 下载 Marketplace 项并发布到 Azure Stack 中心](azure-stack-download-azure-marketplace-item.md)的时间。 本文将指导你完成将项从 Azure 下载到 Azure Stack 中心应用程序的过程。 它涵盖连接和断开连接的方案。 

### <a name="download-event-hubs---connected-scenario"></a>下载事件中心连接的方案

> [!NOTE]
> 下载过程可能需要30分钟到2小时的时间，具体取决于 Azure Stack 中心实例上的网络延迟和现有包。 

如果 Azure Stack 集线器具有 Internet 连接，请按照以下说明操作：

1. 登录到 Azure Stack Hub 管理员门户。
2. 选择左侧的 " **Marketplace 管理**"。
3. 选择“资源提供程序”****。
4. 选择“+ 从 Azure 添加”。****
5. 使用搜索栏搜索 "事件中心"。
6. 在搜索结果中选择 "事件中心" 行。 
7. 在 "事件中心" 下载页上，选择要安装的事件中心版本，然后选择页面底部的 "**下载**"。 
   [![Marketplace 管理包](media/event-hubs-rp-install/1-marketplace-management-download.png)](media/event-hubs-rp-install/1-marketplace-management-download.png#lightbox)

请注意，会随事件中心一起下载其他软件包，其中包括：

- Microsoft Azure Stack 中心外接程序 RP 仅限内部 Windows Server
- PowerShell Desired State Configuration

下载过程完成后，请跳到 "[安装必备组件" 部分](#install-prerequisites)。

### <a name="download-event-hubs---disconnected-or-partially-connected-scenario"></a>下载事件中心-断开连接或部分连接的方案

首先，将包下载到本地计算机，然后将它们导入 Azure Stack 中心实例。

1. 如果尚未这样做，请按照[下载 Marketplace 项-断开连接或部分连接的方案](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario)中的说明进行操作。 在此处下载并运行 Marketplace 联合工具，该工具允许你下载事件中心包。
2. 在联合工具的 "Azure Marketplace 项" 窗口打开后，查找并选择 "事件中心"，将所需的包下载到你的本地计算机。
3. 下载完成后，将包导入到 Azure Stack 中心实例，并发布到 Marketplace。 

## <a name="installation"></a>安装 

1. 如果尚未登录，请登录到 Azure Stack 中心管理员门户。
2. 选择左侧的 " **Marketplace 管理**"，并选择 "**资源提供程序**"。
3. 下载事件中心和其他所需的软件后， **Marketplace 管理**应显示状态为 "未安装" 的 "事件中心" 包。 可能存在显示 "已下载" 状态的其他包。 选择要安装的 "事件中心" 行。
   [![Marketplace 管理下载的包](media/event-hubs-rp-install/2-marketplace-management-downloaded.png)](media/event-hubs-rp-install/2-marketplace-management-downloaded.png#lightbox)
 
4. "事件中心安装包" 页面应在顶部显示蓝色横幅。 选择标题，开始安装事件中心。
   [![Marketplace 管理事件中心-开始安装](media/event-hubs-rp-install/3-marketplace-management-install-ready.png)](media/event-hubs-rp-install/3-marketplace-management-install-ready.png#lightbox)

### <a name="install-prerequisites"></a>安装先决条件

1. 接下来，你将转到 "安装" 页。 选择 "**安装必备组件**" 以开始安装过程。
   ![Marketplace 管理事件中心-先决条件](media/event-hubs-rp-install/4-marketplace-management-install-prereqs-start.png)
 
2. 等到必备组件安装成功。 在继续下一步之前，你应该看到 "**安装先决条件**" 旁边的绿色复选标记。

   ![Marketplace 管理事件中心-先决条件](media/event-hubs-rp-install/5-marketplace-management-install-prereqs-succeeded.png)

### <a name="prepare-secrets"></a>准备机密 

1. 位于**2。准备密钥**步骤，选择 "**添加证书**"，随即将显示 "**添加证书**" 面板。
   ![Marketplace 管理事件中心-准备机密](media/event-hubs-rp-install/6-marketplace-management-install-prepare-secrets.png)

2. 选择 "**将证书添加**到证书文件" 字段右侧的 "浏览" 按钮。
3. 选择在完成先决条件时购买的 .pfx 证书文件。 有关详细信息，请参阅[安装先决条件](event-hubs-rp-prerequisites.md)。 

4. 输入提供的密码，为事件中心 SSL 证书创建安全字符串。 然后选择“添加”  。
   ![Marketplace 管理事件中心-添加证书](media/event-hubs-rp-install/7-marketplace-management-install-prepare-secrets-add-cert.png)

### <a name="install-resource-provider"></a>安装资源提供程序

1. 证书安装成功后，在继续下一步之前，你应该会看到 "**准备机密**" 旁边的绿色复选标记。 现在，选择 " **3 安装资源提供程序**" 旁边的 "**安装**" 按钮。
   ![Marketplace 管理事件中心-开始安装](media/event-hubs-rp-install/8-marketplace-management-install-start.png)
 
2. 接下来，你会看到以下页面，指示正在安装事件中心资源提供程序。
   [![Marketplace 管理事件中心-安装](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png)](media/event-hubs-rp-install/9-marketplace-management-install-inprogress.png#lightbox)
 
3. 等待安装完成通知。 此过程通常要花费一小时或更长时间，具体取决于你的 Azure Stack 中心类型。 
   [![Marketplace 管理事件中心-安装完成](media/event-hubs-rp-install/10-marketplace-management-install-complete.png)](media/event-hubs-rp-install/10-marketplace-management-install-complete.png#lightbox)

4. 返回到 " **Marketplace 管理**"、"**资源提供程序**" 页，验证是否已成功安装事件中心。 事件中心的状态应显示为 "已安装"。
   ![Marketplace 管理事件中心可用](media/event-hubs-rp-install/11-marketplace-management-rps-installed.png)

## <a name="register-event-hubs"></a>注册事件中心

现在，你需要注册事件中心资源提供程序。 注册使你可以使用 "事件中心管理" 页来管理服务。

1. 在管理员门户中，选择左上角的 "**所有服务**"。
2. 选择 "**订阅**"。 你会看到一个订阅列表。 
   > [!NOTE]
   > 请确保不要选择**用户订阅**
3. 选择 "**订阅**" 页上的 "**默认提供程序订阅**"。
4. 在 "**默认提供程序订阅**" 页面左侧选择 "**资源提供程序**"。
5. 在顶部的 "**按名称筛选**" 字段中，搜索字符串 "EventHub"。
6. 查看 "Microsoft EventHub" 和 "node.js" 资源提供程序行的 "**状态**" 列。
7. 如果其中任何一个状态为 "未注册"，请选择每个提供程序，然后选择 "**注册**"。 
   ![未注册的资源提供程序](media/event-hubs-rp-install/12-default-subscription-rps-unregistered.png)
8. 几秒钟后，请选择 "**刷新**"。 此时，你应该会看到资源提供程序的状态为 "已注册"。 
9. 现在，应会看到 "Microsoft EventHub" 和 "Microsoft"，其状态为 "已注册"。
   ![已注册资源提供程序](media/event-hubs-rp-install/13-default-subscription-rps-registered.png)

10. 返回 "**所有服务**" 页。
11. 搜索 "事件中心"。 现在应会看到 "事件中心"，这是 "事件中心管理" 页的入口点。 
   ![可用服务-事件中心](media/event-hubs-rp-install/14-all-service-event-hubs.png)
 
## <a name="next-steps"></a>后续步骤

必须先创建一个或多个计划、产品/服务和订阅，然后用户才能部署事件中心资源。 

- 如果这是你第一次提供服务，请从[向用户提供服务](tutorial-offer-services.md)教程开始。 然后继续学习下一教程，[测试服务产品](tutorial-test-offer.md)。
- 熟悉提供服务的概念后，请创建包含事件中心资源提供程序的产品/服务和计划。 然后为你的用户创建订阅，或为其提供产品/服务信息，以便他们可以创建自己的订阅。 若要进行参考，还可以遵循[服务、计划、产品/服务、订阅概述](service-plan-offer-subscription-overview.md)中的一系列文章。

若要检查更新，[如何更新 Azure Stack 集线器上的事件中心](resource-provider-apply-updates.md)。

如果需要删除资源提供程序，请参阅[删除事件中心资源提供程序](event-hubs-rp-remove.md)

若要了解有关用户体验的详细信息，请访问用户文档[Azure Stack 集线器概述中的事件中心](../user/event-hubs-overview.md)。