---
title: 使用特权终结点（PEP）收集诊断日志
description: 了解如何使用管理员门户或 PowerShell 脚本在 Azure Stack 中心内收集诊断日志。
author: justinha
ms.topic: article
ms.date: 03/05/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/05/2020
ms.openlocfilehash: df5a98e8526181a84d8b214fbdf82eb1dba00088
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520459"
---
# <a name="send-azure-stack-hub-diagnostic-logs-by-using-the-privileged-endpoint-pep"></a>使用特权终结点（PEP）发送 Azure Stack 集线器诊断日志

<!--how do you look up the PEP IP address. You look up the azurestackstampinfo.json--->


若要在集成系统上运行 Get-azurestacklog，需要有权访问特权终结点（PEP）。 下面是一个示例脚本，可使用 PEP 运行来收集日志。 如果要取消正在运行的日志集合，请在启动新的日志收集之前等待5分钟，然后输入 `Remove-PSSession -Session $session`。


```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session { Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if ($session) {
    Remove-PSSession -Session $session
}
```

### <a name="examples"></a>示例

* 收集所有角色的所有日志：

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred
  ```

* 从 VirtualMachines 和 BareMetal 角色收集日志：

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

* 从 VirtualMachines 和 BareMetal 角色收集日志，并在过去8小时内记录日志文件的日期筛选：

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* 从 VirtualMachines 和 BareMetal 角色收集日志，并在8小时前和2小时前的时间段内对日志文件进行日期筛选：

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

* 在 Azure Stack 上，从运行自行管理的 Kubernetes 群集（AKS 引擎）的租户部署收集日志。 Kubernetes 日志应采用一种格式存储在租户存储帐户中，该格式将允许将收集时间范围应用于这些日志。 

  ```powershell
  Get-AzureStackLog -OutputPath <Path> -InputSasUri "<Blob Service Sas URI>" -FromDate "<Beginning of the time range>" -ToDate "<End of the time range>"
  ```

  例如：

  ```powershell
  Get-AzureStackLog -OutputPath C:\KubernetesLogs -InputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>" -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2) 
  ```

* 收集值-添加 RPs 的日志。 一般语法为：
 
  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider <<value-add RP name>>
  ```
 
  收集 IoT 中心日志： 

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider IotHub
  ```
 
  收集事件中心日志：

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider eventhub
  ```
 
  收集 Azure Stack Edge 的日志：

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvide databoxedge
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
  > 此过程适用于上载日志。 即使你没有可访问的 SMB 共享或访问 internet，你也可以在 Azure Stack 集线器上创建 blob 存储帐户，以传输日志，然后使用客户端检索这些日志。  

  若要为存储帐户生成 SAS 令牌，需要以下权限：

  * 对 Blob 存储服务的访问权限。
  * 对容器资源类型的访问。

  若要生成用于 `-OutputSasUri` 参数的 SAS Uri 值，请执行以下步骤：

  1. 按照[本文中](/azure/storage/common/storage-quickstart-create-account)的步骤创建存储帐户。
  2. 打开 Azure 存储资源管理器的实例。
  3. 连接到步骤1中创建的存储帐户。
  4. 在**存储服务**中导航到**Blob 容器**。
  5. 选择 "**创建新容器**"。
  6. 右键单击新容器，然后单击 "**获取共享访问签名**"。
  7. 根据你的需求，选择有效的**开始时间**和**结束时间**。
  8. 对于所需的权限，请选择 "**读取**"、"**写入**" 和 "**列表**"。
  9. 选择“创建”。
  10. 你将获得一个共享访问签名。 复制 URL 部分，并将其提供给 `-OutputSasUri` 参数。

### <a name="parameter-considerations"></a>参数注意事项 

* 参数**OutputSharePath**和**OutputShareCredential**用于将日志存储在用户指定的位置。

* **FromDate**和**ToDate**参数可用于收集特定时间段的日志。 如果未指定这些参数，则默认情况下，将收集过去四小时的日志。

* 使用**FilterByNode**参数按计算机名称筛选日志。 例如：

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```

* 使用**FilterByLogType**参数按类型筛选日志。 可以选择按文件、共享或 WindowsEvent 进行筛选。 例如：

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByLogType File
    ```

* 可以使用**TimeOutInMinutes**参数设置日志收集的超时。 默认情况下，它设置为150（2.5 小时）。
* 默认情况下，已禁用转储文件日志收集。 若要启用它，请使用**IncludeDumpFile**开关参数。
* 目前，可以使用**FilterByRole**参数按以下角色筛选日志收集：

  |   |   |   |    |     |
  | - | - | - | -  |  -  |
  |ACS                   |CA                             |HRP                            |OboService                |VirtualMachines|
  |ACSBlob               |Cacheservice.provider                   |IBC                            |OEM                       |WAS            |
  |ACSDownloadService    |计算                        |InfraServiceController         |OnboardRP                 |WASPUBLIC|
  |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE                       |         |
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator   |         | 
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker        |         |
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing                  |         |
  |ACSMonitoringService  |域                         |KeyVaultInternalDataPlane      |SeedRingServices          |         |
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

### <a name="additional-considerations-on-diagnostic-logs"></a>诊断日志的其他注意事项

* 此命令需要一些时间，具体取决于日志正在收集的角色。 贡献因素还包括为日志收集指定的持续时间，以及 Azure Stack 中心环境中的节点数。
* 在运行日志收集时，检查在命令中指定的**OutputSharePath**参数中创建的新文件夹。
* 每个角色都将其日志包含在单个 zip 文件中。 根据所收集日志的大小，角色可能会将其日志拆分为多个 zip 文件。 对于这种角色，如果要将所有日志文件解压缩到一个文件夹中，请使用可以批量解压缩的工具。 选择该角色的所有压缩文件，并选择 "**在此处提取**"。 该角色的所有日志文件都将解压缩到单个合并文件夹中。
* 还会在包含压缩日志文件的文件夹中创建一个名为 "AzureStackLog_Output" 的文件 **。** 此文件是命令输出的日志，可用于对日志收集过程中的问题进行故障排除。 日志文件有时包括可以安全忽略 `PS>TerminatingError` 条目，除非日志收集运行后所需的日志文件丢失。
* 若要调查特定的故障，可能需要从多个组件进行日志记录。

  * 所有基础结构 Vm 的系统和事件日志收集在**VirtualMachines**角色中。
  * 所有主机的系统和事件日志收集在**BareMetal**角色中。
  * 故障转移群集和 Hyper-v 事件日志在**存储**角色中收集。
  * ACS 日志在**存储**和**ACS**角色中收集。

> [!NOTE]
> 对收集的日志强制使用大小和期限限制，因为必须确保有效地利用存储空间并避免日志溢出。 但是，在诊断问题时，有时需要的日志会因为这些限制而不再存在。 因此，**强烈建议**你将日志卸载到外部存储空间（Azure 中的存储帐户、附加的本地存储设备等），每8到12小时，根据你的要求，将其保存在 1-3 个月内。 还应确保此存储位置已加密。

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

可以使用**AzureStackOnDemandLog** cmdlet 来生成特定角色的按需日志（请参阅本节末尾的列表）。 默认情况下，在执行**get-azurestacklog** cmdlet 时收到的日志绑定中不存在此 cmdlet 生成的日志。 此外，建议仅在 Microsoft 支持团队请求时收集这些日志。

目前，可以使用 `-FilterByRole` 参数按以下角色筛选日志收集：

* OEM
* NC
* SLB
* 网关

#### <a name="example-of-collecting-on-demand-diagnostic-logs"></a>收集按需诊断日志的示例

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

### <a name="how-diagnostic-log-collection-using-the-pep-works"></a>如何使用 PEP 收集诊断日志

Azure Stack 集线器诊断工具有助于更轻松、更高效地收集日志。 下图显示了诊断工具的工作方式：

![Azure Stack 集线器诊断工具工作流关系图](media/azure-stack-diagnostics/get-azslogs.png)


#### <a name="trace-collector"></a>跟踪收集器

默认情况下，跟踪收集器处于启用状态，并在后台持续运行，以便从 Azure Stack 中心组件服务收集所有 Windows 事件跟踪（ETW）日志。 ETW 日志存储在具有5天期限限制的公共本地共享中。 达到此限制后，会在创建新文件时删除最旧的文件。 每个文件允许的默认最大大小为 200 MB。 大小检查每2分钟发生一次，如果当前文件 > = 200 MB，则会保存该文件，并生成新文件。 对于每个事件会话生成的总文件大小，还会有 8 GB 的限制。

#### <a name="get-azurestacklog"></a>Get-AzureStackLog

PowerShell cmdlet Get-azurestacklog 可用于从 Azure Stack 中心环境中的所有组件收集日志。 它将它们保存在用户定义位置的 zip 文件中。 如果 Azure Stack 中心技术支持团队需要你的日志以帮助解决问题，则他们可能会要求你运行 Get-azurestacklog。

> [!CAUTION]
> 这些日志文件可能包含个人身份信息（PII）。 在公开发布任何日志文件之前，请考虑到这一点。

下面是一些收集的示例日志类型：

* **Azure Stack 中心部署日志**
* **Windows 事件日志**
* **Panther 日志**
* **群集日志**
* **存储诊断日志**
* **ETW 日志**

这些文件通过跟踪收集器收集并保存在共享中。 然后，可以根据需要使用 Get-azurestacklog 来收集它们。

