---
title: 在 Azure Stack 集线器上发出警报时，主动收集诊断日志
description: 如何在 Azure Stack 中心帮助和支持中配置主动日志收集。
author: justinha
ms.topic: article
ms.date: 04/17/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 04/17/2020
ms.openlocfilehash: 636a3a62b60092e4869197432884533fac57c788
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81625003"
---
# <a name="send-azure-stack-hub-diagnostic-logs-proactively"></a>主动发送 Azure Stack 集线器诊断日志

::: moniker range=">= azs-2002"

可以通过在 Azure Stack 集线器上发出警报时，主动收集诊断日志来节省时间。
如果需要调查系统运行状况条件，则可以在使用 Microsoft 客户支持服务（CSS）打开支持案例之前，自动上载日志以进行分析。 

## <a name="steps-to-configure-proactive-log-collection"></a>配置主动日志收集的步骤

按照以下步骤配置主动日志收集。 随时可以禁用和重新启用自动日志收集。  

1. 登录到 Azure Stack Hub 管理员门户。
1. 打开“帮助和支持概述”。****
1. 如果出现横幅，请单击 "**启用主动日志收集**"。 

   ![显示“帮助和支持”中用于启用收集日志的位置的屏幕截图](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

   或者，您可以单击 "**设置**" 并将 "**主动日志收集**" 设置为**启用**，然后单击 "**保存**"。

   ![显示“帮助和支持”中用于启用收集日志的位置的屏幕截图](media/azure-stack-help-and-support/settings-enable-automatic-log-collection.png)

::: moniker-end
::: moniker range="<= azs-1910"

我们建议配置诊断日志自动收集功能，以获得顺畅的日志收集和客户支持体验。 
如果需要调查系统运行状况条件，则可以通过 Microsoft 客户支持服务（CSS）自动上载日志以进行分析。 

## <a name="create-an-azure-blob-container-sas-url"></a>创建 Azure Blob 容器 SAS URL 

在配置日志自动收集之前，需要先获取 Blob 容器的共享访问签名 (SAS)。 使用 SAS 可以授予对存储帐户中资源的访问权限，而无需共享帐户密钥。 
可以将 Azure Stack 中心日志文件保存到 Azure 中的 blob 容器，并提供可在其中使用 CSS 收集日志的 SAS URL。 

### <a name="prerequisites"></a>必备条件

可以使用新的或 Azure 中的现有 Blob 容器。 
若要在 Azure 中创建 Blob 容器，至少需要拥有[存储 Blob 参与者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)或[特定的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 
全局管理员也拥有所需的权限。 

有关为自动日志收集存储帐户选择参数的最佳实践，请参阅[自动 Azure Stack 中心日志收集的最佳实践](azure-stack-best-practices-automatic-diagnostic-log-collection.md)。 有关存储帐户类型的详细信息，请参阅 [Azure 存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)

### <a name="create-a-blob-storage-account"></a>创建 Blob 存储帐户
 
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 单击“存储帐户”“添加”。**** > **** 
1. 使用以下设置创建 Blob 容器：
   - **订阅**：选择 Azure 订阅
   - **资源组**：指定资源组
   - **存储帐户名称**：指定唯一的存储帐户名称
   - **位置**：根据公司政策选择数据中心
   - **性能**：标准
   - **帐户类型**StorageV2 （常规用途 v2） 
   - **复制**：本地冗余存储（LRS）
   - **访问层**：酷

   ![显示 Blob 容器属性的屏幕截图](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. 依次单击“查看 + 创建”、“创建”。********  

### <a name="create-a-blob-container"></a>创建 Blob 容器 

1. 部署成功后，单击“转到资源”。**** 还可以将存储帐户固定到仪表板以方便访问。 
1. 单击 "**存储资源管理器（预览）**"，右键单击 " **Blob 容器**"，然后单击 "**创建 blob 容器**"。 
1. 输入新容器的名称，然后单击“确定”。****

## <a name="create-a-sas-url"></a>创建 SAS URL

1. 右键单击容器，然后单击 "**获取共享访问签名**"。 
   
   ![显示如何获取 Blob 容器共享访问签名的屏幕截图](media/azure-stack-automatic-log-collection/get-sas.png)

1. 选择以下属性：
   - 开始时间：可以选择将开始时间移回 
   - 过期时间：两年
   - 时区： UTC
   - 权限：读取、写入和列出

   ![显示共享访问签名属性的屏幕截图](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. 单击“**创建**”。  

复制 URL，并在[配置自动日志收集](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md)时输入该 URL。 
有关 SAS URL 的详细信息，请参阅[使用共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。 


## <a name="steps-to-configure-automatic-log-collection"></a>配置自动日志收集的步骤

按照以下步骤将 SAS URL 添加到日志收集 UI： 

1. 登录到 Azure Stack Hub 管理员门户。
1. 打开“帮助和支持概述”。****
1. 单击“自动收集设置”。****

   ![显示“帮助和支持”中用于启用收集日志的位置的屏幕截图](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. 将“自动日志收集”设置为“已启用”。****
1. 输入存储帐户 Blob 容器的共享访问签名 (SAS) URL。

   ![显示 Blob SAS URL 的屏幕截图](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>随时可以禁用和重新启用自动日志收集。 SAS URL 配置不会更改。 如果重新启用自动日志收集，系统会对以前输入的 SAS URL 进行相同的验证检查，已过期的 SAS URL 将被拒绝。 


::: moniker-end

## <a name="view-log-collection"></a>查看日志集合

"帮助和支持" 中的 "**日志收集**" 页面上会显示从 Azure Stack 中心收集的日志历史记录，其日期和时间如下：

- **收集的时间**：日志收集操作开始时间
- **状态**： "正在进行" 或 "已完成"
- **日志开始**：要收集的时间段的开始时间
- **日志结束**时间：时间段结束
- **类型**：如果是手动或主动日志收集 


![显示日志集合的屏幕截图](media/azure-stack-help-and-support/azure-stack-log-collection.png)


## <a name="proactive-diagnostic-log-collection-alerts"></a>主动诊断日志收集警报 

如果已启用，则只有当引发以下事件之一时，主动日志收集才会上载日志。 

例如， **Update failed**是触发前瞻性诊断日志收集的警报。 如果启用了此功能，则会在更新失败时主动捕获诊断日志，以帮助 CSS 对问题进行故障排除。 仅在引发**更新失败**的警报时才收集诊断日志。 

|警报标题  |    FaultIdType|    
|-------------|------------|
|无法连接到远程服务 |    UsageBridge.NetworkError|
|更新失败 |    Urp.UpdateFailure    |          
|存储资源提供程序基础结构/依赖项不可用 |    StorageResourceProviderDependencyUnavailable     |     
|节点未连接到控制器|    ServerHostNotConnectedToController     |     
|路由发布失败 |    SlbMuxRoutePublicationFailure |    
|存储资源提供程序内部数据存储不可用 |    StorageResourceProvider。 DataStoreConnectionFail     |       
|存储设备发生故障    | Microsoft.Health.FaultType.VirtualDisks.Detached     |      
|运行状况控制器无法访问存储帐户 | Microsoft.Health.FaultType.StorageError |    
|与物理磁盘的连接已丢失 |    Microsoft.Health.FaultType.PhysicalDisk.LostCommunication     |    
|Blob 服务未在节点上运行 |    StorageService.The.blob.service.is.not.running.on.a.node-Critical |    
|基础结构角色不正常 |    Microsoft.Health.FaultType.GenericExceptionFault |        
|表服务错误 |    StorageService.Table.service.errors-Critical |              
|文件共享已利用超过 80% |    Microsoft.Health.FaultType.FileShare.Capacity.Warning.Infra |       
|缩放单元节点处于脱机状态    | FRP.Heartbeat.PhysicalNode |    
|基础结构角色实例不可用 |    FRP.Heartbeat.InfraVM    |    
|基础结构角色实例不可用  |    FRP.Heartbeat.NonHaVm     |        
|基础结构角色“目录管理”报告了时间同步错误 |    DirectoryServiceTimeSynchronizationError |       
|外部证书到期挂起 |    CertificateExpiration.ExternalCert.Warning |
|外部证书到期挂起 |    CertificateExpiration.ExternalCert.Critical |
|由于内存容量不足，无法针对特定类别和大小预配虚拟机 |    AzureStack.ComputeController.VmCreationFailure.LowMemory |
|节点不可访问，无法实现虚拟机放置 |    AzureStack.ComputeController.HostUnresponsive |    
|备份失败  | AzureStack.BackupController.BackupFailedGeneralFault |    
|由于与失败的操作发生冲突，已跳过计划的备份    | AzureStack.BackupController.BackupSkippedWithFailedOperationFault |    


## <a name="see-also"></a>另请参阅

[Azure Stack 中心日志和客户数据处理](azure-stack-data-collection.md)





