---
title: 在 Azure Stack Hub 中将 Azure PowerShell 脚本从 AzureRM 迁移到 Az
description: 了解在 Azure Stack Hub 中将脚本从 AzureRM 模块迁移到新的 Az 模块所需的步骤和工具。
author: mattbriggs
ms.author: mabrigg
ms.topic: conceptual
ms.date: 10/05/2020
ms.reviewer: sijuman
ms.lastreviewed: 04/14/2020
ms.openlocfilehash: bfb385e29a89ad8d47ace3b15776bd368e0cf521
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546165"
---
# <a name="migrate-from-azurerm-to-azure-powershell-az-in-azure-stack-hub"></a>在 Azure Stack Hub 中从 AzureRM 迁移到 Azure PowerShell Az

Az 模块与 AzureRM 具有功能奇偶一致性，但 Az 模块使用更短且更一致的 cmdlet 名称。
为 AzureRM cmdlet 编写的脚本不会自动与新模块配合工作。 为了简化转换，Az 提供了工具，以便你可以使用 AzureRM 运行现有的脚本。 不迁移到新的命令集十分省事，但本文将帮助你开始转换到新的模块。

若要查看 AzureRM 与 Az 之间的中断性变更的完整列表，请参阅 [Az 1.0.0 迁移指南](/powershell/azure/migrate-az-1.0.0)

## <a name="check-for-installed-versions-of-azurerm"></a>检查已安装的 AzureRM 版本

在执行任何迁移步骤之前，请检查系统上安装了 AzureRM 的哪些版本。 这样做可以确保脚本已在最新版本上运行，并获悉是否可以启用命令别名而不卸载 AzureRM。

若要检查已安装了 AzureRM 的哪个（些）版本，请运行以下命令：

```powershell-interactive
Get-InstalledModule -Name AzureRM -AllVersions
```

## <a name="check-current-scripts-work-with-azurerm"></a>检查当前脚本是否适用于 AzureRM

这是最重要的步骤！ 运行现有脚本，并确保这些脚本适用于最新版本的 AzureRM (2.5.0)。 如果脚本不起作用，请务必阅读 [AzureRM 迁移指南](/powershell/azure/azurerm/migration-guide.6.0.0)。

## <a name="install-the-azure-powershell-az-module"></a>安装 Azure Powershell Az 模块

第一步是在平台上安装 Az 模块。 安装 Az 时，建议卸载 AzureRM。 以下步骤将介绍如何继续运行现有脚本并为旧 cmdlet 名称启用兼容性。

若要安装 Azure PowerShell Az 模块，请执行以下步骤：

* __建议__ ： [卸载 AzureRM 模块](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)。
  请确保删除所有已安装的 AzureRM 版本，不只是最新版本。
* [安装 Az 模块](/powershell/azure/install-az-ps)

## <a name="enable-azurerm-compatibility-aliases"></a>启用 AzureRM 兼容性别名 

> [!IMPORTANT]
>
> 只有已卸载了 AzureRM 的所有版本时，才应启用兼容模式。 在 AzureRM cmdlet 仍然可用的情况下启用兼容模式可能会导致不可预知的行为。 如果决定保留已安装的 AzureRM，请跳过此步骤，但请注意，任何 AzureRM cmdlet 都将使用旧模块，不会调用任何 Az cmdlet。

卸载 AzureRM 并且脚本可以与最新的 AzureRM 版本配合工作后，下一步骤是为 Az 模块启用兼容模式。 使用以下命令启用兼容性：

```powershell-interactive
Enable-AzureRmAlias -Scope CurrentUser
```

在安装了 Az 模块的情况下，通过别名可以使用旧 cmdlet 名称。 这些别名会写入到所选范围的用户配置文件。 如果不存在用户配置文件，则会创建一个。

> [!WARNING]
>
> 可以为此命令使用其他 `-Scope`，但不建议这样做。 别名会写入到所选范围的用户配置文件，因此请保持将其启用到尽可能有限的范围。 在系统范围内启用别名也可能会导致已在其本地范围内安装 AzureRM 的其他用户出现问题。

启用别名模式后，请再次运行脚本以确认它们仍然正常运行。 

## <a name="change-module-and-cmdlet-names"></a>更改模块和 cmdlet 名称

一般情况下，模块名称已更改，以便 `AzureRM` 和 `Azure` 变为 `Az`，对于 cmdlet 也是如此。
例如，`AzureRM.Compute` 模块已重命名为 `Az.Compute`。 `New-AzureRMVM` 已变为 `New-AzVM`，并且 `Get-AzureStorageBlob` 现在为 `Get-AzStorageBlob`。

应当注意此命名更改有一些例外。 某些模块已重命名或合并到了现有模块中（在此命名更改未影响这些模块的 cmdlet 后缀的情况下），而不是将 `AzureRM` 或 `Azure` 更改为 `Az`。 其他情况下，完整的 cmdlet 后缀已更改，以反映新的模块名称。

| AzureRM 模块 | Az 模块 | Cmdlet 后缀已更改？ |
|----------------|-----------|------------------------|
| AzureRM.Profile | Az.Accounts | 是 |
| AzureRM.Insights | Az.Monitor | 是 |
| AzureRM.Tags | Az.Resources | 否 |
| AzureRM.UsageAggregates | Az.Billing | 否 |
| AzureRM.Consumption | Az.Billing | 否 |

## <a name="summary"></a>摘要

按照以上这些步骤操作，可以更新所有现有脚本以使用新模块。 如果对这些步骤有任何疑问或问题，使得迁移难于执行，请对本文发表评论，以便我们可以改进指导说明。

## <a name="breaking-changes-for-az-100"></a>Az 1.0.0 的中断性变更

本文档详述了从 AzureRM 6.x 到新 Az 模块（1.x 及更高版本）的变更。 可以通过目录了解完整的迁移路径，包括特定于模块的可能会影响脚本的变更。

## <a name="general-breaking-changes"></a>常规重大更改

此部分详述在重新设计 Az 模块过程中所做的常规中断性变更。

### <a name="cmdlet-noun-prefix-changes"></a>Cmdlet 名词前缀更改

在 AzureRM 模块中，cmdlet 使用了 `AzureRM` 或 `Azure` 作为名词前缀。  Az 简化并规范化了 cmdlet 名称，使所有 cmdlet 都使用“Az”作为其 cmdlet 名词前缀。 例如：

```powershell  
Get-AzureRMVM
Get-AzureKeyVaultSecret
```

已更改为：

```powershell  
Get-AzVM
Get-AzKeyVaultSecret
```

为了更简单地转换到这些新的 cmdlet 名称，Az 引入了两个新的 cmdlet：[Enable-AzureRmAlias](/powershell/module/az.accounts/enable-azurermalias) 和 [Disable-AzureRmAlias](/powershell/module/az.accounts/disable-azurermalias)。  `Enable-AzureRmAlias` 为 AzureRM 中较旧的 cmdlet 名称创建别名，这些旧名映射到较新的 Az cmdlet 名称。 将 `-Scope` 参数与 `Enable-AzureRmAlias` 配合使用即可选择在何处启用别名。

例如，AzureRM 中的以下脚本：

```powershell  
#Requires -Modules AzureRM.Storage
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

可以通过使用 `Enable-AzureRmAlias` 进行最少的更改来运行：

```powershell  
#Requires -Modules Az.Storage
Enable-AzureRmAlias -Scope Process
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

运行 `Enable-AzureRmAlias -Scope CurrentUser` 将为你打开的所有 PowerShell 会话启用别名，因此在执行此 cmdlet 后，像这样的脚本根本不需要进行更改：

```powershell  
Get-AzureRmStorageAccount | Get-AzureStorageContainer | Get-AzureStorageBlob
```

若要获得有关别名 cmdlet 用法的完整详细信息，请查看 [Enable-AzureRmAlias 参考](/powershell/module/az.accounts/enable-azurermalias)。

当你做好禁用别名的准备以后，`Disable-AzureRmAlias` 会删除创建的别名。 若要获得完整的详细信息，请查看 [Disable-AzureRmAlias 参考](/powershell/module/az.accounts/disable-azurermalias)。

> [!IMPORTANT]
> 禁用别名时，请确保在启用了别名的所有范围内禁用别名。

### <a name="module-name-changes"></a>模块名称更改

模块名称已从 `AzureRM.*` 更改为 `Az.*`，但以下模块除外：

| AzureRM 模块 | Az 模块 |
|----------------|-----------|
| Azure.存储 | Az.Storage |
| Azure.AnalysisServices | Az.AnalysisServices |
| AzureRM.Profile | Az.Accounts |
| AzureRM.Insights | Az.Monitor |
| AzureRM.RecoveryServices.Backup | Az.RecoveryServices |
| AzureRM.RecoveryServices.SiteRecovery | Az.RecoveryServices |
| AzureRM.Tags | Az.Resources |
| AzureRM.MachineLearningCompute | Az.MachineLearning |
| AzureRM.UsageAggregates | Az.Billing |
| AzureRM.Consumption | Az.Billing |

模块名称更改意味着使用 `#Requires` 或 `Import-Module` 来加载特定模块的任何脚本都需要更改为使用新模块。 对于其中的 cmdlet 后缀尚未更改的模块，这意味着，虽然模块名称已更改，但指示操作空间的后缀尚未更改。

#### <a name="migrating-requires-and-import-module-statements"></a>迁移 requires 和 import module 语句

使用 `#Requires` 或 `Import-Module` 来声明对 AzureRM 模块的依赖关系的脚本必须更新为使用新的模块名称。 例如：

```powershell  
#Requires -Module AzureRM.Compute
```

应当更改为：

```powershell  
#Requires -Module Az.Compute
```

对于 `Import-Module`：

```powershell  
Import-Module -Name AzureRM.Compute
```

应当更改为：

```powershell  
Import-Module -Name Az.Compute
```

### <a name="migrating-fully-qualified-cmdlet-invocations"></a>迁移完全限定的 cmdlet 调用

使用模块限定的 cmdlet 调用的脚本，例如：

```powershell  
AzureRM.Compute\Get-AzureRmVM
```

必须更改为使用新的模块和 cmdlet 名称：

```powershell  
Az.Compute\Get-AzVM
```

### <a name="migrating-module-manifest-dependencies"></a>迁移模块清单依赖关系

通过模块清单 (.psd1) 文件表示对 AzureRM 模块的依赖关系的模块需要更新其 `RequiredModules` 部分中的模块名称：

```powershell
RequiredModules = @(@{ModuleName="AzureRM.Profile"; ModuleVersion="5.8.2"})
```

必须更改为：

```powershell
RequiredModules = @(@{ModuleName="Az.Accounts"; ModuleVersion="1.0.0"})
```

### <a name="removed-modules"></a>删除的模块

以下模块已删除：

- `AzureRM.Backup`
- `AzureRM.Compute.ManagedService`
- `AzureRM.Scheduler`

不再主动支持这些服务的工具。  建议客户尽快在方便的时候迁移到替代服务。

### <a name="windows-powershell-51-and-net-472"></a>Windows PowerShell 5.1 和 .NET 4.7.2

将 Az 与 Windows 版 PowerShell 5.1 配合使用需要安装 .NET Framework 4.7.2。 使用 PowerShell Core 6.x 或更高版本不需要 .NET Framework。

### <a name="temporary-removal-of-user-login-using-pscredential"></a>暂时删除了使用 PSCredential 的用户登录

由于 .NET Standard 的身份验证流发生更改，我们暂时删除了通过 PSCredential 进行的用户登录。 将在 2019 年 1 月 15 日的 Windows 版 PowerShell 5.1 中重新引入此功能。 [此 GitHub 问题](https://github.com/Azure/azure-powershell/issues/7430)中详细讨论了这方面的内容。

### <a name="default-device-code-login-instead-of-web-browser-prompt"></a>默认设备代码登录替换了 Web 浏览器提示

由于 .NET Standard 的身份验证流发生更改，我们在交互式登录期间使用设备登录作为默认登录流。 将在 2019 年 1 月 15 日的 Windows 版 PowerShell 5.1 中重新引入基于 Web 浏览器的登录作为默认登录流。 到那时，用户将能够使用一个 Switch 参数来选择设备登录。

## <a name="module-breaking-changes"></a>模块中断性变更

此部分详述单个模块和 cmdlet 的具体中断性变更。

### <a name="azapimanagement-previously-azurermapimanagement"></a>Az.ApiManagement（以前的 AzureRM.ApiManagement）

- 删除了以下 cmdlet：
  - New-AzureRmApiManagementHostnameConfiguration
  - Set-AzureRmApiManagementHostnames
  - Update-AzureRmApiManagementDeployment
  - Import-AzureRmApiManagementHostnameCertificate
  - 请改用 **Set-AzApiManagement** cmdlet 来设置这些属性
- 删除了以下属性：
  - 从 `PsApiManagementContext` 中删除了 `PsApiManagementHostnameConfiguration` 类型的属性 `PortalHostnameConfiguration`、`ProxyHostnameConfiguration`、`ManagementHostnameConfiguration` 和 `ScmHostnameConfiguration`。 请改用 `PsApiManagementCustomHostNameConfiguration` 类型的 `PortalCustomHostnameConfiguration`、`ProxyCustomHostnameConfiguration`、`ManagementCustomHostnameConfiguration` 和 `ScmCustomHostnameConfiguration`。
  - 从 PsApiManagementContext 中删除了属性 `StaticIPs`。 此属性已拆分为 `PublicIPAddresses` 和 `PrivateIPAddresses`。
  - 从 New-AzureApiManagementVirtualNetwork cmdlet 中删除了必需属性 `Location`。

### <a name="azbilling-previously-azurermbilling-azurermconsumption-and-azurermusageaggregates"></a>Az.Billing（以前的 AzureRM.Billing、AzureRM.Consumption 和 AzureRM.UsageAggregates）

- 从 `Get-AzConsumptionUsageDetail` cmdlet 中删除了 `InvoiceName` 参数。  脚本将需要为发票使用其他标识参数。

### <a name="azcompute-previously-azurermcompute"></a>Az.Compute（以前的 AzureRM.Compute）

- 从 `PSVirtualMachine` 和 `PSVirtualMachineScaleSet` 对象中的 `Identity` 属性中删除了 `IdentityIds`。脚本不应当再使用此字段的值来做出处理决策。
- `PSVirtualMachineScaleSetVM` 对象的 `InstanceView` 属性的类型已从 `VirtualMachineInstanceView` 更改为 `VirtualMachineScaleSetVMInstanceView`
- 从 `UpgradePolicy` 属性中删除了 `AutoOSUpgradePolicy` 和 `AutomaticOSUpgrade` 属性
- `PSSnapshotUpdate` 对象中的 `Sku` 属性的类型已从 `DiskSku` 更改为 `SnapshotSku`
- `VmScaleSetVMParameterSet` 已从 `Add-AzVMDataDisk` cmdlet 中删除，你不再能够将数据磁盘单独添加到规模集 VM。

### <a name="azkeyvault-previously-azurermkeyvault"></a>Az.KeyVault（以前的 AzureRM.KeyVault）

- 从 `PSKeyVaultKeyAttributes`、`PSKeyVaultKeyIdentityItem` 和 `PSKeyVaultSecretAttributes` 对象中删除了 `PurgeDisabled` 属性。脚本不再应该引用 ```PurgeDisabled``` 属性来做出处理决策。

### <a name="azmonitor-previously-azurerminsights"></a>Az.Monitor（以前的 AzureRM.Insights）

- 从 `Set-AzDiagnosticSetting` cmdlet 中删除了复数名称 `Categories` 和 `Timegrains` 参数并将其替换为单数参数名称。使用以下内容的脚本
  ```powershell  
  Set-AzureRmDiagnosticSetting -Timegrains PT1M -Categories Category1, Category2
  ```

  应当更改为
  ```powershell  
  Set-AzDiagnosticSetting -Timegrain PT1M -Category Category1, Category2
  ```

### <a name="aznetwork-previously-azurermnetwork"></a>Az.Network（以前的 AzureRM.Network）

- 从 `Get-AzServiceEndpointPolicyDefinition` cmdlet 中删除了弃用的 `ResourceId` 参数
- 从 `PSVirtualNetwork` 对象中删除了弃用的 `EnableVmProtection` 属性
- 删除了弃用的 `Set-AzVirtualNetworkGatewayVpnClientConfig` cmdlet

脚本不应当再根据这些字段的值做出处理决策。

### <a name="azresources-previously-azurermresources"></a>Az.Resources（以前的 AzureRM.Resources）

- 从 `New/Set-AzPolicyAssignment` cmdlet 中删除了 `Sku` 参数
- 从 `New-AzADServicePrincipal` 和 `New-AzADSpCredential` cmdlet 中删除了 `Password` 参数。密码将自动生成，提供了密码的脚本：

  ```powershell  
  New-AzAdSpCredential -ObjectId 1f99cf81-0146-4f4e-beae-2007d0668476 -Password $secPassword
  ```

  应当更改为从输出中检索密码：

  ```powershell  
  $credential = New-AzAdSpCredential -ObjectId 1f99cf81-0146-4f4e-beae-2007d0668476
  $secPassword = $credential.Secret
  ```


### <a name="azstorage-previously-azurestorage-and-azurermstorage"></a>Az.Storage（以前的 Azure.Storage 和 AzureRM.Storage）

- 为了支持仅使用存储帐户名称创建 Oauth 存储上下文，默认参数集已更改为 `OAuthParameterSet`
  - 示例： `$ctx = New-AzureStorageContext -StorageAccountName $accountName`
- `Location` 参数在 `Get-AzStorageUsage` cmdlet 中已成为必需参数
- 存储 API 方法现在使用基于任务的异步模式 (TAP)，而非使用同步 API 调用。 以下示例演示新的异步命令：

#### <a name="blob-snapshot"></a>Blob 快照

AzureRM：

```powershell  
$b = Get-AzureStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$b.ICloudBlob.Snapshot()
```

Az：

```powershell  
$b = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$task = $b.ICloudBlob.SnapshotAsync()
$task.Wait()
$snapshot = $task.Result
```

#### <a name="share-snapshot"></a>共享快照

AzureRM：

```powershell  
$Share = Get-AzureStorageShare -Name $containerName -Context $ctx
$snapshot = $Share.Snapshot()
```

Az：

```powershell  
$Share = Get-AzStorageShare -Name $containerName -Context $ctx
$task = $Share.SnapshotAsync()
$task.Wait()
$snapshot = $task.Result
```

#### <a name="undelete-soft-deleted-blob"></a>撤消删除软删除的 Blob

AzureRM：

```powershell  
$b = Get-AzureStorageBlob -Container $containerName -Blob $blobName -IncludeDeleted -Context $ctx
$b.ICloudBlob.Undelete()
```

Az：

```powershell  
$b = Get-AzStorageBlob -Container $containerName -Blob $blobName -IncludeDeleted -Context $ctx
$task = $b.ICloudBlob.UndeleteAsync()
$task.Wait()
```

#### <a name="set-blob-tier"></a>设置 blob 层

AzureRM：

```powershell  
$blockBlob = Get-AzureStorageBlob -Container $containerName -Blob $blockBlobName -Context $ctx
$blockBlob.ICloudBlob.SetStandardBlobTier("hot")

$pageBlob = Get-AzureStorageBlob -Container $containerName -Blob $pageBlobName -Context $ctx
$pageBlob.ICloudBlob.SetPremiumBlobTier("P4")
```

Az：

```powershell  
$blockBlob = Get-AzStorageBlob -Container $containerName -Blob $blockBlobName -Context $ctx
$task = $blockBlob.ICloudBlob.SetStandardBlobTierAsync("hot")
$task.Wait()

$pageBlob = Get-AzStorageBlob -Container $containerName -Blob $pageBlobName -Context $ctx
$task = $pageBlob.ICloudBlob.SetPremiumBlobTierAsync("P4")
$task.Wait()
```

### <a name="azwebsites-previously-azurermwebsites"></a>Az.Websites（以前的 AzureRM.Websites）

- 从 `PSAppServicePlan`、`PSCertificate`、`PSCloningInfo` 和 `PSSite` 对象中删除了弃用的属性

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure Stack Hub 上的 PowerShell，请参阅 [Azure Stack Hub 中的 PowerShell 入门](../user/azure-stack-powershell-overview.md)
- 若要安装 PowerShell Az 模块，请参阅[安装适用于 Azure Stack Hub 的 PowerShell Az 模块](powershell-install-az-module.md)
