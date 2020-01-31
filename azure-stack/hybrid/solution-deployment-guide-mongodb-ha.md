---
title: 将高度可用的 MongoDB 解决方案部署到 Azure 并 Azure Stack 中心
description: 了解如何将高度可用的 MongoDB 解决方案部署到 Azure 并 Azure Stack 中心
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 88cad7fc3f9ac4d213cd16f6e690876b1e0faa4d
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76876994"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack-hub"></a>将高度可用的 MongoDB 解决方案部署到 Azure 并 Azure Stack 中心

本文将指导你完成跨两个 Azure Stack 中心环境的灾难恢复（DR）站点的基本高可用性（HA） MongoDB 群集的自动部署。 若要了解有关 MongoDB 和高可用性的详细信息，请参阅[副本集成员](https://docs.mongodb.com/manual/core/replica-set-members/)。

在此解决方案中，你将创建一个示例环境来执行以下操作：

> [!div class="checklist"]
> - 跨两个 Azure Stack 中心协调部署
> - 使用 Docker 来最大程度地减少 Azure API 配置文件的依赖项问题
> - 使用灾难恢复站点部署基本的高可用性 MongoDB 群集


> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack 中心是 Azure 的扩展。 Azure Stack 中心为本地环境带来了云计算的灵活性和革新，使你能够在任何位置构建和部署混合应用，从而实现了唯一的混合云。  
> 
> [混合应用程序的设计注意事项](overview-app-design-considerations.md)查看软件质量的支柱（放置、可伸缩性、可用性、复原能力、可管理性和安全性），以便设计、部署和操作混合应用程序。 设计注意事项有助于优化混合应用设计，并最大程度减少生产环境中的挑战。



## <a name="architecture-for-mongodb-with-azure-stack-hub"></a>适用于 MongoDB 与 Azure Stack 中心的体系结构

![Azure Stack 中心的高可用性 MongoDB](media/solution-deployment-guide-mongodb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack-hub"></a>Azure Stack 中心的 MongoDB 必备组件

  - 两个连接 Azure Stack 集线器集成系统（Azure Stack 集线器），此部署不适用于 Azure Stack 集线器开发工具包（ASDKs）。 若要详细了解 Azure Stack 中心，请参阅[什么是 Azure Stack 中心？](https://azure.microsoft.com/overview/azure-stack/)
      - 每个 Azure Stack 中心上的租户订阅。    
      - **记下每个 Azure Stack 中心的每个订阅 ID 和 Azure 资源管理器终结点。**
  - 对每个 Azure Stack 中心拥有租户订阅权限的 Azure Active Directory （Azure AD）服务主体。 如果针对不同的 Azure AD 租户部署 Azure Stack 集线器，则可能需要创建两个服务主体。 若要了解如何为 Azure Stack 中心创建服务主体，请参阅[创建服务主体，以使应用程序能够访问 Azure Stack 中心资源](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals)。    
      - **记下每个服务主体的应用程序 ID、客户端机密和租户名称（xxxxx.onmicrosoft.com）。**
  - Ubuntu 16.04 与每个 Azure Stack 集线器的 Marketplace 联合。 若要了解有关 marketplace 供稿的详细信息，请参阅[将 marketplace 项从 Azure 下载到 Azure Stack 中心](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item)。
  - [用于 Windows 的 Docker](https://docs.docker.com/docker-for-windows/)安装在本地计算机上。

## <a name="get-the-docker-image"></a>获取 Docker 映像

每个部署的 Docker 映像消除了不同版本的 Azure PowerShell 之间的依赖关系问题。
1.  请确保用于 Windows 的 Docker 使用 Windows 容器。
2.  在提升的命令提示符下运行以下命令，以获取包含部署脚本的 Docker 容器。
```powershell  
docker pull intelligentedge/mongodb-hadr:1.0.0
```

## <a name="deploy-the-clusters"></a>部署群集

1.  成功拉取容器映像后，启动映像。

    ```powershell  
    docker run -it intelligentedge/mongodb-hadr:1.0.0 powershell
    ```

2.  容器启动后，会在容器中提供提升的 PowerShell 终端。 更改目录以获取部署脚本。

    ```powershell  
    cd .\MongoHADRDemo\
    ```

3.  运行部署。 根据需要提供凭据和资源名称。 HA 是指将在其中部署 HA 群集的 Azure Stack 中心，并 DR 到将在其中部署 DR 群集的 Azure Stack 中心。

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

4.  键入 "`Y`" 以允许安装 NuGet 提供程序，这将启动要安装的 API 配置文件 "2018-03-01-混合" 模块。

5.  将首先部署 HA 资源。 监视部署并等待其完成。 收到指出 HA 部署已完成的消息后，可以查看 HA Azure Stack 集线器的门户，以查看部署的资源。 

6.  继续部署 DR 资源，并决定是否要在 DR Azure Stack 集线器上启用跳转框，以便与群集进行交互。

7.  等待 DR 资源部署完成。

8.  灾难恢复资源部署完成后，退出容器。

  ```powershell
  exit
  ```

## <a name="next-steps"></a>后续步骤

  - 如果在 DR Azure Stack 集线器上启用跳转盒 VM，则可以通过 SSH 进行连接并通过安装 mongo CLI 与 MongoDB 群集进行交互。 若要详细了解如何与 MongoDB 交互，请参阅[Mongo Shell](https://docs.mongodb.com/manual/mongo/)。

  - 了解有关混合云应用程序的详细信息，请参阅[混合云解决方案。](https://aka.ms/azsdevtutorials)

  - 在[GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns)上，将代码修改为此示例。
