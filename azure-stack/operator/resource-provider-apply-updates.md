---
title: 如何将更新应用到 Azure Stack 中心资源提供程序。
description: 了解如何在 Azure Stack 中心向资源提供程序应用服务更新。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/18/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/18/2019
ms.openlocfilehash: 5ec6f85fc2f2e526c4b85521337ab65dc2b939cc
ms.sourcegitcommit: 519f4298dc1ed5c33f9c4fef811f61d61731dd84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799860"
---
# <a name="how-to-update-an-azure-stack-hub-resource-provider"></a>如何更新 Azure Stack 中心资源提供程序

> [!IMPORTANT]
> 继续之前，请务必查看资源提供程序的最新发行说明，以了解新功能、修复程序以及可能影响部署的任何已知问题。 发行说明还可以指定资源提供程序所需的最低 Azure Stack 集线器版本。

从 Marketplace 安装的资源提供程序需要定期维护。 维护是通过应用服务更新来完成的，由 Microsoft 提供。 更新可以包括新功能和修补程序。  

## <a name="check-for-updates"></a>检查更新

资源提供程序使用用于应用 Azure Stack 中心更新的相同更新功能进行更新。

1. 登录到 Azure Stack Hub 管理员门户。
2. 选择左侧的 "**所有服务**" 链接，然后在 "**管理**" 部分下选择 "**更新**"。
   ![所有服务页](media/resource-provider-apply-updates/1-all-services.png)

3. 在 "**更新**" 页上，可以在 "**资源提供程序**" 部分下找到资源提供程序的更新。

   [![更新可用页面](media/resource-provider-apply-updates/3-update-available.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

## <a name="apply-an-update"></a>应用更新

如果更新可用于给定的资源提供程序：

1. 选择要更新的资源提供程序的行。 请注意，页面顶部的 "**下载**" 链接将处于启用状态。
   [![更新可用页面](media/resource-provider-apply-updates/4-download.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

2. 单击 "**下载**" 链接，开始下载资源提供程序安装包。 请注意，资源提供程序行的 "**状态**" 列从 "可用" 更改为 "正在下载"。
3. 当**状态**更改为 "已准备好安装" 时，下载完成。 请注意，页面顶部的 "**立即安装**" 链接也变为启用状态。
4. 选择 "**立即安装**" 链接，然后转到资源提供程序的 "**安装**" 页。 
5. 选择 "**安装**" 按钮开始安装。
6. 右上角会显示 "安装正在进行" 通知，你将返回到 "**更新**" 页面。 资源提供程序行**状态**列还会更改为 "正在安装"。
7. 安装完成后，将显示另一个通知，指示成功或失败。 如果安装成功，还会更新**Marketplace 管理-资源提供程序**页面上的**版本**。

## <a name="next-steps"></a>后续步骤

了解有关[管理员仪表板更新功能](azure-stack-apply-updates.md)的详细信息。
