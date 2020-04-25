---
title: 将高度可用的 MongoDB 解决方案部署到 Azure 和 Azure Stack Hub
description: 了解如何将高度可用的 MongoDB 解决方案部署到 Azure 和 Azure Stack Hub
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 1d73f4099c1bf5923f646cfee74f332d1492d771
ms.sourcegitcommit: b185ab34c4c799892948536dd6d1d1b2fc31174e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82150315"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack-hub"></a>将高度可用的 MongoDB 解决方案部署到 Azure 和 Azure Stack Hub

本文详细介绍如何跨两个 Azure Stack Hub 环境通过灾难恢复 (DR) 站点来自动部署基本的高度可用 (HA) MongoDB 群集。 若要详细了解 MongoDB 和高可用性，请参阅 [Replica Set Members](https://docs.mongodb.com/manual/core/replica-set-members/)（副本集成员）。

在此解决方案中，你将创建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 跨两个 Azure Stack 中心协调部署。
> - 使用 Docker 来最大程度地减少 Azure API 配置文件的依赖项问题。
> - 使用灾难恢复站点部署基本的高可用性 MongoDB 群集。

> [!Tip]  
> ![hybrid-pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Microsoft Azure Stack 中心是 Azure 的扩展。 Azure Stack 中心为本地环境带来了云计算的灵活性和创新，实现了唯一的混合云，使你能够在任何位置构建和部署混合应用。  
> 
> [混合应用设计注意事项](overview-app-design-considerations.md)一文介绍了用于设计、部署和操作混合应用的软件质量（放置、可伸缩性、可用性、复原能力、可管理性和安全性）的支柱。 这些设计注意事项有助于优化混合应用设计，从而最大限度地减少生产环境中的难题。

## <a name="architecture-for-mongodb-with-azure-stack-hub"></a>Azure Stack Hub 中的 MongoDB 的体系结构

![Azure Stack 中心内高度可用的 MongoDB 体系结构](media/solution-deployment-guide-mongodb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack-hub"></a>将 MongoDB 与 Azure Stack Hub 配合使用的先决条件

- 两个连接 Azure Stack 集线器集成系统（Azure Stack 中心）。 此部署不适用于 Azure Stack 开发工具包（ASDK）。 若要详细了解 Azure Stack 中心，请参阅[什么是 Azure Stack 中心？](https://azure.microsoft.com/products/azure-stack/hub/)
  - 每个 Azure Stack Hub 上有一个租户订阅。 
  - **记下每个订阅 ID 以及每个 Azure Stack Hub 的 Azure 资源管理器终结点。**
- 一个 Azure Active Directory (Azure AD) 服务主体，该主体有权访问每个 Azure Stack Hub 上的租户订阅。 如果 Azure Stack Hub 是针对不同 Azure AD 租户部署的，则可能需要创建两个服务主体。 若要了解如何为 Azure Stack 中心创建服务主体，请参阅[使用应用标识访问 Azure Stack 中心资源](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals)。
  - **记下每个服务主体的应用程序 ID、客户端机密和租户名称（xxxxx.onmicrosoft.com）。**
- 与每个 Azure Stack Hub 市场联合的 Ubuntu 16.04。 若要了解有关 marketplace 供稿的详细信息，请参阅[将 marketplace 项下载到 Azure Stack 中心](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item)。
- 在本地计算机上安装[用于 Windows 的 Docker](https://docs.docker.com/docker-for-windows/)。

## <a name="get-the-docker-image"></a>获取 Docker 映像

适用于每个部署的 Docker 映像消除了不同版 Azure PowerShell 之间出现的依赖项问题。

1. 确保用于 Windows 的 Docker 使用 Windows 容器。
2. 在提升的命令提示符下运行以下命令，以获取包含部署脚本的 Docker 容器。

    ```powershell  
    docker pull intelligentedge/mongodb-hadr:1.0.0
    ```

## <a name="deploy-the-clusters"></a>部署群集

1. 成功拉取容器映像以后，即可启动映像。

    ```powershell  
    docker run -it intelligentedge/mongodb-hadr:1.0.0 powershell
    ```

2. 容器启动以后，系统会在容器中为你提供提升的 PowerShell 终端。 将目录切换到部署脚本的位置。

    ```powershell  
    cd .\MongoHADRDemo\
    ```

3. 运行此部署。 根据需要提供凭据和资源名称。 HA 是指将在其中部署 HA 群集的 Azure Stack 中心。 DR 是指将在其中部署 DR 群集的 Azure Stack 中心。

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

4. 键入 `Y` 以安装 NuGet 提供程序，然后就会安装 API 配置文件“2018-03-01-hybrid”模块。

5. HA 资源会先部署。 监视部署并等待其完成。 收到指出 HA 部署已完成的消息后，可以查看 HA Azure Stack 集线器的门户，以查看部署的资源。

6. 继续部署 DR 资源，决定是否要在 DR Azure Stack Hub 上启用与群集交互所需的跳转盒。

7. 等待 DR 资源部署完成。

8. 灾难恢复资源部署完成后，退出容器。

  ```powershell
  exit
  ```

## <a name="next-steps"></a>后续步骤

- 如果在 DR Azure Stack Hub 上启用了跳转盒，则可通过 SSH 进行连接，并通过安装 mongo CLI 与 MongoDB 群集进行交互。 若要详细了解如何与 MongoDB 交互，请参阅 [The mongo Shell](https://docs.mongodb.com/manual/mongo/)（mongo Shell）。
- 若要了解有关混合云应用的详细信息，请参阅[混合云解决方案。](https://aka.ms/azsdevtutorials)
- 在 [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) 上修改此示例的代码。
