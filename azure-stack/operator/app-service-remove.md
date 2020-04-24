---
title: 从 Azure Stack 中心删除 Azure App Service
description: 了解如何从 Azure Stack 中心删除 Azure App Service
author: apwestgarth
ms.topic: article
ms.date: 04/17/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/17/20207
ms.openlocfilehash: b371b319ae87bb112633b64d77d66f8916a13124
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81636468"
---
# <a name="remove-azure-app-service-from-azure-stack-hub"></a>从 Azure Stack 中心删除 Azure App Service

本文介绍如何从 Azure Stack 中心删除 Azure App Service 资源提供程序和相关组件。

## <a name="remove-resource-provider"></a>删除资源提供程序

> [!Important]
> 此操作将删除所有租户资源，从所有计划中删除服务和配额，并删除其中的 Azure App Service 资源提供程序。  如果已部署应用服务高可用文件服务器和 SQL Server 快速入门模板，则还会删除这些资源，因为它们与 Azure Stack Hub 上的 Azure App Service 部署在同一资源组中。

若要从 Azure Stack 中心删除 Azure App Service，请执行以下步骤：

1. 删除保存 Azure Stack 中心资源 Azure App Service 的资源组，例如 AppService

## <a name="remove-databases-and-file-share-content"></a>删除数据库和文件共享内容

仅当你的 SQL Server 和/或文件服务器部署在标记之外或其他资源组中时，才需要遵循此部分，否则请转到下一节。

### <a name="remove-databases-and-logins"></a>删除数据库和登录名

1. 如果使用**SQL Server Always On**，请从可用性组中删除**AppService_Hosting**和**AppService_Metering**数据库：

1. 执行以下 SQL 脚本以删除数据库和登录名

   ```sql
   --******************************************************************
   /*
   Script to clean up App Service objects (databases and logins).
   */
   USE [master]
   GO

   DROP DATABASE [appservice_hosting]
   GO

   DROP DATABASE [appservice_metering]
   GO

   DECLARE @sql NVARCHAR(MAX) = N'';    
 
   SELECT @sql += '
   DROP LOGIN [' + name + '];' 
   from master.sys.sql_logins
   WHERE name LIKE  '%_hosting_%' OR 
   name LIKE  '%_metering_%' OR
   name LIKE  '%WebWorker_%';

   PRINT @sql;
   EXEC sp_executesql @sql;
   PRINT 'Completed';

   --******************************************************************
   ```

### <a name="remove-the-application-file-content-from-the-file-server"></a>从文件服务器中删除应用程序文件内容

1. 从文件服务器中删除 "内容文件共享"。

## <a name="next-steps"></a>后续步骤

若要重新安装，请返回到在[Azure Stack Hub 上部署应用服务的先决条件](azure-stack-app-service-before-you-get-started.md)一文。
