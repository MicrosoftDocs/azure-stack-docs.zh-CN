---
title: Azure Stack 中心中的主动诊断日志收集
description: 了解如何在 Azure Stack 中心帮助和支持中配置主动诊断日志收集。
author: justinha
ms.topic: article
ms.date: 04/17/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 04/17/2020
ms.openlocfilehash: 067817e29858113fc22b596aa394d6e0942e921a
ms.sourcegitcommit: 5f4f0ee043ff994efaad44129ce49be43c64d5dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/16/2020
ms.locfileid: "84819430"
---
# <a name="proactive-diagnostic-log-collection-in-azure-stack-hub"></a>Azure Stack 中心中的主动诊断日志收集

::: moniker range=">= azs-2002"

通过在 Azure Stack 集线器上发出警报时，主动收集诊断日志来节省时间。

如果需要调查系统运行状况条件，则可以在使用 Microsoft 支持部门打开支持案例之前自动上载日志以进行分析。

## <a name="steps-to-configure-proactive-log-collection"></a>配置主动收集日志功能的步骤

按照以下步骤配置主动收集日志功能。 可随时禁用和重新启用主动日志收集。  

1. 登录到 Azure Stack Hub 管理员门户。
1. 打开 "**帮助 + 支持概述**"。
1. 如果出现标题，请选择 "**启用主动日志收集**"。 =

   ![屏幕截图显示在 "帮助 + 支持" 中启用日志收集的位置](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

   或者，你可以选择 "**设置**" 并将 "**主动日志收集**" 设置为 "**启用**"，然后选择 "**保存**"

   ![屏幕截图显示在 "帮助 + 支持" 中启用日志收集的位置](media/azure-stack-help-and-support/settings-enable-automatic-log-collection.png)

::: moniker-end
::: moniker range="<= azs-1910"

我们建议配置诊断日志自动收集功能，以获得顺畅的日志收集和客户支持体验。

如果需要调查系统运行状况条件，可以通过 Microsoft 支持部门自动上载日志以便进行分析。

## <a name="create-an-azure-blob-container-sas-url"></a>创建 Azure Blob 容器 SAS URL

你需要获取 blob 容器的共享访问签名（SAS），然后才能配置自动日志收集。 使用 SAS 可以授予对存储帐户中资源的访问权限，而无需共享帐户密钥。

可以将 Azure Stack 中心日志文件保存到 Azure 中的 blob 容器，然后提供可从中收集日志的 Microsoft 支持部门的 SAS URL。

### <a name="prerequisites"></a>先决条件

可以使用新的或 Azure 中的现有 Blob 容器。 若要在 Azure 中创建 Blob 容器，至少需要拥有[存储 Blob 参与者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)或[特定的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 全局管理员也具有必要的权限。

有关为自动日志收集存储帐户选择参数的最佳实践，请参阅[自动 Azure Stack 中心日志收集的最佳实践](azure-stack-best-practices-automatic-diagnostic-log-collection.md)。 有关存储帐户类型的详细信息，请参阅[Azure 存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)。

### <a name="create-a-blob-storage-account"></a>创建 Blob 存储帐户

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择 "**存储帐户**" > **Add**。
1. 使用以下设置创建 Blob 容器：

   - **订阅**：选择 Azure 订阅。
   - **资源组**：指定资源组。
   - **存储帐户名称**：指定唯一的存储帐户名称。
   - **位置**：根据公司政策选择数据中心。
   - **性能**：标准。
   - **帐户类型**StorageV2 （常规用途 v2）。
   - **复制**：本地冗余存储（LRS）。
   - **访问层**：酷。

   ![Blob 容器属性](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. 选择“查看 + 创建”，然后选择“创建” 。  

### <a name="create-a-blob-container"></a>创建 Blob 容器

1. 部署成功后，选择 "**前往资源**"。 你还可以将存储帐户固定到仪表板，以便轻松访问。
1. 选择**存储资源管理器（预览）**，右键单击 " **blob 容器**"，然后选择 "**创建 blob 容器**"。
1. 输入新容器的名称，然后选择 **"确定"**。

## <a name="create-a-sas-url"></a>创建 SAS URL

1. 右键单击容器，然后选择 "**获取共享访问签名**"。
   
   ![如何获取 blob 容器的共享访问签名](media/azure-stack-automatic-log-collection/get-sas.png)

1. 选择以下属性：

   - 开始时间：可视需要将开始时间回调
   - 过期时间：两年
   - 时区：UTC
   - 权限：“读取”、“写入”和“列出”

   ![共享访问签名属性](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. 选择“创建”。  

复制 URL，并在[配置自动日志收集](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)时输入该 URL。 有关 SAS URL 的详细信息，请参阅[使用共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。

## <a name="steps-to-configure-automatic-log-collection"></a>配置自动日志收集的步骤

按照以下步骤将 SAS URL 添加到日志收集 UI：

1. 登录到 Azure Stack Hub 管理员门户。
1. 打开 "**帮助 + 支持概述**"。
1. 单击“自动收集设置”。

   ![在帮助和支持中启用日志收集的位置](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. 将 "自动日志收集" 设置为 "**已启用**"。
1. 输入存储帐户 Blob 容器的共享访问签名 (SAS) URL。

   ![Blob SAS URL](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>随时可以禁用和重新启用自动日志收集。 SAS URL 配置不会更改。 如果重新启用自动日志收集，系统会对以前输入的 SAS URL 进行相同的验证检查，已过期的 SAS URL 将被拒绝。

::: moniker-end

## <a name="view-log-collection"></a>查看日志集合

从 Azure Stack 中心收集的日志历史记录显示在 "**帮助 + 支持**" 中的 "**日志收集**" 页上，并显示以下日期和时间：

- **收集的时间**：日志收集操作开始。
- **状态**： "正在进行" 或 "已完成"。
- **日志开始**：要收集的时间段的开始时间。
- **日志结束**时间：时间段结束。
- **键入**：如果是手动或主动日志收集。

![帮助和支持中的日志集合](media/azure-stack-help-and-support/azure-stack-log-collection.png)

## <a name="proactive-diagnostic-log-collection-alerts"></a>主动收集诊断日志时的警报

如果已启用，则仅当引发以下事件之一时，主动收集日志功能才会上传日志。

例如，“更新失败”是一个警报，会触发主动收集诊断日志的操作。 如果已启用，则会在更新失败时主动捕获诊断日志，以帮助 Microsoft 支持部门解决该问题。 仅在引发**更新失败**的警报时才收集诊断日志。

| 警报标题 | FaultIdType |
|---|---|
|无法连接到远程服务 | UsageBridge.NetworkError|
|更新失败 | Urp.UpdateFailure |
|存储资源提供程序基础结构/依赖项不可用 |    StorageResourceProviderDependencyUnavailable |
|节点未连接到控制器| ServerHostNotConnectedToController |  
|路由发布失败 | SlbMuxRoutePublicationFailure |
|存储资源提供程序内部数据存储不可用 |    StorageResourceProvider。 DataStoreConnectionFail |
|存储设备发生故障 | Microsoft.Health.FaultType.VirtualDisks.Detached |
|运行状况控制器无法访问存储帐户 | Microsoft.Health.FaultType.StorageError |
|与物理磁盘的连接已丢失 | Microsoft.Health.FaultType.PhysicalDisk.LostCommunication |
|Blob 服务未在节点上运行 | StorageService.The.blob.service.is.not.running.on.a.node-Critical |
|基础结构角色不正常 | Microsoft.Health.FaultType.GenericExceptionFault |
|表服务错误 | StorageService.Table.service.errors-Critical |
|文件共享已利用超过 80% | Microsoft.Health.FaultType.FileShare.Capacity.Warning.Infra |
|缩放单元节点已脱机 | FRP.Heartbeat.PhysicalNode |
|基础结构角色实例不可用 | FRP.Heartbeat.InfraVM |
|基础结构角色实例不可用  | FRP.Heartbeat.NonHaVm |
|基础结构角色“目录管理”报告了时间同步错误 | DirectoryServiceTimeSynchronizationError |
|挂起的外部证书过期 | CertificateExpiration.ExternalCert.Warning |
|挂起的外部证书过期 | CertificateExpiration.ExternalCert.Critical |
|由于内存容量不足，无法针对特定类别和大小预配虚拟机 | AzureStack.ComputeController.VmCreationFailure.LowMemory |
|无法访问节点以供虚拟机放置 | AzureStack.ComputeController.HostUnresponsive |
|备份失败  | AzureStack.BackupController.BackupFailedGeneralFault |
|由于与失败的操作发生冲突，已跳过计划的备份    | AzureStack.BackupController.BackupSkippedWithFailedOperationFault |

## <a name="see-also"></a>另请参阅

[Azure Stack Hub 日志和客户数据处理](azure-stack-data-collection.md)
