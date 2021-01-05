---
title: Azure Stack Hub 的部署工作表
description: 了解如何安装和使用部署工作表工具来部署 Azure Stack Hub。
author: PatAltimore
ms.topic: article
ms.date: 04/19/2019
ms.author: patricka
ms.reviewer: wamota
ms.lastreviewed: 04/19/2019
ms.openlocfilehash: afdad104cad67e928e1d6d988138ab1c8555631c
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870879"
---
# <a name="deployment-worksheet-for-azure-stack-hub-integrated-systems"></a>Azure Stack Hub 集成系统的部署工作表

Azure Stack Hub 部署工作表是一个 Windows 窗体应用，可在一个位置聚合所有必要的部署信息和决策。 可以在规划过程中填写部署工作表，并在部署开始之前查看它。

该工作表所需的信息包括网络、安全性和标识信息。 此信息可能需要特定领域的特定知识，因此建议通过咨询专家来完成工作表。

填写工作表时，可能需要对网络环境进行一些部署前的配置更改。 这些更改可能包括保留 Azure Stack Hub 解决方案的 IP 地址空间，以及配置路由器、交换机和防火墙，以便为连接到新的 Azure Stack Hub 解决方案做好准备。

> [!NOTE]
> 有关如何完成部署工作表工具的详细信息，请参阅[有关 Azure Stack Hub 集成系统的数据中心集成规划注意事项](azure-stack-datacenter-integration.md)。

[![Azure Stack Hub 部署的部署工作表](media/azure-stack-deployment-worksheet/depworksheet.png "部署工作表")](media/azure-stack-deployment-worksheet/depworksheet.png)

## <a name="installing-the-windows-powershell-module"></a>安装 Windows PowerShell 模块

对于部署工作表的每个版本，必须针对要使用部署工作表的每台计算机执行一次性的 PowerShell 模块安装。

> [!NOTE]  
> 要正常使用此方法，该计算机必须已连接到 Internet。

1. 打开提升的 PowerShell 命令提示符。

2. 在 PowerShell 窗口中，通过 [PowerShell 库](https://www.powershellgallery.com/packages/Azs.Deployment.Worksheet/)安装该模块：

   ```PowerShell
   Install-Module -Name Azs.Deployment.Worksheet -Repository PSGallery
   ```

如果有消息询问是否要从不受信任的存储库安装，请按 **Y** 键并继续安装。

## <a name="use-the-deployment-worksheet-tool"></a>使用部署工作表工具

若要在安装了部署工作表 PowerShell 模块的计算机上启动并使用部署工作表，请执行以下步骤：

1. 启动 Windows PowerShell（不要使用 PowerShell ISE，否则可能会出现意外的结果）。 不需要以管理员的身份运行 PowerShell。

2. 导入 **AzS.Deployment.Worksheet** PowerShell 模块：

   ```PowerShell
   Import-Module AzS.Deployment.Worksheet
   ```

3. 导入模块后，启动部署工作表：

   ```PowerShell
   Start-DeploymentWorksheet
   ```

部署工作表包含单独的选项卡用于收集环境设置，例如“客户设置”、“网络设置”和“缩放单元 #”  。 必须在所有选项卡上提供所有值（标记为“可选”的值除外），然后才能生成任何配置数据文件。 在工具中输入所有必需的值后，可以使用“操作”菜单来执行“导入”、“导出”和“生成”。    部署所需的 JSON 文件如下：

**导入**：用于导入此工具生成的 Azure Stack Hub 配置数据文件 (ConfigurationData.json)，或任何以前版本的部署工作表创建的文件。 执行导入会重置表格，并删除所有以前输入的设置或生成的数据。

**导出**：验证当前在表格中输入的数据，生成 IP 子网和分配，并将内容保存为 JSON 格式的配置文件。 然后，可以使用这些文件来生成网络配置并安装 Azure Stack Hub。

**生成**：验证当前输入的数据并生成网络映射，但不导出部署 JSON 文件。 如果“生成”成功，将会创建两个新的选项卡：“子网摘要”和“IP 分配”。  可以分析这些选项卡上的数据，以确保网络分配符合预期。

**全部清除**：清除当前在表格中输入的所有数据，并将其恢复为默认值。

**保存或打开正在进行的工作**：可以使用“文件”->“保存”和“文件”->“打开”菜单，来保存和打开工作过程中部分输入的数据 。 此功能不同于“导入”和“导出”功能，因为这些功能要求所有数据均已输入且已验证 。 打开/保存操作不会执行验证，并且不要求输入所有字段即可保存正在进行的工作。

**日志记录和警告消息**：使用表格时，你可能会在 PowerShell 窗口中看到一些非关键的警告消息。 关键错误将以弹出消息的形式显示。 可以启用可选的详细日志记录（包括写入到磁盘的日志），以帮助排查问题。

若要在启用详细日志记录的情况下启动该工具：

   ```PowerShell
   Start-DeploymentWorksheet -EnableLogging
   ```

可以在当前用户的 **Temp** 目录中找到保存的日志；例如：**C:\Users\me\AppData\Local\Temp\Microsoft_AzureStack\DeploymentWorksheet_Log.txt**。

## <a name="next-steps"></a>后续步骤

* [Azure Stack Hub 部署连接模型](azure-stack-connection-models.md)
