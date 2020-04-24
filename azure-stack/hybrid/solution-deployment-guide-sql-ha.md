---
title: 将 SQL Server 2016 可用性组部署到 Azure 和 Azure Stack Hub
description: 了解如何将 SQL Server 2016 可用性组部署到 Azure 和 Azure Stack Hub
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 90eb2c2c6dac0c4a1dde16b05192b9188b63c709
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "77700657"
---
# <a name="deploy-a-sql-server-2016-availability-group-to-azure-and-azure-stack-hub"></a>将 SQL Server 2016 可用性组部署到 Azure 和 Azure Stack Hub

本文逐步指导你跨两个 Azure Stack Hub 环境，通过异步的灾难恢复 (DR) 站点自动部署基本的高度可用 (HA) SQL Server 2016 Enterprise 群集。 有关 SQL Server 2016 和高可用性的详细信息，请参阅 [Always On 可用性组：高可用性和灾难恢复解决方案](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2016)。

在此解决方案中，你将构建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 跨两个 Azure Stack Hub 对部署进行协调
> - 使用 Docker 尽量减少使用 Azure API 配置文件时出现的依赖项问题
> - 通过灾难恢复站点部署基本的高可用性 SQL Server 2016 Enterprise 群集

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack 中心是 Azure 的扩展。 Azure Stack Hub 将云计算的灵活性和创新性带入你的本地环境，并支持唯一的混合云，以允许你在任何地方构建和部署混合应用。  
> 
> [混合应用程序的设计注意事项](overview-app-design-considerations.md)一文回顾了设计、部署和运行混合应用程序所需的软件质量要素（位置、可伸缩性、可用性、复原能力、可管理性和安全性）。 这些设计注意事项有助于优化混合应用设计，从而最大限度地减少生产环境中的难题。

## <a name="architecture-for-sql-server-2016"></a>2016 SQL Server 的体系结构

![SQL Server 2016 SQL HA Azure Stack Hub](media/solution-deployment-guide-sql-ha/image1.png)

## <a name="prerequisites-for-sql-server-2016"></a>SQL Server 2016 的先决条件

  - 两个连接的 Azure Stack Hub 集成系统 (Azure Stack Hub)，此部署不能在 Azure Stack 开发工具包 (ASDK) 上使用。 若要详细了解 Azure Stack Hub，请参阅[什么是 Azure Stack Hub？](https://azure.microsoft.com/overview/azure-stack/)。
  - 每个 Azure Stack Hub 上有一个租户订阅。    
      - **记下每个订阅 ID 以及每个 Azure Stack Hub 的 Azure 资源管理器终结点。**
  - 一个 Azure Active Directory (Azure AD) 服务主体，该主体有权访问每个 Azure Stack Hub 上的租户订阅。 如果 Azure Stack Hub 是针对不同 Azure AD 租户部署的，则可能需要创建两个服务主体。 若要了解如何为 Azure Stack Hub 创建服务主体，请参阅[创建为应用程序提供 Azure Stack Hub 资源访问权限的服务主体](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals)。
      - **记下每个服务主体的应用程序 ID、客户端机密和租户名称（xxxxx.onmicrosoft.com）。**
  - SQL Server 2016 Enterprise 已联合到 Azure Stack Hub 的每个市场。 若要详细了解市场联合，请参阅[将市场项从 Azure 下载到 Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item)。
    **请确保你的组织具有适当的 SQL 许可证。**
  - 在本地计算机上安装[用于 Windows 的 Docker](https://docs.docker.com/docker-for-windows/)。

## <a name="get-the-docker-image"></a>获取 Docker 映像

适用于每个部署的 Docker 映像消除了不同版 Azure PowerShell 之间出现的依赖项问题。

1.  确保用于 Windows 的 Docker 使用 Windows 容器。
2.  在提升的命令提示符下运行以下脚本，获取包含部署脚本的 Docker 容器。

```powershell  
 docker pull intelligentedge/sqlserver2016-hadr:1.0.0
```

## <a name="deploy-the-availability-group"></a>部署可用性组

1.  成功拉取容器映像以后，即可启动映像。

      ```powershell  
      docker run -it intelligentedge/sqlserver2016-hadr:1.0.0 powershell
      ```

2.  容器启动以后，系统会在容器中为你提供提升的 PowerShell 终端。 将目录切换到部署脚本的位置。

      ```powershell  
      cd .\SQLHADRDemo\
      ```

3.  运行此部署。 根据需要提供凭据和资源名称。 HA 是指将要在其中部署 HA 群集的 Azure Stack Hub，而 DR 是指将要在其中部署 DR 群集的 Azure Stack Hub。

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

4.  键入 `Y` 以安装 NuGet 提供程序，然后就会安装 API 配置文件“2018-03-01-hybrid”模块。

5.  等待资源部署完成。

6.  等 DR 资源部署完成后，退出容器。

      ```powershell
      exit
      ```

7.  查看每个 Azure Stack Hub 门户中的资源以检查部署。 连接到 HA 环境中的某个 SQL 实例，并通过 SQL Server Management Studio (SSMS) 检查可用性组。

![SQL Server 2016 SQL HA](media/solution-deployment-guide-sql-ha/image2.png)

## <a name="next-steps"></a>后续步骤

  - 使用 SQL Server Management Studio 手动故障转移群集，具体请参阅[执行 Always On 可用性组 (SQL Server) 的强制手动故障转移](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-2017)
  - 若要详细了解混合云应用程序，请参阅[混合云解决方案](https://aka.ms/azsdevtutorials)。
  - 使用自己的数据，或修改 [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) 上此示例的代码。
