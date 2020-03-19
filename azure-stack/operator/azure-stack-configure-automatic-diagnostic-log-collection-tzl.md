---
title: 在 Azure Stack 集线器上发出警报时，主动收集诊断日志
description: 如何在 Azure Stack 中心帮助和支持中配置主动日志收集。
author: justinha
ms.topic: article
ms.date: 02/12/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/12/2020
ms.openlocfilehash: 0c6bfe4dba2b3795e1069419d90624c8e3b55cda
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520261"
---
# <a name="send-azure-stack-hub-diagnostic-logs-proactively"></a>主动发送 Azure Stack 集线器诊断日志

可以通过在 Azure Stack 集线器上发出警报时，主动收集诊断日志来节省时间。
如果需要调查系统运行状况条件，则可以在使用 Microsoft 客户支持服务（CSS）打开支持案例之前，自动上载日志以进行分析。 

## <a name="steps-to-configure-proactive-log-collection"></a>配置主动日志收集的步骤

按照以下步骤配置主动日志收集。 可随时禁用和重新启用自动日志收集。  

1. 登录到 Azure Stack 中心管理员门户。
1. 打开 "**帮助和支持概述**"。
1. 如果出现横幅，请单击 "**启用主动日志收集**"。 

   ![屏幕截图显示在 "帮助和支持" 中启用日志收集的位置](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)


   或者，您可以单击 "**设置**" 并将 "**主动日志收集**" 设置为**启用**，然后单击 "**保存**"。

   ![屏幕截图显示在 "帮助和支持" 中启用日志收集的位置](media/azure-stack-help-and-support/settings-enable-automatic-log-collection.png)


## <a name="view-log-collection"></a>查看日志收集

"帮助和支持" 中的 "**日志收集**" 页面上会显示从 Azure Stack 中心收集的日志历史记录，其日期和时间如下：

- **收集的时间**：日志收集操作开始时间
- **状态**： "正在进行" 或 "已完成"
- **日志开始**：要收集的时间段的开始时间
- **日志结束**时间：时间段结束
- **类型**：如果是手动或主动日志收集 


![屏幕截图显示日志集合](media/azure-stack-help-and-support/azure-stack-log-collection.png)


## <a name="proactive-diagnostic-log-collection-alerts"></a>主动诊断日志收集警报 

如果已启用，则只有当引发以下事件之一时，主动日志收集才会上载日志。 

例如， **Update failed**是触发前瞻性诊断日志收集的警报。 如果启用了此功能，则会在更新失败时主动捕获诊断日志，以帮助 CSS 对问题进行故障排除。 仅在引发**Update 失败**警报时才收集诊断日志。 

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

[Azure Stack 中心日志和客户数据处理](azure-stack-data-collection.md)





