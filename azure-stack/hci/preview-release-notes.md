---
title: Azure Stack HCI 发行说明存档
description: Azure Stack HCI，版本20H2 公共预览版的存档发行说明。
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/21/2020
ms.openlocfilehash: 5125e9b27fbb9cc17e9381a9617fefce6409418f
ms.sourcegitcommit: a745662c7a5a18f135accf3f70d8508b57e83e2b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2020
ms.locfileid: "97737940"
---
# <a name="archived-release-notes-for-azure-stack-hci-version-20h2-public-preview"></a>Azure Stack HCI 的存档发行说明，版本20H2 公共预览版

> 适用于：Azure Stack HCI，版本 20H2

本文介绍了 Azure Stack HCI 版本的更新包的内容， (正式发布之前的版本20H2 公共预览版发布) 2020 年12月10日发布。 你还可以查看 [AZURE STACK HCI 的当前发行说明](https://support.microsoft.com/help/4595086/)。

## <a name="december-8-2020-security-update-kb4592441"></a>2020年12月8日 (KB4592441) 的安全更新

此更新包括 Azure Stack HCI 的公共预览版本的改进和修补程序。 

### <a name="improvements-and-fixes"></a>改进和修复
此更新包含对内部 OS 功能的各种安全改进。 它通过阻止作为系统帐户运行的应用程序从打印到 "FILE：" 端口来解决安全漏洞。 若要在将来解决此问题，请确保你的应用程序或服务以特定用户或服务帐户的身份运行。

此版本没有其他已知问题。

有关已解决的安全漏洞的详细信息，请参阅[安全更新指南](https://portal.msrc.microsoft.com/security-guidance)。

### <a name="known-issues-in-this-update"></a>此更新中的已知问题
Microsoft 目前未了解到任何有关此更新的问题。

### <a name="how-to-get-this-update"></a>如何获取此更新
[AZURE STACK HCI preview](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)的2020年12月8日安全更新 (KB4592441) 通过 Windows 更新提供。 若要在 Azure Stack HCI 群集上安装该安全更新，请参阅[更新 Azure Stack HCI 群集](manage/update-cluster.md)。

### <a name="file-information"></a>文件信息
有关此更新中提供的文件的列表 (OS Build 17784.1408) ，请下载 [累积更新4592441的文件信息](https://download.microsoft.com/download/2/7/2/272ea75f-1657-43ce-a7a4-a17d51463a94/4592441.csv)。

   > [!NOTE]
   > 某些文件在 CSV 文件的“文件版本”列中错误地显示为“不适用”。 当使用某些第三方扫描检测工具验证生成时，这可能会导致假正或误报。

## <a name="november-23-2020-preview-update-kb4586852"></a>2020 年 11 月 23 日预览版更新 (KB4586852)

此更新包括 Azure Stack HCI 的公共预览版本的改进和修补程序。 

   > [!IMPORTANT]
   > 如果已使用公共预览版映像配置并注册了 Azure Stack HCI 群集，则在安装 KB4586852 更新后必须修复你的 Azure 注册，才能使用该更新提供的新功能。 安装更新后，针对每个群集执行以下步骤：
   >
   > 1. 确保群集中的所有服务器都已更新到 KB4586852。 如果没有，则修复会失败，并指示需要进行更新的节点。
   >
   > 2. 通过本地方式或 `Enter-PSSession <server-name>` 连接到群集节点之一
   >
   > 3. 从 PowerShell 库下载 AzStackHCI v 0.4.1 (或更高版本) 注册模块。 运行 `Install-Module -Name Az.StackHCI` 来获取最新模块。
   >
   > 4. 运行以下命令来修复你的注册。 请使用最初用来注册群集的订阅 ID。 `Get-AzureStackHCI` 显示当前 Azure 资源管理器 Uri，其中包含订阅信息。
   >
   >   ```PowerShell
   >   Register-AzStackHCI -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -RepairRegistration
   >   ```
   > 

### <a name="improvements-and-fixes"></a>改进和修复
此非安全更新包括质量改进。 关键更改包括： 

- 有了此更新，持有有效的 Windows Server 2019 Datacenter 版本许可证的 Azure Stack HCI 客户可以使用这些许可证方便地激活在 Azure Stack HCI 上托管的虚拟机 (VM)，而无需管理每个 VM 的产品密钥。 具体而言，你可以使用 Windows Admin Center 或 PowerShell 将未使用的 Windows Server 2019 Datacenter Edition 激活密钥直接输入到 Azure Stack HCI 主机，以启用 VM 自动激活 (AVMA)。 然后，运行 Windows Server 2019 或更低版本的 VM 可以从主机继承激活。 输入的密钥可以是从批量许可中心获取的多次激活密钥 (MAK)、OEM 服务器上粘贴的真品证书 (COA) 不干胶标签上打印的密钥，或者是 Windows Server 2019 Datacenter Edition 零售盒装副本的密钥。 此版本不支持通用批量许可密钥 (GVLK)。

- Azure Stack HCI 现在会收集必需的诊断数据，这是确保设备安全、保持最新状态且性能符合预期所需的最少数据。 所需的诊断数据将收集有限的数据集，这些数据对于了解设备及其配置至关重要。 此数据可帮助查明特定硬件或软件配置中可能出现的问题。  

### <a name="known-issues-in-this-update"></a>此更新中的已知问题
Microsoft 目前未了解到任何有关此更新的问题。

### <a name="how-to-get-this-update"></a>如何获取此更新 
[Azure Stack HCI 预览版](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)的 2020 年 11 月 23 日安全更新 (KB4586852) 通过 Windows 更新交付。 若要在 Azure Stack HCI 群集上安装该安全更新，请参阅[更新 Azure Stack HCI 群集](manage/update-cluster.md)。

### <a name="file-information"></a>文件信息
有关此更新中提供的文件的列表 (OS Build 17784.1381) ，请下载 [累积更新4586852的文件信息](https://download.microsoft.com/download/5/c/6/5c6f8c37-3e0b-4239-a6d9-9c709e18e869/4586852.csv)。

   > [!NOTE]
   > 某些文件在 CSV 文件的“文件版本”列中错误地显示为“不适用”。 当使用某些第三方扫描检测工具验证生成时，这可能会导致假正或误报。

## <a name="november-10-2020-security-update-kb4586811"></a>2020 年 11 月 10 日安全更新 (KB4586811)

此更新包括 Azure Stack HCI 的公共预览版本的改进和修补程序。

### <a name="improvements-and-fixes"></a>改进和修复
此更新包含对内部 OS 功能的各种安全改进。 它通过阻止以 SYSTEM 帐户身份运行的应用程序将内容输出到指向某个文件的本地端口，解决了一个安全漏洞。 失败的打印作业日志错误50，在 PrintService\Admin 事件日志中，事件 ID 372 中的 "请求不受支持"。 若要在将来解决此问题，请确保你的应用程序或服务以特定用户或服务帐户的身份运行。

此版本没有其他已知问题。

有关已解决的安全漏洞的详细信息，请参阅[安全更新指南](https://portal.msrc.microsoft.com/security-guidance)。

### <a name="known-issues-in-this-update"></a>此更新中的已知问题
Microsoft 目前未了解到任何有关此更新的问题。

### <a name="how-to-get-this-update"></a>如何获取此更新
[AZURE STACK HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)的2020年11月10日安全更新 (KB4586811) 通过 Windows 更新提供。 若要在 Azure Stack HCI 群集上安装该安全更新，请参阅[更新 Azure Stack HCI 群集](manage/update-cluster.md)。

### <a name="file-information"></a>文件信息
有关此更新（OS 内部版本 17784.1345）中提供的文件的列表，请下载[累积更新 4586811 的文件信息](https://download.microsoft.com/download/8/f/2/8f2ce4bb-e113-4abc-b3ff-f0f4c4c71403/4586811.csv)。

   > [!NOTE]
   > 某些文件在 CSV 文件的“文件版本”列中错误地显示为“不适用”。 当使用某些第三方扫描检测工具验证生成时，这可能会导致假正或误报。

## <a name="november-10-2020-servicing-stack-update-kb4590242"></a>2020 年 11 月 10 日服务堆栈更新 (KB4590242)

此更新包括 Azure Stack HCI 的公共预览版本的质量改进。

### <a name="improvements-and-fixes"></a>改进和修复
此更新对服务堆栈（安装更新的组件）进行质量改进。 服务堆栈更新 (SSU) 确保你具有强大可靠的服务堆栈，以便你的设备可以接收并安装 Microsoft 更新。

### <a name="how-to-get-this-update"></a>如何获取此更新
[AZURE STACK HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)的11月份2020服务堆栈更新 (KB4590242) 通过 Windows 更新提供。 若要在 Azure Stack HCI 群集上安装该安全更新，请参阅[更新 Azure Stack HCI 群集](manage/update-cluster.md)。

### <a name="restart-information"></a>“重启”信息
应用此更新后，无需重启计算机。

### <a name="removal-information"></a>“删除”信息
服务堆栈更新 (SSU) 对安装更新的方式进行了更改，并且无法从设备中卸载。

### <a name="file-information"></a>文件信息
有关此更新（OS 内部版本 17784.1342）中提供的文件的列表，请下载[累积更新 4590242 的文件信息](https://download.microsoft.com/download/b/b/4/bb4fb4f5-c0ba-4e55-bada-d72310857982/4590242.csv)。

### <a name="references"></a>参考

有关 SSU 的信息，请查看以下文章：

- [服务堆栈更新](/windows/deployment/update/servicing-stack-updates)
- [服务堆栈更新 (SSU)：常见问题解答](https://support.microsoft.com/help/4535697)

了解 Microsoft 用于描述软件更新的[术语](https://support.microsoft.com/help/824684)。

## <a name="october-20-2020-preview-update-kb4580388"></a>2020 年 10 月 20 日预览版更新 (KB4580388)

此更新包括 Azure Stack HCI 的公共预览版本的改进和修补程序。

### <a name="improvements-and-fixes"></a>改进和修复
此非安全更新包括质量改进。 关键更改包括：

- 通过此更新，持有有效 Windows Server 2019 Datacenter 版本许可证的 Azure Stack HCI 客户可以使用这些许可证方便地激活在 Azure Stack HCI 上托管的虚拟机 (VMs)，而无需管理每个 VM 的产品密钥。

### <a name="known-issues-in-this-update"></a>此更新中的已知问题

Microsoft 意识到此更新存在一个问题。

#### <a name="symptom"></a>症状
使用实时迁移在 Windows Server 和 Azure Stack HCI 操作系统之间移动 VM 时，可能会看到以下错误：“已阻止虚拟机 <vmname> 的迁移操作，因为不支持不同 Windows 版本之间的 VM 迁移（虚拟机 ID）。”

此外，这也可能导致群集感知更新 (CAU) 操作失败（如果任何 VM 应在 CAU 期间执行实时迁移）。

#### <a name="workaround"></a>解决方法

使用快速迁移而不是实时迁移。 如果使用 CAU，请暂时更改默认行为，使 CAU 使用快速迁移。

例如：

```powershell
Get-ClusterResourceType "Virtual Machine" | Set-ClusterParameter MoveTypeThreshold 3001
```

建议在 CAU 成功完成后还原为以前的 `MoveTypeThreshold` 值。

有关详细信息，请参阅[配置在排出节点时如何移动 VM](https://techcommunity.microsoft.com/t5/failover-clustering/configuring-how-vms-are-moved-when-a-node-is-drained/ba-p/371848)。

### <a name="how-to-get-this-update"></a>如何获取此更新
[AZURE STACK HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)的2020年10月20日安全更新 (KB4580388) 通过 Windows 更新提供。 若要在 Azure Stack HCI 群集上安装该安全更新，请参阅[更新 Azure Stack HCI 群集](manage/update-cluster.md)。

### <a name="file-information"></a>文件信息
有关此更新（OS 内部版本 17784.1321）中提供的文件列表，请下载[累积更新 4580388 的文件信息](https://download.microsoft.com/download/2/f/b/2fb766d3-c4c8-4279-8718-8efbd0b6f211/4580388.csv)。

   > [!NOTE]
   > 某些文件在 CSV 文件的“文件版本”列中错误地显示为“不适用”。 当使用某些第三方扫描检测工具验证生成时，这可能会导致假正或误报。

## <a name="october-13-2020-security-update-kb4580363"></a>2020 年 10 月 13 日安全更新 (KB4580363)

此更新包括 Azure Stack HCI 的公共预览版本的改进和修补程序。

### <a name="improvements-and-fixes"></a>改进和修复
此更新包含对内部 OS 功能的各种安全改进。 此版本没有其他已知问题。

有关已解决的安全漏洞的详细信息，请参阅[安全更新指南](https://portal.msrc.microsoft.com/security-guidance)。

### <a name="known-issues-in-this-update"></a>此更新中的已知问题
Microsoft 目前未了解到任何有关此更新的问题。

### <a name="how-to-get-this-update"></a>如何获取此更新
[AZURE STACK HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)的2020年10月13日安全更新 (KB4580363) 通过 Windows 更新提供。 若要在 Azure Stack HCI 群集上安装该安全更新，请参阅[更新 Azure Stack HCI 群集](manage/update-cluster.md)。

### <a name="file-information"></a>文件信息
有关此更新（OS 内部版本 17784.1288）中提供的文件列表，请下载[累积更新 4580363 的文件信息](https://download.microsoft.com/download/f/7/8/f78801f0-e7e5-4a3d-a971-f642ccd24ee4/4580363.csv)。

   > [!NOTE]
   > 某些文件在 CSV 文件的“文件版本”列中错误地显示为“不适用”。 当使用某些第三方扫描检测工具验证生成时，这可能会导致假正或误报。

## <a name="october-13-2020-servicing-stack-update-kb4583287"></a>2020 年 10 月 13 日服务堆栈更新 (KB4583287)

此更新包括 Azure Stack HCI 的公共预览版本的质量改进。

### <a name="improvements-and-fixes"></a>改进和修复
此更新对服务堆栈（安装更新的组件）进行质量改进。 服务堆栈更新 (SSU) 确保你具有强大可靠的服务堆栈，以便你的设备可以接收并安装 Microsoft 更新。

### <a name="how-to-get-this-update"></a>如何获取此更新
2020年10月13日 [AZURE STACK HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) (KB4583287) 通过 Windows 更新提供。 若要在 Azure Stack HCI 群集上安装该安全更新，请参阅[更新 Azure Stack HCI 群集](manage/update-cluster.md)。

### <a name="restart-information"></a>“重启”信息
应用此更新后，无需重启计算机。

### <a name="removal-information"></a>“删除”信息
服务堆栈更新 (SSU) 对安装更新的方式进行了更改，并且无法从设备中卸载。

### <a name="file-information"></a>文件信息
有关此更新（OS 内部版本 17784.1287）中提供的文件列表，请下载[累积更新 4583287 的文件信息](https://download.microsoft.com/download/b/8/5/b85160fb-85d9-49f9-b9d5-7dbc0158a944/4583287.csv)。

### <a name="references"></a>参考

有关 SSU 的信息，请查看以下文章：

- [服务堆栈更新](/windows/deployment/update/servicing-stack-updates)
- [服务堆栈更新 (SSU)：常见问题解答](https://support.microsoft.com/help/4535697)

了解 Microsoft 用于描述软件更新的[术语](https://support.microsoft.com/help/824684)。

## <a name="september-17-2020-preview-update-kb4577629"></a>2020 年 9 月 17 日预览版更新 (KB4577629)

此更新包括 Azure Stack HCI 的公共预览版本的改进和修补程序。

### <a name="improvements-and-fixes"></a>改进和修复
此非安全更新包括质量改进。 关键更改包括：
- 解决了下面的问题：流过多路复用器的软件负载均衡器 (SLB) 流量可能会重定向到可能导致应用程序连接失败的另一个主机。

### <a name="known-issues-in-this-update"></a>此更新中的已知问题
Microsoft 目前未了解到任何有关此更新的问题。

### <a name="how-to-get-this-update"></a>如何获取此更新
[AZURE STACK HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)的2020年9月17日安全更新 (KB4577629) 通过 Windows 更新提供。 若要在 Azure Stack HCI 群集上安装该安全更新，请参阅[更新 Azure Stack HCI 群集](manage/update-cluster.md)。

### <a name="file-information"></a>文件信息
有关此更新（OS 内部版本 17784.1259）中提供的文件列表，请下载[累积更新 4577629 的文件信息](https://download.microsoft.com/download/9/1/a/91addcbb-2b36-408c-ab88-736de42edb98/4577629.csv)

   > [!NOTE]
   > 某些文件在 CSV 文件的“文件版本”列中错误地显示为“不适用”。 当使用某些第三方扫描检测工具验证生成时，这可能会导致假正或误报。

## <a name="september-8-2020-security-update-kb4577470"></a>2020 年 9 月 8 日安全更新 (KB4577470)

此更新包括 Azure Stack HCI 的公共预览版本的改进和修补程序。

### <a name="improvements-and-fixes"></a>改进和修复
此更新包含对内部 OS 功能的各种安全改进。 此版本没有其他已知问题。

有关已解决的安全漏洞的详细信息，请参阅[安全更新指南](https://portal.msrc.microsoft.com/security-guidance)。

### <a name="known-issues-in-this-update"></a>此更新中的已知问题
Microsoft 目前未了解到任何有关此更新的问题。

### <a name="how-to-get-this-update"></a>如何获取此更新
[AZURE STACK HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)的2020年9月8日安全更新 (KB4577470) 通过 Windows 更新提供。 若要在 Azure Stack HCI 群集上安装该安全更新，请参阅[更新 Azure Stack HCI 群集](manage/update-cluster.md)。

### <a name="file-information"></a>文件信息
有关此更新（OS 内部版本 17784.1226）中提供的文件列表，请下载[累积更新 4577470 的文件信息](https://download.microsoft.com/download/3/c/4/3c468525-5867-4cc3-8d34-dba88989adab/4577470.csv)。

   > [!NOTE]
   > 某些文件在 CSV 文件的“文件版本”列中错误地显示为“不适用”。 当使用某些第三方扫描检测工具验证生成时，这可能会导致假正或误报。

## <a name="september-8-2020-servicing-stack-update-kb4577558"></a>2020 年 9 月 8 日服务堆栈更新 (KB4577558)

此更新包括 Azure Stack HCI 的公共预览版本的质量改进。

### <a name="improvements-and-fixes"></a>改进和修复
此更新对服务堆栈（安装更新的组件）进行质量改进。 服务堆栈更新 (SSU) 确保你具有强大可靠的服务堆栈，以便你的设备可以接收并安装 Microsoft 更新。

### <a name="how-to-get-this-update"></a>如何获取此更新
2020年9月8日 [AZURE STACK HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/) (KB4577558) 通过 Windows 更新提供。 若要在 Azure Stack HCI 群集上安装该安全更新，请参阅[更新 Azure Stack HCI 群集](manage/update-cluster.md)。

### <a name="restart-information"></a>“重启”信息 
应用此更新后，无需重启计算机。

### <a name="removal-information"></a>“删除”信息
服务堆栈更新 (SSU) 对安装更新的方式进行了更改，并且无法从设备中卸载。

### <a name="file-information"></a>文件信息
有关此更新（OS 内部版本 17784.1220）中提供的文件列表，请下载[累积更新 4577558 的文件信息](https://download.microsoft.com/download/8/f/6/8f612a9b-cb4e-4832-9397-156760848592/4577558.csv)。

### <a name="references"></a>参考

有关 SSU 的信息，请查看以下文章：

- [服务堆栈更新](/windows/deployment/update/servicing-stack-updates)
- [服务堆栈更新 (SSU)：常见问题解答](https://support.microsoft.com/help/4535697)

了解 Microsoft 用于描述软件更新的[术语](https://support.microsoft.com/help/824684)。

## <a name="august-11-2020-security-update-kb4574585"></a>2020 年 8 月 11 日安全更新 (KB4574585)

此更新包括 Azure Stack HCI 的公共预览版本的改进和修补程序。

### <a name="improvements-and-fixes"></a>改进和修复
此更新包含对内部 OS 功能的各种安全改进。 此版本没有其他已知问题。

有关已解决的安全漏洞的详细信息，请参阅[安全更新指南](https://portal.msrc.microsoft.com/security-guidance)。

### <a name="known-issues-in-this-update"></a>此更新中的已知问题
Microsoft 目前未了解到任何有关此更新的问题。

### <a name="how-to-get-this-update"></a>如何获取此更新
[AZURE STACK HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)的2020年8月11日安全更新 (KB4574585) 通过 Windows 更新提供。 若要在 Azure Stack HCI 群集上安装该安全更新，请参阅[更新 Azure Stack HCI 群集](manage/update-cluster.md)。

### <a name="file-information"></a>文件信息
有关此更新（OS 内部版本 17784.1162）中提供的文件列表，请下载[累积更新 4574585 的文件信息](https://download.microsoft.com/download/7/f/4/7f451def-76c5-4cc0-9929-0c5efeb27d2f/4574585.csv)。

   > [!NOTE]
   > 某些文件在 CSV 文件的“文件版本”列中错误地显示为“不适用”。 当使用某些第三方扫描检测工具验证生成时，这可能会导致假正或误报。
