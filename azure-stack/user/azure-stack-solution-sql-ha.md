---
title: 将 SQL Server 2016 可用性组部署到 Azure 和 Azure Stack |Microsoft Docs
description: 了解如何将 SQL Server 2016 可用性组部署到 Azure 和 Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 39b0078345c16b7931f41cd2394476f8258d92dd
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308867"
---
# <a name="tutorial-deploy-a-sql-server-2016-availability-group-to-azure-and-azure-stack"></a>教程：将 SQL Server 2016 可用性组部署到 Azure 和 Azure Stack

本文将逐步引导您基本高可用性 (HA) SQL Server 2016 Enterprise 群集与异步灾难恢复 (DR) 站点的自动部署在两个 Azure Stack 环境。 若要了解有关 SQL Server 2016 和高可用性的详细信息，请参阅[Always On 可用性组： 高可用性和灾难恢复解决方案](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2016)。

在本教程中，我们将构建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 协调跨两个 Azure stack 订阅的部署
> - 使用 Docker 来最小化使用 Azure API 配置文件的依赖项问题
> - 部署灾难恢复站点使用基本的高度可用的 SQL Server 2016 Enterprise 群集

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack 是 Azure 的扩展。 Azure Stack 将的敏捷性和创新的云计算到您的本地环境，并且实现唯一的混合云，可用于生成和部署混合应用任意位置。  
> 
> 本白皮书[混合应用程序的设计注意事项](https://aka.ms/hybrid-cloud-applications-pillars)回顾的软件质量 （放置、 可伸缩性、 可用性、 复原能力、 可管理性和安全性） 进行设计、 部署和操作的支柱混合应用程序。 这些设计注意事项有助于优化混合应用程序设计，从而最大限度地减少生产环境中的难题。

## <a name="architecture-for-sql-server-2016"></a>适用于 SQL Server 2016 的体系结构

![SQL Server 2016 SQL HA Azure Stack](media/azure-stack-solution-sql-ha/image1.png)

## <a name="prerequisites-for-sql-server-2016"></a>SQL Server 2016 的先决条件

  - 两个已连接的 Azure Stack 集成系统 (Azure Stack)，此部署在 Azure Stack 开发工具包 (ASDKs) 不能。 若要了解有关 Azure Stack 的详细信息，请参阅[什么是 Azure Stack？](https://azure.microsoft.com/overview/azure-stack/)。
  - 每个 Azure Stack 上的租户订阅。    
      - **记下每个 Azure Stack 的每个订阅 ID 和 Azure 资源管理器终结点。**
  - 每个 Azure Stack 具有权限的租户订阅 Azure Active Directory (Azure AD) 服务主体。 可能需要创建两个服务主体，如果针对不同的 Azure AD 部署 Azure stack 订阅的租户。 若要了解如何创建适用于 Azure Stack 的服务主体，请参阅[创建服务主体以授予应用程序访问 Azure Stack 资源](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals)。
      - **记下每个服务主体的应用程序 ID、 客户端机密和租户名称 (xxxxx.onmicrosoft.com)。**
  - SQL Server 2016 Enterprise 融入到每个 Azure Stack Marketplace。 若要了解有关 marketplace 联合的详细信息，请参阅[从 Azure marketplace 项下载到 Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item)。
    **请确保你的组织具有相应的 SQL 许可证。**
  - [适用于 Windows 的 docker](https://docs.docker.com/docker-for-windows/)在本地计算机上安装。

## <a name="get-the-docker-image"></a>获取 Docker 映像

每个部署的 docker 映像消除的 Azure PowerShell 的不同版本之间的依赖项问题。

1.  请确保 Docker 的 Windows 使用 Windows 容器。
2.  在提升的命令提示符才能获得的部署脚本在 Docker 容器中运行以下命令。

```powershell  
 docker pull intelligentedge/sqlserver2016-hadr:1.0.0
```

## <a name="deploy-the-availability-group"></a>部署可用性组

1.  已成功拉取容器映像后, 启动映像。

      ```powershell  
      docker run -it intelligentedge/sqlserver2016-hadr:1.0.0 powershell
      ```

2.  启动容器后，将为您提供的提升的 PowerShell 终端在容器中。 更改目录，获得的部署脚本。

      ```powershell  
      cd .\SQLHADRDemo\
      ```

3.  运行部署。 提供凭据和资源名称在需要时。 高可用性是指与 HA 群集将在其中部署，Azure Stack 和灾难恢复到 Azure Stack 将在其中部署灾难恢复群集。

      ```powershell
      > .\Deploy-AzureResourceGroup.ps1 `
      -AzureStackApplicationId_HA "applicationIDforHAServicePrincipal" `
      -AzureStackApplicationSercet_HA "clientSecretforHAServicePrincipal" `
      -AADTenantName_HA "hatenantname.onmicrosoft.com" `
      -AzureStackResourceGroup_HA "haresourcegroupname" `
      -AzureStackArmEndpoint_HA "https://management.haazurestack.com" `
      -AzureStackSubscriptionId_HA "haSubscriptionId" `
      -AzureStackApplicationId_DR "applicationIDforDRServicePrincipal" `
      -AzureStackApplicationSercet_DR "ClientSecretforDRServicePrincipal" `
      -AADTenantName_DR "drtenantname.onmicrosoft.com" `
      -AzureStackResourceGroup_DR "drresourcegroupname" `
      -AzureStackArmEndpoint_DR "https://management.drazurestack.com" `
      -AzureStackSubscriptionId_DR "drSubscriptionId"
      ```

4.  类型`Y`使 NuGet 提供程序安装，这将启动要安装的 API 配置文件"2018年-03-01-混合"模块。

5.  等待资源部署完成。

6.  完成 DR 资源部署后，退出该容器。

      ```powershell
      exit
      ```

7.  检查部署，方法是在每个 Azure Stack 门户中查看资源。 连接到其中一个 SQL 实例上的高可用性环境并检查可用性组通过 SQL Server Management Studio (SSMS)。

![SQL Server 2016 SQL HA](media/azure-stack-solution-sql-ha/image2.png)

## <a name="next-steps"></a>后续步骤

  - 使用 SQL Server Management Studio 手动故障转移群集，请参阅[执行强制手动故障转移的 Alwayson 可用性组 (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-2017)
  - 了解有关混合云应用程序的详细信息，请参阅[混合云解决方案。](https://aka.ms/azsdevtutorials)
  - 使用你自己的数据或修改此示例的代码上[GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns)。
