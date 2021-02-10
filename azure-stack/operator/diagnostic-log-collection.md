---
title: 诊断日志收集
description: 了解诊断日志收集。
author: PatAltimore
ms.topic: article
ms.date: 02/03/2021
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 02/03/2021
ms.openlocfilehash: ad5f0a7f6028249dba3d63490cdc3c91d7a45e72
ms.sourcegitcommit: 69c700a456091adc31e4a8d78e7a681dfb55d248
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2021
ms.locfileid: "100013211"
---
# <a name="diagnostic-log-collection"></a>诊断日志收集

可以共享 Azure Stack 集线器创建的诊断日志。 这些日志由 Windows 组件和本地 Azure 服务创建。 Microsoft 支持部门可以使用日志来修复或确定 Azure Stack 中心实例的问题。

若要开始使用 Azure Stack 集线器诊断日志集合，你必须注册你的实例。 如果尚未注册 Azure Stack Hub，请使用[特权终结点 (PEP)](azure-stack-get-azurestacklog.md) 来共享日志。 

::: moniker range=">= azs-2005"

可以通过多种方式将诊断日志发送到 Microsoft 支持部门。 根据与 Azure 的连接，选项包括：
* [主动发送日志（建议）](#send-logs-proactively)
* [立即发送日志](#send-logs-now)
* [在本地保存日志](#save-logs-locally)

流程图显示了用于发送诊断日志的选项。 如果 Azure Stack 中心连接到 Azure，请启用 **主动日志收集**。 当引发关键警报时，主动日志收集会自动将诊断日志上传到 Microsoft 控制的存储 blob。 还可以通过使用 " **立即发送日志**" 按需收集日志。 对于在断开连接的环境中运行的 Azure Stack 集线器，或者如果遇到连接问题，请选择 **将日志保存在本地**。

![流程图，显示如何将日志立即发送到 Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

::: moniker-end

## <a name="send-logs-proactively"></a>主动发送日志

在你提出支持案例之前，主动日志收集会自动收集 Azure Stack 集线器中的诊断日志并将其发送给 Microsoft。 仅当出现系统运行状况警报时才收集这些日志，并且仅在支持案例的上下文中 Microsoft 支持部门才会对其进行访问。

::: moniker range=">= azs-2008"

从 Azure Stack Hub 版本 2008 开始，主动收集日志的功能使用改进的算法，即使在操作员看不到的错误情况下也可以捕获日志。 这样可以确保在适当的时间收集正确的诊断信息，无需操作员进行任何交互。 在某些情况下，Microsoft 支持人员可以更快地开始进行故障排除并解决问题。 初始算法改进侧重于修补升级操作。

Azure Stack 中心收集警报和其他隐藏失败事件的日志，这些事件对你不可见。

Azure Stack 中心为以下内容主动收集日志：

- 更新失败。
- 更新需要注意。

当事件触发这些警报时，Azure Stack 集线器会主动将日志发送给 Microsoft。

此外，Azure Stack 集线器会将日志发送给 Microsoft，由其他失败事件触发。 这些事件对你不可见。

建议启用主动收集日志的功能，因为更多的操作已经过优化，好处更多。

::: moniker-end

随时可以禁用和重新启用主动日志收集。 按照以下步骤设置主动收集日志功能。

1. 登录到 Azure Stack Hub 管理员门户。
1. 打开“帮助 + 支持概述”。
1. 如果出现横幅，请选择“启用主动收集日志功能”。 也可以选择“设置”，将“主动收集日志”设置为“启用”，然后选择“保存”   。

> [!NOTE]
> 如果为本地文件共享配置了日志位置设置，请确保生命周期管理策略会阻止共享存储达到其容量配额。 Azure Stack Hub 不会监视本地文件共享或强制执行任何保留策略。   

### <a name="how-the-data-is-handled"></a>数据处理方式

仅根据 Azure Stack 集线器系统运行状况警报，由 Microsoft 同意定期自动收集日志。 你还可以在 Microsoft 管理和控制的 Azure 存储帐户中确认并同意这些日志的上传和保留。

这些数据只用于排查系统运行状况警报问题，在未经你同意的情况下不会用于市场营销、广告或任何其他商业目的。 数据最多可保留90天，Microsoft 收集的任何数据都将按照我们的 [标准隐私做法](https://privacy.microsoft.com/)进行处理。

如果你不再同意，则以前在你同意的情况下收集的任何数据都不会受到影响。

使用 **前瞻性日志** 收集收集的日志将上传到由 Microsoft 管理和控制的 Azure 存储帐户。 Microsoft 可能会在支持案例的上下文中访问这些日志，并改善 Azure Stack 集线器的运行状况。

## <a name="send-logs-now"></a>立即发送日志

> [!TIP]
> 通过使用[主动发送日志](#send-logs-proactively)而不是“立即发送日志”来节省时间。

"立即发送日志" 是一种选项，在这种情况下，你可以从 Azure Stack 中心手动收集和上载诊断日志（通常是在打开支持案例之前）。

可以通过两种方式将诊断日志手动发送到 Microsoft 支持部门：
* [管理员门户（建议）](#send-logs-now-with-the-administrator-portal)
* [PowerShell](#send-logs-now-with-powershell)

如果 Azure Stack 中心连接到 Azure，我们建议使用管理员门户，因为它是直接将日志发送到 Microsoft 的最简单方法。 如果该门户不可用，则应使用 PowerShell 发送日志。

### <a name="send-logs-now-with-the-administrator-portal"></a>通过管理员门户立即发送日志

通过管理员门户立即发送日志：

1. 打开“帮助 + 支持”>“日志收集”>“立即发送日志”。 
1. 指定日志收集的开始时间和结束时间。 
1. 选择本地时区。
1. 选择“收集并上传”。

如果你从 internet 断开连接或只想要在本地保存日志，请使用 [get-azurestacklog](azure-stack-get-azurestacklog.md) 方法发送日志。

### <a name="send-logs-now-with-powershell"></a>通过 PowerShell 立即发送日志

如果使用“立即发送日志”方法并想要使用 PowerShell 而不是管理员门户，则可使用 `Send-AzureStackDiagnosticLog` cmdlet 来收集和发送特定日志。

* 可以使用 **FromDate** 和 **ToDate** 参数来收集特定时间段的日志。 如果未指定这些参数，则默认收集过去四小时的日志。

* 使用 **FilterByNode** 参数按计算机名筛选日志。 例如：

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByNode azs-xrp01
  ```

* 使用 **FilterByLogType** 参数按类型筛选日志。 可以选择按文件、共享或 WindowsEvent 进行筛选。 例如：

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByLogType File
  ```

* 使用 FilterByResourceProvider 参数为增值资源提供程序 (RP) 发送诊断日志。 常规语法为：
 
  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider <<value-add RP name>>
  ```

  ::: moniker range=">= azs-2008"

  为 SQL RP 发送诊断日志： 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider SQLAdapter
  ```
  为 MySQL RP 发送诊断日志： 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider MySQLAdapter
  ```
  
  ::: moniker-end

  为 IoT 中心发送诊断日志： 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider IotHub
  ```
 
  为事件中心发送诊断日志：

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider eventhub
  ```
 
  为 Azure Stack Edge 发送诊断日志：

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvide databoxedge
  ```

* 使用 FilterByRole 参数从 VirtualMachines 和 BareMetal 角色发送诊断日志：

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal
  ```

* 从 VirtualMachines 和 BareMetal 角色发送诊断日志，通过日期筛选功能筛选出过去 8 小时的日志文件：

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* 从 VirtualMachines 和 BareMetal 角色发送诊断日志，通过日期筛选功能筛选出 8 小时前到 2 小时前这个时间段的日志文件：

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

> [!NOTE]
> 如果已断开与 Internet 的连接，或者只想在本地保存日志，请使用 [Get-AzureStackLog](azure-stack-get-azurestacklog.md) 方法发送日志。 

### <a name="how-the-data-is-handled"></a>数据处理方式

通过从 Azure Stack 中心发起诊断日志收集，你确认并同意上载这些日志，并将其保留在 Microsoft 管理并控制的 Azure 存储帐户中。 Microsoft 支持部门可以通过支持案例立即访问这些日志，而无需与客户进行日志收集。

::: moniker range=">= azs-2005"

## <a name="save-logs-locally"></a>在本地保存日志

当 Azure Stack Hub 与 Azure 断开连接时，可以将日志保存到本地服务器消息块 (SMB) 共享。 例如，你可以运行断开连接的环境。 如果正常连接，但遇到连接问题，则可以在本地保存日志以帮助进行故障排除。

 在“设置”边栏选项卡中，输入具有共享写入权限的路径、用户名和密码。 在支持案例中，Microsoft 支持部门将提供有关如何传输这些本地日志的详细步骤。 如果管理员门户不可用，则可以使用 [Get-AzureStackLog](azure-stack-get-azurestacklog.md) 在本地保存日志。

![诊断日志收集选项的屏幕截图](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end

## <a name="bandwidth-considerations"></a>带宽注意事项

进行诊断日志收集时日志的平均大小各不相同，具体取决于它是主动运行还是手动运行。 “主动收集日志”选项对应的日志平均大小约为 2 GB。 “立即发送日志”选项对应的收集大小取决于需要收集多少小时。

下表列出了在以受限或计量方式连接到 Azure 时的环境注意事项。

| 网络连接 | 影响 |
|----|---|
| 低带宽/高延迟连接 | 完成日志上传的时间会延长。 |
| 共享连接 | 上传也可能影响共享网络连接的其他应用/用户。 |
| 计量连接 | 您的 ISP 可能还会收取额外的网络使用量。 |

## <a name="view-log-collection"></a>查看日志集合

以往从 Azure Stack Hub 收集的日志显示在“帮助 + 支持”中的“日志收集”页上，其中提供了以下日期和时间 ：

- **收集的时间**：日志收集操作的开始时间。
- **状态**：“正在进行”或“已完成”。
- **日志开始**：要收集日志的时段的开始时间。
- **日志结束**：收集时段的结束时间。
- **类型**：是手动收集日志还是主动收集日志。

![“帮助 + 支持”中的日志集合](media/azure-stack-help-and-support/azure-stack-log-collection.png)

## <a name="see-also"></a>另请参阅

[Azure Stack Hub 日志和客户数据处理](./azure-stack-data-collection.md)
