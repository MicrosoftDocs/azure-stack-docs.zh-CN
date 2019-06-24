---
title: 将临时的数据分析解决方案部署到 Azure Stack |Microsoft Docs
description: 了解如何将临时的数据分析解决方案部署到 Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/20/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 7148e93977f50a7c64d79c422c43c6825b22b4a3
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308956"
---
# <a name="tutorial-deploy-a-staged-data-analytics-solution-to-azure-stack"></a>教程：将临时的数据分析解决方案部署到 Azure Stack

本文将演示如何部署用于收集数据，以便可以进行快速决定执行哪些将需要在集合时进行分析的解决方案。 这种数据收集通常是在没有 Internet 访问的情况下进行的。 建立连接后，可能需要对数据进行资源密集型分析以获取更多的见解。

在本教程中，需创建一个示例环境来完成以下任务：

> [!div class="checklist"]
> - 创建原始数据存储 Blob。
> - 创建新的 Azure Stack 函数，将纯净数据从 Azure Stack 移到 Azure。
> - 创建 Blob 存储触发的函数。
> - 创建包含 Blob 和队列的 Azure Stack 存储帐户。
> - 创建队列触发的函数。
> - 测试队列触发的函数。

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack 是 Azure 的扩展。 Azure Stack 将的敏捷性和创新的云计算到您的本地环境，并且实现唯一的混合云，可用于生成和部署混合应用任意位置。  
> 
> 本白皮书[混合应用程序的设计注意事项](https://aka.ms/hybrid-cloud-applications-pillars)回顾的软件质量 （放置、 可伸缩性、 可用性、 复原能力、 可管理性和安全性） 进行设计、 部署和操作的支柱混合应用程序。 这些设计注意事项有助于优化混合应用程序设计，从而最大限度地减少生产环境中的难题。

## <a name="architecture-for-staged-data-analytics"></a>临时的数据分析的体系结构

![临时的数据分析](media/azure-stack-solution-staged-data/image1.png)

## <a name="prerequisites-for-staged-data-analytics"></a>临时的数据分析的先决条件

  - Azure 订阅。
  - 一个有权在 Azure 和 Azure Stack 上的租户订阅的 Azure Active Directory (AAD) 服务主体。 您可能需要创建两个服务主体，如果 Azure Stack 使用比你的 Azure 订阅不同的 AAD 租户。 若要了解如何创建适用于 Azure Stack 的服务主体，请转[创建服务主体以授予应用程序访问 Azure Stack 资源](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals)。
      - **记下每个服务主体的应用程序 ID、 客户端机密、 Azure AD 租户 ID 和租户名称 (xxxxx.onmicrosoft.com)。**
  - 您需要为数据分析工具提供的数据集合。 提供了示例数据。
  - [适用于 Windows 的 docker](https://docs.docker.com/docker-for-windows/)在本地计算机上安装。

## <a name="get-the-docker-image"></a>获取 Docker 映像

每个部署的 docker 映像消除的 Azure PowerShell 的不同版本之间的依赖项问题。
1.  请确保 Docker 的 Windows 使用 Windows 容器。
2.  在提升的命令提示符才能获得的部署脚本在 Docker 容器中运行以下命令。

```
 docker pull intelligentedge/stageddatasolution:1.0.0
```

## <a name="deploy-the-solution"></a>部署解决方案

1.  已成功拉取容器映像后, 启动映像。

      ```powershell  
      docker run -it intelligentedge/stageddatasolution:1.0.0 powershell
      ```

2.  启动容器后，将为您提供的提升的 PowerShell 终端在容器中。 更改目录，获得的部署脚本。

      ```powershell  
      cd .\SDDemo\
      ```

3.  运行部署。 提供凭据和资源名称在需要时。 高可用性是指与 HA 群集将在其中部署，Azure Stack 和灾难恢复到 Azure Stack 将在其中部署灾难恢复群集。

      ```powershell
      .\DeploySolution-Azure-AzureStack.ps1 `
      -AzureApplicationId "applicationIDforAzureServicePrincipal" `
      -AzureApplicationSercet "clientSecretforServicePrincipal" `
      -AzureTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -AzureStackAADTenantName "azurestacktenant.onmicrosoft.com" `
      -AzureStackTenantARMEndpoint "https://management.haazurestack.com" `
      -AzureStackApplicationId "applicationIDforStackServicePrincipal" `
      -AzureStackApplicationSercet "ClientSecretforStackServicePrincipal" `
      -AzureStackTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -ResourcePrefix "aPrefixForResources"
      ```

1.  如果系统提示;输入 Azure 部署和 Application Insights 的区域。

2.  键入"Y"以允许 NuGet 提供程序来安装，这将启动安装以允许进行到 Azure 和 Azure Stack 部署的 API 配置文件"2018年-03-01-混合"模块。

3.  部署资源后，测试将生成 Azure Stack 和 Azure 的数据。

    ```powershell  
      .\TDAGenerator.exe
    ```

4.  请参阅处理通过转到 web 应用程序部署到 Azure 或 Azure Stack 中的数据。

### <a name="azure-web-app"></a>Azure Web 应用
 
![临时的数据分析解决方案](media/azure-stack-solution-staged-data/image2.png)
 
### <a name="azure-stack-web-app"></a>Azure Stack Web 应用
 
![Azure Stack 适用的临时的数据分析解决方案](media/azure-stack-solution-staged-data/image3.png)

## <a name="next-steps"></a>后续步骤

  - 了解有关混合云应用程序的详细信息，请参阅[混合云解决方案。](https://aka.ms/azsdevtutorials)

  - 使用你自己的数据或修改此示例的代码上[GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns)。
