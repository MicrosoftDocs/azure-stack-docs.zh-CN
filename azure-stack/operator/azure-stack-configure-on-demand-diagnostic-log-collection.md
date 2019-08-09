---
title: 立即收集 Azure Stack 诊断日志 |Microsoft Docs
description: 如何在 Azure Stack 帮助和支持中配置按需诊断日志收集。
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: 007703facf882eccd594dfb6f78941570c9cfb02
ms.sourcegitcommit: 8de4c18b25bd1047fc270812a795f24e8f1e9244
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/09/2019
ms.locfileid: "68865907"
---
# <a name="collect-azure-stack-diagnostic-logs-now-on-demand"></a>立即收集 Azure Stack 诊断日志 (按需)

适用对象：*Azure Stack 集成系统*

在故障排除过程中, Microsoft 客户支持服务 (CSS) 可能需要分析诊断日志。 从1907版本开始, Azure Stack 操作员可以使用 "**帮助和支持**" 将按需诊断日志上传到 Azure 中的 blob 容器。 作为替代方法, 如果门户不可用, 操作员可以使用 Get-azurestacklog 通过特权终结点 (PEP) 收集日志。 本主题介绍了按需收集诊断日志的两种方式。

## <a name="using-help-and-support"></a>使用帮助和支持

若要解决某个问题, CSS 可能会请求 Azure Stack 运算符按需收集上一周特定时间范围内的诊断日志。 在这种情况下, CSS 将向操作员提供用于上传集合的 SAS URL。 按照以下步骤使用 CSS 中的 SAS URL 配置按需日志收集:

1. 打开 "**帮助和支持概述**", 然后单击 "**立即收集日志**"。 
1. 选择过去7天的1-4 小时滑动窗口。 
1. 选择本地时区。
1. 输入 CSS 提供的 SAS URL。

   ![按需日志收集的屏幕截图](media/azure-stack-automatic-log-collection/collect-logs-now.png)

>[!NOTE]
>如果启用了自动诊断日志收集, 则 "**帮助和支持**" 将显示 "日志收集正在进行"。 如果单击 "**立即收集日志**" 以从特定时间收集日志, 而在进行自动日志收集时, 按需收集将在自动完成日志收集后开始。 

## <a name="using-pep"></a>使用 PEP

<!--how do you look up the PEP IP address. You look up the azurestackstampinfo.json--->

Azure Stack 诊断工具有助于更轻松、更高效地收集日志。 下图显示了诊断工具的工作方式:

![Azure Stack 诊断工具](media/azure-stack-diagnostics/get-azslogs.png)

### <a name="trace-collector"></a>跟踪收集器

跟踪收集器默认启用，可以在后台持续运行，以便从 Azure Stack 组件服务收集所有 Windows 事件跟踪 (ETW) 日志。 ETW 日志存储在一个常用的本地共享中，其时间限制为五天。 一旦达到此限制，就会在创建新文件时删除最旧的文件。 每个文件默认允许的最大大小为 200 MB。 每 2 分钟进行一次大小检查，如果当前文件 >= 200 MB，则会保存该文件并生成新文件。 按事件会话生成的文件的总大小也存在 8 GB 的限制。

### <a name="get-azurestacklog"></a>Get-AzureStackLog

PowerShell cmdlet Get-azurestacklog 可用于从 Azure Stack 环境中的所有组件收集日志。 此工具将日志以 zip 文件形式保存在用户定义的位置。 如果 Azure Stack 技术支持团队需要你的日志以帮助解决问题, 则他们可能会要求你运行 Get-azurestacklog。

> [!CAUTION]
> 这些日志文件可能包含个人身份信息 (PII)。 在公开发布任何日志文件之前，请考虑到这一因素。

下面是一些收集的示例日志类型：

* **Azure Stack 部署日志**
* **Windows 事件日志**
* **Panther 日志**
* **群集日志**
* **存储诊断日志**
* **ETW 日志**

这些文件由跟踪收集器收集并保存在共享中。 然后, 可以根据需要使用 Get-azurestacklog 来收集它们。

#### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>在 Azure Stack 集成系统上运行 Get-AzureStackLog

若要在集成系统上运行 Get-azurestacklog, 需要有权访问特权终结点 (PEP)。 下面是一个可以通过 PEP 来运行的示例脚本，用于在集成系统上收集日志：

```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session { Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if ($session) {
    Remove-PSSession -Session $session
}
```

#### <a name="run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>在 Azure Stack 开发工具包 (ASDK) 系统上运行 Get-AzureStackLog

使用以下步骤在 ASDK 主机上运行 `Get-AzureStackLog`。

1. 以 **AzureStack\CloudAdmin** 身份登录到 ASDK 主机。
2. 以管理员身份打开一个新的 PowerShell 窗口。
3. 运行 **Get-AzureStackLog** PowerShell cmdlet。

#### <a name="examples"></a>示例

* 收集所有角色的所有日志：

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred
  ```

* 从 VirtualMachines 和 BareMetal 角色收集日志：

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

* 从 VirtualMachines 和 BareMetal 角色收集日志，通过日期筛选功能筛选出过去 8 小时的日志文件：

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* 从 VirtualMachines 和 BareMetal 角色收集日志，通过日期筛选功能筛选出 8 小时前到 2 小时前这个时间段的日志文件：

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

* 收集日志并将其存储在指定的 Azure 存储 blob 容器中。 此操作的常规语法如下所示：

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ```

  例如：

  ```powershell
  Get-AzureStackLog -OutputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>"
  ```

  > [!NOTE]
  > 此过程适用于上载日志。 即使你没有可访问的 SMB 共享或访问 internet, 你也可以在 Azure Stack 上创建 blob 存储帐户, 以传输日志, 然后使用客户端检索这些日志。  

  若要为存储帐户生成 SAS 令牌，需要以下权限：

  * 对 Blob 存储服务的访问权限
  * 对容器资源类型的访问权限

  若要生成要用于 `-OutputSasUri` 参数的 SAS URI 值，请执行以下步骤：

  1. 按照[本文中](/azure/storage/common/storage-quickstart-create-account)的步骤创建存储帐户。
  2. 打开 Azure 存储资源管理器的实例。
  3. 连接到在步骤 1 中创建的存储帐户。
  4. 在**存储服务**中导航到 **Blob 容器**。
  5. 选择“新建容器”。
  6. 右键单击新容器，然后单击“获取共享访问签名”。
  7. 根据需求，选择有效的**开始时间**和**结束时间**。
  8. 根据所需的权限，选择“读取”、“写入”和“列表”。
  9. 选择“创建”。
  10. 你将获得共享访问签名。 复制 URL 部分，并将其提供给 `-OutputSasUri` 参数。

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>ASDK 系统和集成系统的参数考虑事项

* 参数 **OutputSharePath** 和 **OutputShareCredential** 用于将日志存储在用户指定的位置。

* 可以使用 **FromDate** 和 **ToDate** 参数来收集特定时间段的日志。 如果未指定这些参数，则默认收集过去四小时的日志。

* 使用 **FilterByNode** 参数按计算机名筛选日志。 例如：

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```

* 使用 **FilterByLogType** 参数按类型筛选日志。 可以选择按文件、共享或 WindowsEvent 进行筛选。 例如：

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByLogType File
    ```

* 可以使用 **TimeOutInMinutes** 参数设置日志收集的超时。 它默认设置为 150（2.5 小时）。
* 转储文件日志收集默认情况下处于禁用状态。 若要启用它，请使用 **IncludeDumpFile** 开关参数。
* 目前，可以使用 **FilterByRole** 参数按以下角色筛选日志收集：

  |   |   |   |    |     |
  | - | - | - | -  |  -  |
  |ACS                   |CA                             |HRP                            |OboService                |VirtualMachines|
  |ACSBlob               |CacheService                   |IBC                            |OEM                       |WAS            |
  |ACSDownloadService    |计算                        |InfraServiceController         |OnboardRP                 |WASPUBLIC|
  |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE                       |         |
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator   |         | 
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker        |         |
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing                  |         |
  |ACSMonitoringService  |Domain                         |KeyVaultInternalDataPlane      |SeedRingServices          |         |
  |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB                       |         |
  |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL                       |         |
  |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP                       |         |
  |ACSWac                |ExternalDNS                    |MetricsRP                      |存储                   |         |
  |ADFS                  |FabricRing                     |MetricsServer                  |StorageController         |         |
  |ApplicationController |FabricRingServices             |MetricsStoreService            |URP                       |         |
  |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |SupportBridgeController   |         |
  |AzureBridge           |FRP                            |MonRP                          |SupportRing               |         |
  |AzureMonitor          |网关                        |NC                             |SupportRingServices       |         |
  |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |SupportBridgeRP           |         |
  |BRP                   |HintingServiceV2               |NRP                            |UsageBridge               |         |
  |   |   |   |    |     | 

### <a name="additional-considerations"></a>其他注意事项

* 此命令需要一些时间来运行，具体取决于日志收集的角色。 影响因素还包括指定用于日志收集的时限，以及 Azure Stack 环境中的节点数。
* 当日志收集运行时，请查看在 **OutputSharePath** 参数（在命令中指定）中创建的新文件夹。
* 每个角色的日志位于单个 zip 文件中。 根据所收集日志的大小，一个角色的日志可能会拆分成多个 zip 文件。 对于这种角色, 如果想要将所有日志文件解压缩到一个文件夹, 请使用可批量解压缩的工具。 选择角色的所有压缩文件，然后选择“解压缩到此处”。 这样就会将该角色的所有日志文件解压缩到单个合并的文件夹中。
* 在压缩的日志文件所在的文件夹中，还会创建名为 **Get-AzureStackLog_Output.log** 的文件。 此文件是一个命令输出日志，可以用来排查日志收集过程中的问题。 有时，日志文件包含 `PS>TerminatingError` 条目，除非运行日志收集后缺少预期的日志文件，否则可以放心忽略这些条目。
* 调查某个特定的故障时，可能需要多个组件中的日志。

  * 所有基础结构 VM 的系统和事件日志收集在 **VirtualMachines** 角色中。
  * 所有主机的系统和事件日志收集在 **BareMetal** 角色中。
  * 故障转移群集和 Hyper-V 事件日志收集在“存储”角色中。
  * ACS 日志收集在“存储”角色和 **ACS** 角色中。

> [!NOTE]
> 会对收集的日志强制实施大小和保留时间限制，因为必须确保对存储空间进行有效的利用，从而确保该空间不会充斥着日志。 但是，在诊断问题时，有时可能需要某些日志，但这些日志可能因为这些限制而不再存在了。 因此，**强烈建议**每隔 8 到 12 小时就将日志卸载到外部存储空间（Azure 中的存储帐户、其他本地存储设备，等等）并在该处保留 1 - 3 月，具体取决于你的要求。 另外，请确保该存储位置已加密。

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

可以使用 **Invoke-AzureStackOnDemandLog** cmdlet 为某些角色生成按需日志（请参阅本部分末尾的列表）。 默认情况下，执行 **Get-AzureStackLog** cmdlet 时收到的日志捆绑包中不存在此 cmdlet 生成的日志。 此外, 建议仅在 Microsoft 支持团队请求时收集这些日志。

目前，可以使用 `-FilterByRole` 参数按以下角色筛选日志收集：

* OEM
* NC
* SLB
* 网关

#### <a name="example-of-collecting-on-demand-logs"></a>收集按需日志的示例

```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session {
   Invoke-AzureStackOnDemandLog -Generate -FilterByRole "<on-demand role name>" # Provide the supported on-demand role name e.g. OEM, NC, SLB, Gateway
   Get-AzureStackLog -OutputSharePath "<external share address>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate
}

if ($session) {
   Remove-PSSession -Session $session
}
```

