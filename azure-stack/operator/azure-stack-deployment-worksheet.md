---
title: 部署工作表适用于 Azure Stack 集成系统 |Microsoft Docs
description: 了解如何安装和部署工作表工具用于部署 Azure Stack。
services: azure-stack
documentationcenter: ''
author: wamota
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2019
ms.author: wamota
ms.reviewer: wamota
ms.lastreviewed: 04/19/2019
ms.openlocfilehash: d75915f110b6316f4621f66b1f91b010f735d165
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172672"
---
# <a name="deployment-worksheet-for-azure-stack-integrated-systems"></a>部署 Azure Stack 集成系统的工作表

Azure Stack 部署工作表是聚合了所有必需的部署信息和在一个位置的决策的 Windows 窗体应用程序。 您可以在规划过程中，完成部署工作表并查看它在部署开始之前。

工作表所需的信息包括网络、 安全性和标识信息。 它要求可能需要在许多不同方面; 中的知识的重要决策因此，你可能想要咨询团队拥有在这些领域的专业知识，若要完成该工作表。

时填写该工作表时，可能需要对您的网络环境进行一些部署前的配置更改。 这可能包括保留 Azure Stack 解决方案的 IP 地址空间和配置路由器、 交换机和防火墙，若要为连接到新的 Azure Stack 解决方案做准备。

> [!NOTE]
> 有关如何完成部署工作表工具的详细信息，请参阅[中的 Azure Stack 文档的此文章](azure-stack-datacenter-integration.md)。

[![部署工作表](media/azure-stack-deployment-worksheet/depworksheet.png "部署工作表")](media/azure-stack-deployment-worksheet/depworksheet.png)

## <a name="installing-the-windows-powershell-module"></a>安装 Windows PowerShell 模块

对于每个版本的部署工作表，必须执行一个 Powershell 模块，用于你想要使用的部署工作表的每台计算机的一次性安装。

> [!NOTE]  
> 计算机必须连接到 internet，此方法来工作。

1. 打开提升的 PowerShell 命令提示符。

2. 在 PowerShell 窗口中，安装的模块[PowerShell 库](https://www.powershellgallery.com/packages/Azs.Deployment.Worksheet/):

   ```PowerShell
   Install-Module -Name Azs.Deployment.Worksheet -Repository PSGallery
   ```

如果你将收到有关不受信任的存储库中安装，请按**Y**才能继续安装。

## <a name="use-the-deployment-worksheet-tool"></a>使用部署工作表工具

若要启动并已安装的部署工作表 PowerShell 模块的计算机上使用的部署工作表，请执行以下步骤：

1. 启动 Windows PowerShell （不使用 PowerShell ISE 中，可能会出现意外的结果）。 不需要以管理员身份运行 PowerShell。

2. 导入**AzS.Deployment.Worksheet** PowerShell 模块：

   ```PowerShell
   Import-Module AzS.Deployment.Worksheet
   ```

3. 一旦导入模块时，启动部署工作表：

   ```PowerShell
   Start-DeploymentWorksheet
   ```

部署工作表包含，如收集环境设置单独的选项卡**客户设置**，**网络设置**，并**缩放单位 #**。 必须提供所有值 (任何标记除外**可选**) 之前的任何配置的所有选项卡上可以生成数据文件。 别忘了所需的值均已输入到该工具，可以使用**操作**到菜单**导入**，**导出**，以及**生成**。 部署所需的 JSON 文件如下所示：

**导入**：可以导入已生成的此工具，或创建的部署工作表的所有以前版本的 Azure Stack 配置数据文件 (ConfigurationData.json)。 执行导入窗体将重置和删除任何以前输入的设置或生成的数据。

**导出**：验证当前输入到窗体的数据，生成的 IP 子网和分配，然后将内容保存为 JSON 格式的配置文件。 然后，您可以使用这些文件生成的网络配置并安装 Azure Stack。

**生成**:验证当前输入的数据并生成在网络映射，而不导出部署 JSON 文件。 如果创建两个新选项卡**生成**成功：**子网摘要**并**IP 分配**。 你可以分析以确保按预期的网络分配的这些选项卡上的数据。

**清除所有**:清除当前窗体中输入的所有数据并将它们返回到默认值。

**保存或打开你在编写**:可以保存和打开部分输入的数据，您处理它，使用**文件-> 另存**并**文件-> 打开**菜单。 这不同于**导入**并**导出**函数，因为这些需要输入和验证的所有数据。 打开/保存不会验证，并且不需要所有字段以输入要保存正在进行的工作。

**日志记录和警告消息**:使用窗体时，可能会看到 PowerShell 窗口中显示的非关键警告消息。 严重错误显示为弹出消息。 可选的详细日志记录，包括日志写入到磁盘，可以启用以帮助解决问题。

若要使用详细日志记录启动该工具：

   ```PowerShell
   Start-DeploymentWorksheet -EnableLogging
   ```

您可以在当前用户的发现已保存的日志**Temp**目录; 例如：**C:\Users\me\AppData\Local\Temp\Microsoft_AzureStack\DeploymentWorksheet_Log.txt**.

## <a name="next-steps"></a>后续步骤

* [Azure Stack 部署连接模型](azure-stack-connection-models.md)
