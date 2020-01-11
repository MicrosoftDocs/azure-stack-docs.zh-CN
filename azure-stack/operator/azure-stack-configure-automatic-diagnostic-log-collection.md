---
title: 配置自动 Azure Stack 中心日志收集 |Microsoft Docs
description: 如何在 Azure Stack 中心帮助 + 支持中配置自动日志收集。
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
ms.date: 10/08/2019
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 10/08/2019
ms.openlocfilehash: 63ee429c37d5ec7bf9258e3e940d9dbfa3786907
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882583"
---
# <a name="configure-automatic-azure-stack-hub-diagnostic-log-collection"></a>配置自动 Azure Stack 集线器诊断日志收集

建议配置自动诊断日志收集功能，以简化日志收集和客户支持体验。 如果需要调查系统运行状况条件，则可以通过 Microsoft 客户支持服务（CSS）自动上载日志以进行分析。 

## <a name="create-an-azure-blob-container-sas-url"></a>创建 Azure blob 容器 SAS URL 

你需要获取 blob 容器的共享访问签名（SAS），然后才能配置自动日志收集。 使用 SAS 可以授予对存储帐户中资源的访问权限，而无需共享帐户密钥。 可以将 Azure Stack 中心日志文件保存到 Azure 中的 blob 容器，并提供可在其中使用 CSS 收集日志的 SAS URL。 

### <a name="prerequisites"></a>必备组件

可以在 Azure 中使用新的或现有的 blob 容器。 若要在 Azure 中创建 blob 容器，你至少需要[存储 blob 参与者角色](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)或[特定权限](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations)。 全局管理员也具有必要的权限。 

有关为自动日志收集存储帐户选择参数的最佳实践，请参阅[自动 Azure Stack 中心日志收集的最佳实践](azure-stack-best-practices-automatic-diagnostic-log-collection.md)。 有关存储帐户类型的详细信息，请参阅[Azure 存储帐户概述](https://docs.microsoft.com/azure/storage/common/storage-account-overview)

### <a name="create-a-blob-storage-account"></a>创建 Blob 存储帐户
 
1. 登录 [Azure 门户](https://portal.azure.com)。
1. 单击 "**存储帐户**" > "**添加**"。 
1. 使用以下设置创建 blob 容器：
   - **订阅**：选择 Azure 订阅
   - **资源组**：指定资源组
   - **存储帐户名称**：指定唯一的存储帐户名称
   - **位置**：根据公司政策选择数据中心
   - **性能**：选择标准
   - **帐户类型**选择 StorageV2 （常规用途 v2） 
   - **复制**：选择本地冗余存储（LRS）
   - **访问层**：选择 "冷"

   ![显示 blob 容器属性的屏幕截图](media/azure-stack-automatic-log-collection/azure-stack-log-collection-create-storage-account.png)

1. 单击“查看 + 创建”，然后单击“创建”。  

### <a name="create-a-blob-container"></a>创建 Blob 容器 

1. 部署成功后，单击 "**前往资源**"。 你还可以将存储帐户固定到仪表板，以便轻松访问。 
1. 单击 "**存储资源管理器（预览）** "，右键单击 " **Blob 容器**"，然后单击 "**创建 blob 容器**"。 
1. 输入新容器的名称，然后单击 **"确定"** 。

## <a name="create-a-sas-url"></a>创建 SAS URL

1. 右键单击容器，然后单击 "**获取共享访问签名**"。 
   
   ![显示如何获取 blob 容器的共享访问签名的屏幕截图](media/azure-stack-automatic-log-collection/get-sas.png)

1. 选择以下属性：
   - 开始时间：可以选择将开始时间移回 
   - 过期时间：两年
   - 时区： UTC
   - 权限：读取、写入和列出

   ![显示共享访问签名属性的屏幕截图](media/azure-stack-automatic-log-collection/sas-properties.png) 

1. 单击“创建”。  

复制 URL 并在[配置自动日志收集](azure-stack-configure-automatic-diagnostic-log-collection.md)时输入该 URL。 有关 SAS Url 的详细信息，请参阅[使用共享访问签名（SAS）](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)。 


## <a name="steps-to-configure-automatic-log-collection"></a>配置自动日志收集的步骤

按照以下步骤将 SAS URL 添加到日志收集 UI： 

1. 登录到 Azure Stack 中心管理员门户。
1. 打开 "**帮助和支持概述**"。
1. 单击 "**自动收集设置**"。

   ![屏幕截图显示在 "帮助和支持" 中启用日志收集的位置](media/azure-stack-automatic-log-collection/azure-stack-automatic-log-collection.png)

1. 将 "自动日志收集" 设置为 "**已启用**"。
1. 输入存储帐户 blob 容器的共享访问签名（SAS） URL。

   ![屏幕截图显示 blob SAS URL](media/azure-stack-automatic-log-collection/azure-stack-enable-automatic-log-collection.png)

>[!NOTE]
>可随时禁用和重新启用自动日志收集。 SAS URL 配置不会更改。 如果重新启用了自动日志收集，则之前输入的 SAS URL 将进行相同的验证检查，并且将拒绝过期的 SAS URL。 

## <a name="view-log-collection"></a>查看日志收集

"帮助和支持" 中的 "**日志收集**" 页面上会显示从 Azure Stack 中心收集的日志历史记录，其日期和时间如下：

- **收集时间**：日志收集操作开始时 
- 开始**日期**：要收集的时间段的开始时间
- 结束**日期**：时间段结束

![屏幕截图显示日志集合](media/azure-stack-automatic-log-collection/azure-stack-log-collection.png)

如果诊断日志收集失败，请验证 SAS URL 是否有效。 如果故障仍然存在或者出现多个故障，请调用 Microsoft CSS 以获得帮助。 

操作员还可以在存储帐户中检查自动收集的日志。 例如，此屏幕截图使用 Azure 门户中的存储资源管理器预览显示日志集合：

![屏幕截图显示日志集合](media/azure-stack-automatic-log-collection/check-storage-account.png)

## <a name="automatic-diagnostic-log-collection-alerts"></a>自动诊断日志收集警报 

如果启用，则仅在需要时才会进行自动诊断日志收集。 仅下表触发器集合中的警报。 

例如， **Update failed**是触发自动诊断日志收集的警报。 如果启用了自动收集，则会在更新失败期间主动捕获诊断日志，以帮助 CSS 对问题进行故障排除。 仅在引发**Update 失败**警报时才收集诊断日志。 

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
|文件共享已使用超过80% |    FaultType... #。 |       
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


## <a name="see-also"></a>另请参阅

[Azure Stack 中心日志和客户数据处理](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

[使用共享访问签名 (SAS)](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

[自动 Azure Stack 中心日志收集的最佳实践](azure-stack-best-practices-automatic-diagnostic-log-collection.md)





