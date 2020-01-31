---
title: 下载并提取 ASDK
description: 了解如何下载和提取 Azure Stack 开发工具包（ASDK）。
author: justinha
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 08/10/2018
ms.openlocfilehash: 99c43848af1d140c6698e1f4d1ae992665c0389a
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873832"
---
# <a name="download-and-extract-the-asdk"></a>下载并提取 ASDK
在确保开发工具包主机满足安装 Azure Stack 开发工具包（ASDK）的基本要求后，下一步是下载并提取 ASDK 部署包以获取 Cloudbuilder.vhdx。

## <a name="download-the-asdk"></a>下载 ASDK
1. 开始下载之前，请确保计算机满足以下先决条件：

   - 除了操作系统磁盘之外，计算机还必须具有至少 60 GB 的可用磁盘空间，在四个单独的逻辑硬盘上可用。
   - 必须安装[.NET Framework 4.6 （或更高版本）](https://dotnet.microsoft.com/download/dotnet-framework-runtime/net46) 。

2. [转到 "入门" 页](https://azure.microsoft.com/overview/azure-stack/try/?v=try)，您可以在其中下载 ASDK，提供您的详细信息，然后单击 "**提交**"。
3. 下载并运行 ASDK 先决条件检查程序脚本的[部署检查器](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409)。 此独立脚本将完成 ASDK 安装程序执行的先决条件检查。 它提供一种方法来确认你在为 ASDK 下载更大的包之前满足硬件和软件要求。
4. 在 "**下载软件**" 下，单击**Azure Stack 开发工具包**。

   > [!NOTE]
   > ASDK 下载（Azurestackdevelopmentkit.exe）约10GB。

## <a name="extract-the-asdk"></a>提取 ASDK
1. 下载完成后，单击 "**运行**" 以启动 ASDK （azurestackdevelopmentkit.exe）。
2. 在自解压缩程序向导的 "**许可协议**" 页中查看并接受显示的许可协议，然后单击 "**下一步**"。
3. 查看在自解压程序向导的 "**重要通知**" 页上显示的隐私声明信息，然后单击 "**下一步**"。
4. 在自解压缩向导的 "**选择目标位置**" 页上选择要提取 Azure Stack 安装程序文件的位置，然后单击 "**下一步**"。 默认位置是*当前文件夹*\Azure Stack 开发工具包。 
5. 查看自动提取程序向导的 "**准备提取**" 页上的 "目标位置摘要"，然后单击 "**提取**" 以提取 cloudbuilder.vhdx （大约28GB）和 ThirdPartyLicenses 文件。 此过程需要一些时间才能完成。
6. 将 Cloudbuilder.vhdx 文件复制或移动到 C：\ 的根目录ASDK 主计算机上的驱动器（`C:\CloudBuilder.vhdx`）。

> [!NOTE]
> 提取文件后，可以删除。EXE 和。用于恢复硬盘空间的 BIN 文件。 或者，你可以备份这些文件，这样，如果你需要重新部署 ASDK，则无需再次下载这些文件。


## <a name="next-steps"></a>后续步骤
[准备 ASDK 主机计算机](asdk-prepare-host.md)