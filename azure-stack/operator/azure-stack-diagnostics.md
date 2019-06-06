---
title: Azure Stack 中的诊断
description: 如何收集日志文件以在 Azure Stack 中进行诊断
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 05/29/2019
ms.author: justinha
ms.reviewer: adshar
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: 98ad556bf1b0b5f0297cb7964cd9911a50145496
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691757"
---
# <a name="azure-stack-diagnostics-tools"></a>Azure Stack 诊断工具

Azure Stack 是一个大型集合，其中的组件可以一起工作并互相交互。 所有这些组件会生成自己独特的日志。 这样一来，问题可能就难以诊断，尤其是在错误来自多个交互的 Azure Stack 组件的情况下。

我们的诊断工具有助于确保日志收集机制易用且高效。 下图演示了如何在 Azure Stack 中使用日志收集工具：

![Azure Stack 诊断工具](media/azure-stack-diagnostics/get-azslogs.png)

## <a name="trace-collector"></a>跟踪收集器

跟踪收集器默认启用，可以在后台持续运行，以便从 Azure Stack 组件服务收集所有 Windows 事件跟踪 (ETW) 日志。 ETW 日志存储在一个常用的本地共享中，其时间限制为五天。 一旦达到此限制，就会在创建新文件时删除最旧的文件。 每个文件默认允许的最大大小为 200 MB。 每 2 分钟进行一次大小检查，如果当前文件 >= 200 MB，则会保存该文件并生成新文件。 按事件会话生成的文件的总大小也存在 8 GB 的限制。

## <a name="log-collection-tool"></a>日志集合工具

可以使用 PowerShell cmdlet **Get-AzureStackLog** 从 Azure Stack 环境中的所有组件收集日志。 此工具将日志以 zip 文件形式保存在用户定义的位置。 如果 Azure Stack 技术支持团队需要日志来排查问题，他们可能要求你运行此工具。

> [!CAUTION]
> 这些日志文件可能包含个人身份信息 (PII)。 在公开发布任何日志文件之前，请考虑到这一因素。

下面是一些收集的示例日志类型：

* **Azure Stack 部署日志**
* **Windows 事件日志**
* **Panther 日志**
* **群集日志**
* **存储诊断日志**
* **ETW 日志**

这些文件由跟踪收集器收集并保存在共享中。 然后，可以根据需要使用 **Get-AzureStackLog** PowerShell cmdlet 来收集它们。

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>在 Azure Stack 集成系统上运行 Get-AzureStackLog

若要在集成系统上运行日志收集工具，需访问特权终结点 (PEP)。 下面是一个可以通过 PEP 来运行的示例脚本，用于在集成系统上收集日志：

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)

$shareCred = Get-Credential

$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue

Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

### <a name="run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>在 Azure Stack 开发工具包 (ASDK) 系统上运行 Get-AzureStackLog

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

* 收集日志并将其存储在指定的 Azure 存储 blob 容器。 此操作的常规语法如下所示：

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ```

  例如：

  ```powershell
  Get-AzureStackLog -OutputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>"
  ```

  > [!NOTE]
  > 打开与 Microsoft 支持部门的一种情况并需要将日志上传时，此过程非常有用。 即使 ERCS VM 不具有 internet 访问权限并不一定能够从 ERCS VM 访问的 SMB 共享，您可以传输日志，在 Azure Stack 上创建 blob 存储帐户，然后使用您的客户端检索这些日志并将其上载到 Microsoft。  

  若要生成的存储帐户的 SAS 令牌，都需要以下权限：

  * Blob 存储服务的访问权限
  * 容器资源类型的访问权限

  若要生成 SAS Uri 值要用于`-OutputSasUri`参数，请执行以下步骤：

  1. 创建存储帐户中的步骤[这篇文章中](/azure/storage/common/storage-quickstart-create-account)。
  2. 打开 Azure 存储资源管理器的实例。
  3. 连接到在步骤 1 中创建的存储帐户。
  4. 导航到**Blob 容器**中**存储服务**。
  5. 选择**创建一个新容器**。
  6. 右键单击新的容器，然后单击**获取共享访问签名**。
  7. 选择一个有效**开始时间**并**结束时间**，取决于您的要求。
  8. 对于所需的权限，请选择**读**，**编写**，并**列表**。
  9. 选择“创建”  。
  10. 将获取共享访问签名。 复制的 URL 部分中，并提供到`-OutputSasUri`参数。

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

  |   |   |   |    |
  | - | - | - | -  |
  |ACS                   |CacheService                   |IBC                            |OEM|
  |ACSDownloadService    |计算                        |InfraServiceController         |OnboardRP|
  |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE|
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator|
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker|
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing|
  |ACSMonitoringService  |Domain                         |KeyVaultInternalDataPlane      |SeedRingServices|
  |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB|
  |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL|
  |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP   |
  |ACSWac                |ExternalDNS                    |MetricsRP                      |存储|
  |ADFS                  |FabricRing                     |MetricsServer                  |StorageController   |
  |ApplicationController |FabricRingServices             |MetricsStoreService            |URP   |
  |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |UsageBridge|
  |AzureBridge           |FRP                            |MonRP                          |VirtualMachines   |
  |AzureMonitor          |网关                        |NC                             |WAS|
  |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |WASPUBLIC|
  |BRP                   |HintingServiceV2               |NRP                            |   |
  |CA                    |HRP                            |OboService                     |   |
  |   |   |   |    |

### <a name="additional-considerations"></a>其他注意事项

* 此命令需要一些时间来运行，具体取决于日志收集的角色。 影响因素还包括指定用于日志收集的时限，以及 Azure Stack 环境中的节点数。
* 当日志收集运行时，请查看在 **OutputSharePath** 参数（在命令中指定）中创建的新文件夹。
* 每个角色的日志位于单个 zip 文件中。 根据所收集日志的大小，一个角色的日志可能会拆分成多个 zip 文件。 对于此类角色，如果需要将所有日志文件解压缩到单个文件夹中，请使用可以批量解压缩的工具（例如 7zip）。 选择角色的所有压缩文件，然后选择“解压缩到此处”。  这样就会将该角色的所有日志文件解压缩到单个合并的文件夹中。
* 在压缩的日志文件所在的文件夹中，还会创建名为 **Get-AzureStackLog_Output.log** 的文件。 此文件是一个命令输出日志，可以用来排查日志收集过程中的问题。 有时，日志文件包含 `PS>TerminatingError` 条目，除非运行日志收集后缺少预期的日志文件，否则可以放心忽略这些条目。
* 调查某个特定的故障时，可能需要多个组件中的日志。

  * 所有基础结构 VM 的系统和事件日志收集在 **VirtualMachines** 角色中。
  * 所有主机的系统和事件日志收集在 **BareMetal** 角色中。
  * 故障转移群集和 Hyper-V 事件日志收集在“存储”  角色中。
  * ACS 日志收集在“存储”角色  和 **ACS** 角色中。

> [!NOTE]
> 会对收集的日志强制实施大小和保留时间限制，因为必须确保对存储空间进行有效的利用，从而确保该空间不会充斥着日志。 但是，在诊断问题时，有时可能需要某些日志，但这些日志可能因为这些限制而不再存在了。 因此，**强烈建议**每隔 8 到 12 小时就将日志卸载到外部存储空间（Azure 中的存储帐户、其他本地存储设备，等等）并在该处保留 1 - 3 月，具体取决于你的要求。 另外，请确保该存储位置已加密。

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

可以使用**Invoke AzureStackOnDemandLog** cmdlet 来生成按需日志对于某些角色 （请参阅本部分末尾的列表）。 此 cmdlet 生成的日志中不存在默认情况下接收在执行日志捆绑**Get-azurestacklog** cmdlet。 此外，建议收集这些日志，仅当请求由 Microsoft 支持团队。

目前，可以使用`-FilterByRole`参数按以下角色筛选日志收集：

* OEM
* NC
* SLB
* 网关

#### <a name="example-of-collecting-on-demand-logs"></a>收集按需日志的示例

```powershell
$ip = "<IP address of the PEP VM>" # You can also use the machine name instead of IP here.

$pwd= ConvertTo-SecureString "<cloud admin password>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<domain name>\CloudAdmin", $pwd)

$shareCred = Get-Credential

$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue

Invoke-Command -Session $s
{
   Invoke-AzureStackOnDemandLog -Generate -FilterByRole "<on-demand role name>" #Provide the supported on-demand role name : OEM, NC, SLB , Gateway
   Get-AzureStackLog -OutputSharePath "<external share address>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate

}

if($s)
{
   Remove-PSSession $s
}
```

## <a name="next-steps"></a>后续步骤

[Microsoft Azure Stack 故障排除](azure-stack-troubleshooting.md)
