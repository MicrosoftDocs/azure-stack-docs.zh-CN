---
title: 更改隐私设置
description: 本主题提供有关如何在 Azure Stack HCI 操作系统或 Windows Server 中更改隐私设置的指导。
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 10/05/2020
ms.openlocfilehash: 8055e02b702fb5e585b5570171c6dabb247b623f
ms.sourcegitcommit: 79be77eb2bebf314d956258fc9bc14b2014be190
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745138"
---
# <a name="change-privacy-settings-on-individual-servers"></a>更改各个服务器上的隐私设置

>适用于： Azure Stack HCI，版本 20H2;Windows Server 2019、Windows Server 2016

下面介绍如何更改运行 Azure Stack HCI 操作系统或 Windows Server 的服务器上的隐私设置。 若要同时在多台服务器上使用组策略管理隐私设置，请参阅 [管理企业诊断数据](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#manage-enterprise-diagnostic-data)。

## <a name="azure-stack-hci-or-server-core"></a>Azure Stack HCI 或服务器核心
如果服务器使用 Azure Stack HCI 操作系统或使用服务器核心安装选项的 Windows Server，请使用以下步骤：
1. 通过使用远程桌面、远程管理 (无外设或 BMC) 控制器、KVM 来连接到 Azure Stack HCI 群集中的服务器，或者使用键盘和监视器在本地登录。 
1. 如果连接到运行 Azure Stack HCI 的服务器，则会自动打开 (Sconfig.cmd) 的服务器配置工具。 如果连接到运行 Windows Server 的服务器核心服务器，请在命令提示符处输入 `Sconfig` 。
1. 在 " **输入一个数字以选择选项：** prompt" 中键入 **10** ，然后按 enter。
1. 在 **更改遥测** 确认提示符下，选择 **"是"** 以显示以下选项：

    可用遥测设置： **1 个安全**， **2 个基本**，3个 **增强**， **4 个完整**

    >[!NOTE]
    > Azure Stack HCI 的默认设置为 **1 个安全性**。

1. 在 " **输入新遥测" 设置：** "提示符" 下，键入所需的选项，然后按 enter。

## <a name="full-desktop"></a>完整桌面
如果服务器正在运行 Windows Server 并具有完整的桌面安装选项，请使用以下步骤：
1. 连接到 Windows Server 的服务器管理器面板。

    你可以通过使用键盘和监视器或使用远程管理 (无外设或 BMC) 控制器或远程桌面来进行本地连接。 

1. 在 **仪表板**下的 "服务器管理" 中，选择 " **本地服务器**"。
1. 在服务器的 " **属性** " 页上，在 " **反馈 & 诊断**" 旁边，选择 " **设置**"。

    在 " **设置** " 页上，将显示 " **反馈频率** " 和 " **诊断和使用情况数据** " 设置。 
 
1. 展开 " **诊断和使用情况数据** " 设置，选择下列选项之一：
    - **必需的诊断数据**
    - **增强版**
    - **可选诊断数据**

    >[!NOTE]
    > 在 " **设置** " 页上，如果发现 **某些设置由组织管理** ，则 " **重点讨论和使用情况数据** " 设置可能不可用。

## <a name="next-steps"></a>后续步骤
若要同时在多台服务器上使用组策略管理隐私设置，请参阅：
-   [管理企业诊断数据](/windows/privacy/configure-windows-diagnostic-data-in-your-organization#manage-enterprise-diagnostic-data)
