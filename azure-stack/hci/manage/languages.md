---
title: 更改 Azure Stack HCI 中的语言
description: 本主题介绍如何在 Azure Stack HCI 操作系统、Windows 10、Windows Admin Center 和 Microsoft Edge 中更改语言。
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: 09245e4498e87896b95df993e23693ae1ed23eaf
ms.sourcegitcommit: a15a0f955bac922cebb7bf90a72384fd84ddfe56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2020
ms.locfileid: "86947327"
---
# <a name="change-languages-in-azure-stack-hci"></a>更改 Azure Stack HCI 中的语言

>适用于：Azure Stack HCI 版本 20H2；Windows Server 2019

你可以在管理电脑、Windows Admin Center、Microsoft Edge 以及所管理服务器上的 Azure Stack HCI 操作系统中更改首选语言。 无论这些其他资源中的语言如何变化，Windows Admin Center 都将保留你选择的语言。

本文介绍了如何在以下资源中更改语言：
- Windows 10
- Windows Admin Center
- Microsoft Edge
- Azure Stack HCI 操作系统中的服务器核心

## <a name="change-the-language-in-the-management-pc"></a>在管理电脑中更改语言
如果你在管理电脑上使用 Windows 10，则可以安装要在 Windows 10、你使用的应用以及你访问的网站中使用的语言包。 你还可以更改键盘语言，并按网站和应用的语言优先顺序设置输入语言。

更改在 Windows 10 或键盘语言中使用的语言不会影响 Windows Admin Center 中的显示语言。

   >[!IMPORTANT]
   > 在 Windows 10 中更改语言后，建议你在注销之前将首选键盘语言设置为默认语言，以避免潜在的键盘输入问题。

若要了解详细信息，请参阅[管理 Windows 10 中的输入和显示语言设置](https://support.microsoft.com/help/4496404/windows-10-manage-the-input-and-display-language)。

## <a name="change-the-language-in-windows-admin-center"></a>在 Windows Admin Center 中更改语言
你可以根据所在位置视需要更改 Windows Admin Center 中的语言以及区域格式。 在 Windows Admin Center 中更改任意这些选项对运行 Windows 10 的管理电脑的语言设置没有影响。

若要在 Windows Admin Center 中更改语言，请执行以下操作：
1. 在“所有连接”窗格上，选择“设置”齿轮图标，然后在“设置”下选择“语言/区域”。
1. 展开“语言”下拉列表以选择你的首选语言，然后在需要时展开“区域格式”下拉列表以选择其他区域。
1. 选择“保存并重新加载”。

    :::image type="content" source="media/languages/language-region.png" alt-text="Windows Admin Center 中的“语言/区域”页面":::

若要了解详细信息，请参阅 [Windows Admin Center 设置](/windows-server/manage/windows-admin-center/configure/settings)。

## <a name="change-the-language-in-microsoft-edge"></a>在 Microsoft Edge 中更改语言
你可以向 Microsoft Edge 添加支持的语言，并在浏览器中重新排列语言首选项。 还可以向浏览器添加外语翻译人员扩展，以获取翻译。

若要了解详细信息，请参阅 [Microsoft Edge 语言支持](/deployedge/microsoft-edge-supported-languages)。

## <a name="change-the-language-in-server-core"></a>在服务器核心中更改语言
如果需要在 Azure Stack HCI 操作系统的服务器核心中更改语言，建议在将服务器加入群集后执行此操作。 你可以将支持的语言包添加到服务器核心，然后将语言和键盘布局更改为你要使用的语言和键盘布局。 还可以使用 Windows PowerShell 命令替代当前的语言和键盘输入方法。

每个语言包都安装在目录 %SystemRoot%\System32\\%Language-ID% 中。 例如，C:\Windows\System32\es-ES 是西班牙语语言包的位置。 每个语言包大约为 50 MB。 如果要安装全部 38 个语言包，则你创建的必需映像的大小约为 2 GB。

若要了解详细信息，请参阅 [Windows 的可用语言](/windows-hardware/manufacture/desktop/available-language-packs-for-windows)。

若要手动获取语言包并将其添加到操作系统中，请执行以下操作：

1. 使用 **DISM / Add-WindowsPackage** 工具将语言包添加到服务器核心。 PowerShell 命令 `Add-WindowsPackage` 等效于此 DISM 可执行文件。

    若要了解详细信息，请参阅[向 Windows 映像中添加语言](/windows-hardware/manufacture/desktop/add-language-packs-to-windows)。

1. 建议添加按需语言功能 (FOD)，以便为所添加的语言启用附加功能，如以下示例所示：

    ```DOS
    dism /online /add-capability /capabilityname:Language.Basic~~~de-de~0.0.1.0
    ```

    若要了解详细信息，请参阅[按需语言和区域功能 (FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod)。

1. 可以使用 PowerShell **Set-WinUILanguageOverride** cmdlet 在当前用户帐户的操作系统中更改 Windows UI 语言。 在下面的示例中，`de-DE` 将德语指定为操作系统中的当前语言设置：

    ```PowerShell
    Set-WinUILanguageOverride de-DE
    ```

    若要了解详细信息，请参阅 [Set-WinUILanguageOverride](/powershell/module/international/set-winuilanguageoverride?view=win10-ps)。

## <a name="next-steps"></a>后续步骤
有关详细信息，请参阅：

- [添加或删除 Azure Stack HCI 群集的服务器](./add-cluster.md)
