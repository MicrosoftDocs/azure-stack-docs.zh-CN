---
title: 配置 Azure Stack 日志自动收集 | Microsoft Docs
description: 如何在 Azure Stack 的“帮助 + 支持”中配置日志自动收集。
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
ms.date: 07/25/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 4d6bc431b292fc7a124aa2b8051d0a927d736eee
ms.sourcegitcommit: 4e48f1e5af74712a104eda97757dc5f50a591936
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71224950"
---
# <a name="configure-automatic-azure-stack-diagnostic-log-collection"></a>配置 Azure Stack 诊断日志自动收集

适用范围：*Azure Stack 集成系统*

我们建议配置诊断日志自动收集功能，以获得顺畅的日志收集和客户支持体验。 如果需要调查系统运行状况条件，则可以通过 Microsoft 客户支持服务（CSS）自动上载日志以进行分析。 

## <a name="create-an-azure-blob-container-sas-url"></a>创建 Azure Blob 容器 SAS URL 

在配置日志自动收集之前，需要先获取 Blob 容器的共享访问签名 (SAS)。 使用 SAS 可以授予对存储帐户中资源的访问权限，而无需共享帐户密钥。 可将 Azure Stack 日志存储到 Azure 中的某个 Blob 容器，然后提供 SAS URL 供 CSS 从中收集日志。 

### <a name="prerequisites"></a>先决条件

可以使用新的或 Azure 中的现有 Blob 容器。 若要在 Azure 中创建 Blob 容器，至少需要拥有[存储 Blob 参与者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)或[特定的权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 全局管理员也拥有所需的权限。 

有关如何为日志自动收集存储帐户选择参数的最佳做法，请参阅有关[自动收集 Azure Stack 日志的最佳做法](azure-stack-best-practices-automatic-diagnostic-log-collection.md)。 有关存储帐户类型的详细信息，请参阅 [Azure 存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)

### <a name="create-a-blob-storage-account"></a>创建 Blob 存储帐户
 
1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 单击“存储帐户” > “添加”。 
1. 使用以下设置创建 Blob 容器：
   - **订阅**：选择自己的 Azure 订阅
   - **资源组**：指定资源组
   - **存储帐户名称**：指定唯一的存储帐户名称
   - **位置**：根据公司政策选择数据中心
   - **性能**：选择标准
   - **帐户类型**选择 StorageV2 （常规用途 v2） 
   - **复制**：选择“本地冗余存储(LRS)”
   - **访问层**：选择“冷”

   ![显示 Blob 容器属性的屏幕截图](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. 依次单击“查看 + 创建”、“创建”。  

### <a name="create-a-blob-container"></a>创建 Blob 容器 

1. 部署成功后，单击“转到资源”。 还可以将存储帐户固定到仪表板以方便访问。 
1. 单击 "**存储资源管理器（预览）** "，右键单击 " **Blob 容器**"，然后单击 "**创建 blob 容器**"。 
1. 输入新容器的名称，然后单击“确定”。

## <a name="create-a-sas-url"></a>创建 SAS URL

1. 右键单击容器，然后单击 "**获取共享访问签名**"。 
   
   ![显示如何获取 Blob 容器共享访问签名的屏幕截图](media/azure-stack-automatic-log-collection/get-sas.png)

1. 选择以下属性：
   - 开始时间：可视需要将开始时间回调 
   - 过期时间：两年
   - 时区：UTC
   - 权限：“读取”、“写入”和“列出”

   ![显示共享访问签名属性的屏幕截图](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. 单击“创建”。  

复制 URL，并在[配置自动日志收集](azure-stack-configure-automatic-diagnostic-log-collection.md)时输入该 URL。 有关 SAS URL 的详细信息，请参阅[使用共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。 


## <a name="steps-to-configure-automatic-log-collection"></a>配置自动日志收集的步骤

按照以下步骤将 SAS URL 添加到日志收集 UI： 

1. 登录到 Azure Stack 管理员门户。
1. 打开“帮助和支持概述”。
1. 单击“自动收集设置”。

   ![显示“帮助和支持”中用于启用收集日志的位置的屏幕截图](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. 将“自动日志收集”设置为“已启用”。
1. 输入存储帐户 Blob 容器的共享访问签名 (SAS) URL。

   ![显示 Blob SAS URL 的屏幕截图](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>随时可以禁用和重新启用自动日志收集。 SAS URL 配置不会更改。 如果重新启用自动日志收集，系统会对以前输入的 SAS URL 进行相同的验证检查，已过期的 SAS URL 将被拒绝。 

## <a name="view-log-collection"></a>查看日志集合

以往从 Azure Stack 收集的日志显示在“帮助和支持”中的“日志收集”页上，其中提供了以下日期和时间：

- **收集时间**：日志收集操作的开始时间 
- **起始日期**：要收集日志的时段的开始时间
- **截止日期**：收集时段的结束时间

![显示日志集合的屏幕截图](media/azure-stack-automatic-log-collection/azure-stack-log-collection.png)

如果诊断日志收集失败，请检查 SAS URL 是否有效。 如果持续失败或多次出现失败，请联系 Microsoft CSS 寻求帮助。 

操作员还可以在存储帐户中检查自动收集的日志。 例如，以下屏幕截图显示从 Azure 门户使用存储资源管理器预览版收集的日志：

![显示日志集合的屏幕截图](media/azure-stack-automatic-log-collection/check-storage-account.png)

## <a name="automatic-diagnostic-log-collection-alerts"></a>自动诊断日志收集警报 

如果启用，则仅在需要时才会进行自动诊断日志收集。 仅下面的警报触发收集。 

|警报标题  | FaultIdType|    
|-------------|------------|
|无法连接到远程服务 |  UsageBridge. NetworkError|
|更新失败 |    Urp. UpdateFailure   |          
|存储资源提供程序基础结构/依赖关系不可用 |  StorageResourceProviderDependencyUnavailable     |     
|节点未连接到控制器|  ServerHostNotConnectedToController   |     
|路由发布失败 |    SlbMuxRoutePublicationFailure | 
|存储资源提供程序内部数据存储不可用 |    StorageResourceProvider. DataStoreConnectionFail     |       
|存储设备发生故障 | FaultType. VirtualDisks。   |      
|运行状况控制器无法访问存储帐户 | FaultType. StorageError |    
|与物理磁盘的连接已丢失 |    FaultType. PhysicalDisk. LostCommunication    |    
|Blob 服务未在节点上运行 | StorageService.... a. a. e--关键 | 
|基础结构角色不正常 |    FaultType. GenericExceptionFault |        
|表服务错误 | StorageService-严重错误 |              
|文件共享已使用超过 80% |    FaultType... #。 |       
|缩放单元节点处于脱机状态 | FRP.PhysicalNode |  
|基础结构角色实例不可用 | FRP.InfraVM   |    
|基础结构角色实例不可用  |    FRP.NonHaVm     |        
|基础结构角色 "目录管理" 报告时间同步错误 |  DirectoryServiceTimeSynchronizationError |     
|外部证书到期挂起 |  CertificateExpiration. ExternalCert. Warning |
|外部证书到期挂起 |  CertificateExpiration. ExternalCert |
|由于内存容量不足，无法针对特定类别和大小预配虚拟机 |  Test-azurestack. ComputeController. VmCreationFailure. LowMemory |
|节点不可访问，无法实现虚拟机放置 |  Test-azurestack. ComputeController. HostUnresponsive | 
|备份失败  | Test-azurestack. BackupController. BackupFailedGeneralFault |    
|由于与失败的操作冲突，已跳过计划的备份  | Test-azurestack. BackupController. BackupSkippedWithFailedOperationFault |   


## <a name="see-also"></a>请参阅

[Azure Stack 日志和客户数据处理](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[使用共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[有关 Azure Stack 日志自动收集的最佳做法](azure-stack-best-practices-automatic-diagnostic-log-collection.md)





