---
title: Azure Stack 中心集成系统的部署工作表
description: 了解如何安装和使用 "部署工作表" 工具部署 Azure Stack 中心。
author: IngridAtMicrosoft
ms.topic: article
ms.date: 04/19/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 04/19/2019
ms.openlocfilehash: 8c13121f5d591abca8a6c83771848d97cc9106db
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700062"
---
# <a name="deployment-worksheet-for-azure-stack-hub-integrated-systems"></a>Azure Stack 中心集成系统的部署工作表

Azure Stack 中心部署工作表是一个 Windows 窗体应用程序，它在一个位置聚合所有必需的部署信息和决策。 您可以在规划过程中完成部署工作表，并在部署开始之前查看它。

工作表所需的信息涵盖网络、安全性和标识信息。 它需要在多个不同区域中可能需要了解的重要决策;因此，你可能需要咨询这些领域中具有专业技能的团队，以便完成工作表。

填写工作表时，可能需要对网络环境进行一些预部署配置更改。 这可能包括保留 Azure Stack 中心解决方案的 IP 地址空间，以及配置路由器、交换机和防火墙，以便为连接到新的 Azure Stack 中心解决方案做好准备。

> [!NOTE]
> 有关如何完成 "部署工作表" 工具的详细信息，请参阅[Azure Stack 中心文档中的这篇文章](azure-stack-datacenter-integration.md)。

[![部署工作表](media/azure-stack-deployment-worksheet/depworksheet.png "部署工作表")](media/azure-stack-deployment-worksheet/depworksheet.png)

## <a name="installing-the-windows-powershell-module"></a>安装 Windows PowerShell 模块

对于每个版本的部署工作表，必须为要在其上使用 "部署" 工作表的每台计算机执行一次 Powershell 模块安装。

> [!NOTE]  
> 计算机必须连接到 internet 才能使用此方法。

1. 打开提升权限的 PowerShell 提示符。

2. 在 PowerShell 窗口中，从[powershell 库](https://www.powershellgallery.com/packages/Azs.Deployment.Worksheet/)安装模块：

   ```PowerShell
   Install-Module -Name Azs.Deployment.Worksheet -Repository PSGallery
   ```

如果收到有关从不受信任的存储库中安装的消息，请按**Y**继续安装。

## <a name="use-the-deployment-worksheet-tool"></a>使用 "部署工作表" 工具

若要在已安装部署工作表 PowerShell 模块的计算机上启动和使用部署工作表，请执行以下步骤：

1. 启动 Windows PowerShell （请勿使用 PowerShell ISE，因为可能会发生意外的结果）。 不需要以管理员身份运行 PowerShell。

2. 导入**AzS** PowerShell 模块：

   ```PowerShell
   Import-Module AzS.Deployment.Worksheet
   ```

3. 导入模块后，启动部署工作表：

   ```PowerShell
   Start-DeploymentWorksheet
   ```

部署工作表包含单独的选项卡，用于收集环境设置，如**客户设置**、**网络设置**和**缩放单位号**。 必须在所有选项卡上提供所有值（任何标记为**可选**的除外），然后才能生成任何配置数据文件。 在该工具中输入所有必需值后，您可以使用 "**操作**" 菜单**导入**、**导出**和**生成**。 部署所需的 JSON 文件如下所示：

**导入**：使你可以导入此工具生成的 Azure Stack 中心配置数据文件（ConfigurationData），或部署工作表的任何以前版本创建的数据文件。 执行导入将重置窗体，并删除以前输入的任何设置或生成的数据。

**导出**：验证当前输入到窗体中的数据，生成 IP 子网和分配，然后将内容另存为 JSON 格式的配置文件。 然后，你可以使用这些文件来生成网络配置并安装 Azure Stack Hub。

**生成**：验证当前输入的数据并生成网络映射，而不导出部署 JSON 文件。 如果**生成**成功，则会创建两个新选项卡： "**子网摘要**" 和 " **IP 分配**"。 你可以分析这些选项卡上的数据以确保网络分配按预期方式进行。

**全部清除**：清除当前在窗体中输入的所有数据并将其返回到默认值。

**保存或打开正在进行的工作**：可以在使用**文件 > 保存**和**文件 > 打开**菜单时，保存并打开部分输入的数据。 这不同于**导入**和**导出**功能，因为这需要输入并验证所有数据。 打开/保存不会进行验证，并且不需要输入所有字段即可保存正在进行的工作。

**日志记录和警告消息**：在使用窗体时，可能会看到在 PowerShell 窗口中显示非严重警告消息。 严重错误显示为弹出消息。 可以启用可选详细日志记录（包括写入磁盘的日志），以帮助解决问题。

若要启动具有详细日志记录的工具：

   ```PowerShell
   Start-DeploymentWorksheet -EnableLogging
   ```

可以在当前用户的**临时**目录中找到保存的日志;例如： **c:\users\me\appdata\local\temp\ Microsoft_AzureStack \ DeploymentWorksheet_Log。**

## <a name="next-steps"></a>后续步骤

* [Azure Stack 中心部署连接模型](azure-stack-connection-models.md)
