---
title: 将高度可用的 MongoDB 解决方案部署到 Azure 和 Azure Stack |Microsoft Docs
description: 了解如何将高度可用的 MongoDB 解决方案部署到 Azure 和 Azure Stack
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
ms.openlocfilehash: 77d0b7c856e594e7e97ad30085419bfac13bd4f7
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308887"
---
# <a name="tutorial-deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack"></a>教程：将高度可用的 MongoDB 解决方案部署到 Azure 和 Azure Stack

本文将逐步引导您的灾难恢复 (DR) 站点使用基本的高可用性 (HA) MongoDB 群集的自动部署在两个 Azure Stack 环境。 若要了解有关 MongoDB 和高可用性的详细信息，请参阅[副本集成员](https://docs.mongodb.com/manual/core/replica-set-members/)。

在本教程中，需创建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 协调跨两个 Azure stack 订阅的部署
> - 使用 Docker 来最小化使用 Azure API 配置文件的依赖项问题
> - 部署具有灾难恢复站点高度可用的基本 MongoDB 群集


> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack 是 Azure 的扩展。 Azure Stack 将的敏捷性和创新的云计算到您的本地环境，并且实现唯一的混合云，可用于生成和部署混合应用任意位置。  
> 
> 本白皮书[混合应用程序的设计注意事项](https://aka.ms/hybrid-cloud-applications-pillars)回顾的软件质量 （放置、 可伸缩性、 可用性、 复原能力、 可管理性和安全性） 进行设计、 部署和操作的支柱混合应用程序。 这些设计注意事项有助于优化混合应用程序设计，从而最大限度地减少生产环境中的难题。



## <a name="architecture-for-mongodb-with-azure-stack"></a>通过 Azure Stack 的 mongodb 的体系结构

![在 Azure Stack 中的高可用 MongoDB](media/azure-stack-solution-mongdb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack"></a>通过 Azure Stack 的 MongoDB 的先决条件

  - 两个已连接的 Azure Stack 集成系统 (Azure Stack)，此部署在 Azure Stack 开发工具包 (ASDKs) 不能。 若要了解有关 Azure Stack 的详细信息，请参阅[什么是 Azure Stack？](https://azure.microsoft.com/overview/azure-stack/)
      - 每个 Azure Stack 上的租户订阅。    
      - **记下每个 Azure Stack 的每个订阅 ID 和 Azure 资源管理器终结点。**
  - 每个 Azure Stack 具有权限的租户订阅 Azure Active Directory (Azure AD) 服务主体。 可能需要创建两个服务主体，如果针对不同的 Azure AD 部署 Azure stack 订阅的租户。 若要了解如何创建适用于 Azure Stack 的服务主体，请参阅[创建服务主体以授予应用程序访问 Azure Stack 资源](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals)。    
      - **记下每个服务主体的应用程序 ID、 客户端机密和租户名称 (xxxxx.onmicrosoft.com)。**
  - Ubuntu 16.04 融入到每个 Azure Stack Marketplace。 若要了解有关 marketplace 联合的详细信息，请参阅[从 Azure marketplace 项下载到 Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item)。
  - [适用于 Windows 的 docker](https://docs.docker.com/docker-for-windows/)在本地计算机上安装。

## <a name="get-the-docker-image"></a>获取 Docker 映像

每个部署的 docker 映像消除的 Azure PowerShell 的不同版本之间的依赖项问题。
1.  请确保 Docker 的 Windows 使用 Windows 容器。
2.  在提升的命令提示符才能获得的部署脚本在 Docker 容器中运行以下命令。
```powershell  
docker pull intelligentedge/mongodb-hadr:1.0.0
```

## <a name="deploy-the-clusters"></a>部署群集

1.  已成功拉取容器映像后, 启动映像。 \

    ```powershell  
    docker run -it intelligentedge/mongodb-hadr:1.0.0 powershell
    ```

2.  启动容器后，将为您提供的提升的 PowerShell 终端在容器中。 更改目录，获得的部署脚本。

    ```powershell  
    cd .\MongoHADRDemo\
    ```

3.  运行部署。 提供凭据和资源名称在需要时。 高可用性是指与 HA 群集将在其中部署，Azure Stack 和灾难恢复到 Azure Stack 将在其中部署灾难恢复群集。

    ```powershell
    .\Deploy-AzureResourceGroup.ps1 `
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

5.  将首先部署高可用性资源。 监视部署并等待它完成。 后，消息指示 HA 部署完成，可以检查在 HA Azure Stack 的门户，查看部署的资源。 

6.  继续进行灾难恢复资源的部署，并且决定是否想要启用的跳转盒在 DR Azure Stack，以与群集交互。

7.  等待 DR 资源部署完成。

8.  完成 DR 资源部署后，退出该容器。

  ```powershell
  exit
  ```

## <a name="next-steps"></a>后续步骤

  - 如果您启用了跳转盒 VM 灾难恢复 Azure Stack 上，可以通过 SSH 连接，并通过安装 mongo CLI 与 MongoDB 群集进行交互。 若要了解有关与 MongoDB 交互的详细信息，请参阅[mongo Shell](https://docs.mongodb.com/manual/mongo/)。

  - 了解有关混合云应用程序的详细信息，请参阅[混合云解决方案。](https://aka.ms/azsdevtutorials)

  - 上修改此示例代码[GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns)。