---
title: 将更新应用于 Azure Stack Hub 资源提供程序
description: 了解如何将服务更新应用于 Azure Stack Hub 上的资源提供程序。
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 03/01/2021
ms.reviewer: jfggdl
ms.lastreviewed: 03/01/2021
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 60f273603d03c6625addcfeba345a198f82fbe31
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116999"
---
# <a name="how-to-update-an-azure-stack-hub-resource-provider"></a>如何更新 Azure Stack Hub 资源提供程序

> [!IMPORTANT]
> 在继续之前，请务必查看资源提供程序的最新发行说明，了解新功能、修补程序以及任何可能影响部署的已知问题。 发行说明还可以指定资源提供程序所需的最低 Azure Stack Hub 版本。 如果以前从未安装过资源提供程序，请参阅资源提供程序的先决条件和初始安装说明。

从 Marketplace 安装的资源提供程序将需要定期维护。 维护是通过应用 Microsoft 定期提供的服务更新来完成的。 更新可以同时包含新功能和修补程序。  

## <a name="check-for-updates"></a>检查更新

资源提供程序使用用于应用 Azure 堆栈中心更新的相同更新功能进行更新。

1. 登录到 Azure Stack Hub 管理员门户。
2. 选择左侧的“所有服务”链接，然后在“管理”部分下选择“更新”  。
   [![所有服务页](media/resource-provider-apply-updates/1-all-services.png)](media/resource-provider-apply-updates/1-all-services.png#lightbox)

3. 在 " **更新** " 页上，可以在 " **资源提供程序** " 部分中找到资源提供程序的更新， **状态** 显示为 "可用"。

   [![显示“资源提供程序”部分的屏幕截图。](media/resource-provider-apply-updates/3-update-available.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

## <a name="download-package"></a>下载包

如果更新可用于给定的资源提供程序：

::: zone pivot="state-connected"
1. 从 "**更新**" 页的 "**资源提供程序**" 部分，选择要更新的资源提供程序的行。 请注意，页面顶部的“下载”链接已变为可用状态。
   [![可用更新页](media/resource-provider-apply-updates/4-download.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

2. 单击“下载”链接开始下载资源提供程序安装包。 请注意，资源提供程序行的“状态”列将从“可用”更改为“正在下载”。
3. 当“状态”更改为“准备安装”时，下载已完成。 
::: zone-end

::: zone pivot="state-disconnected" 
[!INCLUDE [prereqs](../includes/resource-provider-va-package-download-disconnected.md)]
::: zone-end

## <a name="apply-an-update"></a>应用更新

下载资源提供程序包后，返回到 "**更新**" 页的 **资源提供程序** 部分：

1. 选择要更新的资源提供程序的行。 此时，状态将显示 "准备安装"，页面顶部的 "**立即安装**" 链接将变为启用 **状态**。
2. 选择“立即安装”链接，然后你将转到资源提供商的“安装”页面 。 
3. 选择“安装”按钮以开始安装。
4. 右上角将显示“正在安装”通知，并且你将返回到“更新”页面。 资源提供程序行“状态”列也会更改为“正在安装”。
5. 安装完成后，会显示另一条通知，指示安装是否成功。 成功的安装还会更新“Marketplace 管理 - 资源提供商”页面上的“版本” 。

## <a name="next-steps"></a>后续步骤

详细了解[管理员仪表板更新功能](azure-stack-apply-updates.md)。
