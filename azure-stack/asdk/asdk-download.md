---
title: 下载并提取 ASDK |Microsoft Docs
description: 了解如何下载和提取 Azure Stack 开发工具包 (ASDK)。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 08/10/2018
ms.openlocfilehash: a4d0258d2a7a5168c94159d9eae13605b1269b86
ms.sourcegitcommit: 4eb1766c7a9d1ccb1f1362ae1211ec748a7d708c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69579121"
---
# <a name="download-and-extract-the-asdk"></a>下载并提取 ASDK
在确保开发工具包主机满足安装 Azure Stack 开发工具包 (ASDK) 的基本要求后, 下一步是下载并提取 ASDK 部署包以获取 Cloudbuilder.vhdx。

## <a name="download-the-asdk"></a>下载 ASDK
1. 开始下载之前，请确保计算机满足以下先决条件：

   - 除了操作系统磁盘之外, 计算机还必须具有至少 60 GB 的可用磁盘空间, 在四个单独的逻辑硬盘上可用。
   - 必须已安装 [.NET Framework 4.6（或更高版本）](https://dotnet.microsoft.com/download/dotnet-framework-runtime/net46)。

2. [转到 "入门" 页](https://azure.microsoft.com/overview/azure-stack/try/?v=try), 您可以在其中下载 ASDK, 提供您的详细信息, 然后单击 "**提交**"。
3. 下载并运行 ASDK 先决条件检查程序脚本的[部署检查器](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409)。 此独立脚本将完成 ASDK 安装程序执行的先决条件检查。 它提供一种方法来确认你在为 ASDK 下载更大的包之前满足硬件和软件要求。
4. 在“下载软件”下单击“Azure Stack 开发工具包”。

   > [!NOTE]
   > ASDK 下载项 (AzureStackDevelopmentKit.exe) 大约为 10GB。

## <a name="extract-the-asdk"></a>提取 ASDK
1. 下载完成后，请单击“运行”，启动 ASDK 自解压缩程序 (AzureStackDevelopmentKit.exe)。
2. 查看并接受自解压缩程序向导的“许可协议”页中显示的许可协议，然后单击“下一步”。
3. 查看在自解压程序向导的 "**重要通知**" 页上显示的隐私声明信息, 然后单击 "**下一步**"。
4. 在自解压缩程序向导的“选择目标位置”页上选择要将 Azure Stack 安装程序文件提取到其中的位置，然后单击“下一步”。 默认位置为：当前文件夹\Azure Stack Development Kit。 
5. 查看自解压缩程序向导的“准备提取”页上的目标位置摘要，然后单击“提取”以提取 CloudBuilder.vhdx（约 28GB）和 ThirdPartyLicenses.rtf 文件。 此过程需要一些时间才能完成。
6. 将 Cloudbuilder.vhdx 文件复制或移动到 C:\ 的根目录ASDK 主`C:\CloudBuilder.vhdx`计算机上的驱动器 ()。

> [!NOTE]
> 提取这些文件以后，即可通过删除 .EXE 和 .BIN 文件来恢复硬盘空间。 也可备份这些文件，这样当你需要重新部署 ASDK 时，就不需再次下载这些文件。


## <a name="next-steps"></a>后续步骤
[准备 ASDK 主机](asdk-prepare-host.md)