---
title: 将 SQL Server 2016 可用性组部署到 Azure 和 Azure Stack 中心
description: 了解如何将 SQL Server 2016 可用性组部署到 Azure 和 Azure Stack 中心
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 90eb2c2c6dac0c4a1dde16b05192b9188b63c709
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700657"
---
# <a name="deploy-a-sql-server-2016-availability-group-to-azure-and-azure-stack-hub"></a>将 SQL Server 2016 可用性组部署到 Azure 和 Azure Stack 中心

本文将指导你完成跨两个 Azure Stack 中心环境的异步灾难恢复（DR）站点的基本高可用性（HA） SQL Server 2016 企业群集的自动部署。 若要详细了解 SQL Server 2016 和高可用性，请参阅[Always On 可用性组：高可用性和灾难恢复解决方案](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2016)。

在此解决方案中，您将构建一个示例环境，以：

> [!div class="checklist"]
> - 跨两个 Azure Stack 中心协调部署
> - 使用 Docker 来最大程度地减少 Azure API 配置文件的依赖项问题
> - 使用灾难恢复站点部署基本的高可用性 SQL Server 2016 企业群集

> [!Tip]  
> ![hybrid-pillars](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack 中心是 Azure 的扩展。 Azure Stack 中心为本地环境带来了云计算的灵活性和革新，使你能够在任何位置构建和部署混合应用，从而实现了唯一的混合云。  
> 
> [混合应用程序的设计注意事项](overview-app-design-considerations.md)查看软件质量的支柱（放置、可伸缩性、可用性、复原能力、可管理性和安全性），以便设计、部署和操作混合应用程序。 设计注意事项有助于优化混合应用设计，并最大程度减少生产环境中的挑战。

## <a name="architecture-for-sql-server-2016"></a>2016 SQL Server 的体系结构

![SQL Server 2016 SQL HA Azure Stack 中心](media/solution-deployment-guide-sql-ha/image1.png)

## <a name="prerequisites-for-sql-server-2016"></a>SQL Server 2016 的先决条件

  - 两个连接 Azure Stack 集线器集成系统（Azure Stack 集线器），此部署不适用于 Azure Stack 开发工具包（ASDKs）。 若要详细了解 Azure Stack 中心，请参阅[什么是 Azure Stack 中心？](https://azure.microsoft.com/overview/azure-stack/)。
  - 每个 Azure Stack 中心上的租户订阅。    
      - **记下每个 Azure Stack 中心的每个订阅 ID 和 Azure 资源管理器终结点。**
  - 对每个 Azure Stack 中心拥有租户订阅权限的 Azure Active Directory （Azure AD）服务主体。 如果针对不同的 Azure AD 租户部署 Azure Stack 集线器，则可能需要创建两个服务主体。 若要了解如何为 Azure Stack 中心创建服务主体，请参阅[创建服务主体，以使应用程序能够访问 Azure Stack 中心资源](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals)。
      - **记下每个服务主体的应用程序 ID、客户端机密和租户名称（xxxxx.onmicrosoft.com）。**
  - SQL Server 2016 企业与每个 Azure Stack 中心的 Marketplace 联合。 若要了解有关 marketplace 供稿的详细信息，请参阅[将 marketplace 项从 Azure 下载到 Azure Stack 中心](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item)。
    **请确保你的组织具有适当的 SQL 许可证。**
  - [用于 Windows 的 Docker](https://docs.docker.com/docker-for-windows/)安装在本地计算机上。

## <a name="get-the-docker-image"></a>获取 Docker 映像

每个部署的 Docker 映像消除了不同版本的 Azure PowerShell 之间的依赖关系问题。

1.  请确保用于 Windows 的 Docker 使用 Windows 容器。
2.  在提升的命令提示符下运行以下脚本，以获取包含部署脚本的 Docker 容器。

```powershell  
 docker pull intelligentedge/sqlserver2016-hadr:1.0.0
```

## <a name="deploy-the-availability-group"></a>部署可用性组

1.  成功拉取容器映像后，启动映像。

      ```powershell  
      docker run -it intelligentedge/sqlserver2016-hadr:1.0.0 powershell
      ```

2.  容器启动后，会在容器中提供提升的 PowerShell 终端。 更改目录以获取部署脚本。

      ```powershell  
      cd .\SQLHADRDemo\
      ```

3.  运行部署。 根据需要提供凭据和资源名称。 HA 是指将在其中部署 HA 群集的 Azure Stack 中心，并 DR 到将在其中部署 DR 群集的 Azure Stack 中心。

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

4.  键入 "`Y`" 以允许安装 NuGet 提供程序，这将启动要安装的 API 配置文件 "2018-03-01-混合" 模块。

5.  等待资源部署完成。

6.  灾难恢复资源部署完成后，退出容器。

      ```powershell
      exit
      ```

7.  通过查看每个 Azure Stack 中心门户中的资源来检查部署。 连接到 HA 环境上的一个 SQL 实例，并通过 SQL Server Management Studio （SSMS）检查可用性组。

![SQL Server 2016 SQL HA](media/solution-deployment-guide-sql-ha/image2.png)

## <a name="next-steps"></a>后续步骤

  - 使用 SQL Server Management Studio 手动故障转移群集，请参阅[执行 Always On 可用性组的强制手动故障转移（SQL Server）](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-2017)
  - 了解有关混合云应用程序的详细信息，请参阅[混合云解决方案。](https://aka.ms/azsdevtutorials)
  - 使用自己的数据，或在[GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns)上将代码修改为此示例。
