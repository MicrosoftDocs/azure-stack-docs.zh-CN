---
title: 将分步数据分析解决方案部署到 Azure Stack |Microsoft Docs
description: 了解如何将分步数据分析解决方案部署到 Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.topic: conceptual
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/24/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 4d93e16a46efad33c4392e40ca3a79e89a14795b
ms.sourcegitcommit: e6a738f674634e1d5dd4eb23b6c44b660ea2fe84
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72891274"
---
# <a name="deploy-a-staged-data-analytics-solution-to-azure-stack"></a>将分步数据分析解决方案部署到 Azure Stack

本文介绍如何部署用于收集数据的解决方案，该解决方案需要在集合中进行分析，以便可以进行快速决策。 通常，此数据收集在没有 Internet 访问的情况下发生。 建立连接后，你可能需要对数据进行资源密集型分析以获得更多见解。

在此解决方案中，你将创建一个示例环境来执行以下操作：

> [!div class="checklist"]
> - 创建原始数据存储 blob。
> - 创建新的 Azure Stack 函数，将清理数据从 Azure Stack 移到 Azure。
> - 创建 Blob 存储触发的函数。
> - 创建包含 blob 和队列的 Azure Stack 存储帐户。
> - 创建队列触发的函数。
> - 测试队列触发的函数。

> [!Tip]  
> ![hybrid-pillars](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack 是 Azure 的扩展。 Azure Stack 将云计算的灵活性和创新带入本地环境，从而实现了唯一的混合云，使你能够在任何位置构建和部署混合应用。  
> 
> [混合应用程序的设计注意事项](azure-stack-edge-pattern-overview.md)查看软件质量的支柱（放置、可伸缩性、可用性、复原能力、可管理性和安全性），以便设计、部署和操作混合应用程序。 设计注意事项有助于优化混合应用设计，并最大程度减少生产环境中的挑战。

## <a name="architecture-for-staged-data-analytics"></a>过渡数据分析体系结构

![过渡数据分析](media/azure-stack-solution-staged-data/image1.png)

## <a name="prerequisites-for-staged-data-analytics"></a>暂存数据分析的先决条件

  - Azure 订阅。
  - 有权访问 Azure 上的租户订阅并 Azure Stack 的 Azure Active Directory （Azure AD）服务主体。 如果 Azure Stack 使用的 AAD 租户不同于你的 Azure 订阅，则可能需要创建两个服务主体。 若要了解如何创建 Azure Stack 的服务主体，请参阅[创建服务主体，为应用程序提供 Azure Stack 资源的访问权限](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals)。
      - **记下每个服务主体的应用程序 ID、客户端机密、Azure AD 租户 ID 和租户名称（xxxxx.onmicrosoft.com）。**
  - 需要提供数据集合以进行数据分析。 提供示例数据。
  - [用于 Windows 的 Docker](https://docs.docker.com/docker-for-windows/)安装在本地计算机上。

## <a name="get-the-docker-image"></a>获取 Docker 映像

每个部署的 Docker 映像消除了不同版本的 Azure PowerShell 之间的依赖关系问题。
1.  请确保用于 Windows 的 Docker 使用 Windows 容器。
2.  在提升的命令提示符下运行以下命令，以获取包含部署脚本的 Docker 容器。

```
 docker pull intelligentedge/stageddatasolution:1.0.0
```

## <a name="deploy-the-solution"></a>部署解决方案

1.  成功拉取容器映像后，启动映像。

      ```powershell  
      docker run -it intelligentedge/stageddatasolution:1.0.0 powershell
      ```

2.  容器启动后，会在容器中提供提升的 PowerShell 终端。 更改目录以获取部署脚本。

      ```powershell  
      cd .\SDDemo\
      ```

3.  运行部署。 根据需要提供凭据和资源名称。 HA 是指将在其中部署 HA 群集的 Azure Stack，并 DR 到将在其中部署 DR 群集的 Azure Stack。

      ```powershell
      .\DeploySolution-Azure-AzureStack.ps1 `
      -AzureApplicationId "applicationIDforAzureServicePrincipal" `
      -AzureApplicationSecret "clientSecretforServicePrincipal" `
      -AzureTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -AzureStackAADTenantName "azurestacktenant.onmicrosoft.com" `
      -AzureStackTenantARMEndpoint "https://management.haazurestack.com" `
      -AzureStackApplicationId "applicationIDforStackServicePrincipal" `
      -AzureStackApplicationSecret "ClientSecretforStackServicePrincipal" `
      -AzureStackTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -ResourcePrefix "aPrefixForResources"
      ```

1.  如果系统提示，输入 Azure 部署的区域并 Application Insights。

2.  键入 "Y" 以允许安装 NuGet 提供程序，这将启动要安装的 API 配置文件 "2018-03-01-混合" 模块，以允许部署到 Azure 并 Azure Stack。

3.  部署资源后，请测试是否将为 Azure Stack 和 Azure 生成数据。

    ```powershell  
      .\TDAGenerator.exe
    ```

4.  查看正在处理的数据，方法是转到部署到 Azure 的 web 应用程序或 Azure Stack。

### <a name="azure-web-app"></a>Azure Web 应用
 
![过渡数据分析解决方案](media/azure-stack-solution-staged-data/image2.png)
 
### <a name="azure-stack-web-app"></a>Azure Stack Web 应用
 
![Azure Stack 的分步数据分析解决方案](media/azure-stack-solution-staged-data/image3.png)

## <a name="next-steps"></a>后续步骤

  - 了解有关混合云应用程序的详细信息，请参阅[混合云解决方案。](https://aka.ms/azsdevtutorials)

  - 使用自己的数据，或在[GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns)上将代码修改为此示例。
